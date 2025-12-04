# Windows Cross-Compilation and Release Workflow

This workflow (`windows-release.yml`) automatically builds Yosys for Windows (64-bit) using cross-compilation on Ubuntu and creates a GitHub release with the compiled binaries.

## Overview

The workflow:
1. Cross-compiles Yosys for Windows using MinGW-w64 on Ubuntu
2. Packages the executable and required files into a ZIP archive
3. Uploads the package as a GitHub artifact
4. Creates a GitHub release with the Windows binaries

## Triggering the Workflow

### Automatic Trigger (Push Tags)

The workflow automatically runs when you push a tag starting with `v` (e.g., `v0.1.0`, `v1.2.3`):

```bash
git tag v0.1.0
git push origin v0.1.0
```

### Manual Trigger (Workflow Dispatch)

You can also trigger the workflow manually from the GitHub Actions tab:

1. Go to the repository's "Actions" tab
2. Select "Windows Cross-Compile and Release" from the workflows list
3. Click "Run workflow"
4. Enter a tag name (e.g., `v0.1.0`)
5. Click "Run workflow"

## Build Configuration

The workflow uses the following configuration:

- **Compiler**: MinGW-w64 cross-compiler (x86_64-w64-mingw32-g++)
- **Target**: Windows 64-bit
- **Features**:
  - TCL: Enabled
  - ABC: Enabled
  - GLOB: Enabled
  - ZLIB: Enabled
  - COVER: Enabled
  - PLUGINS: Disabled (cross-compilation limitation)
  - READLINE: Disabled (Windows compatibility)

## Output

The workflow produces:

1. **GitHub Artifact**: `yosys-windows-<version>.zip`
   - Retained for 90 days
   - Available in the workflow run page

2. **GitHub Release**: Created automatically with:
   - Release tag matching the version
   - Release notes describing the build
   - Attached ZIP file with Windows binaries

## Package Contents

The `yosys-win64.zip` file contains:

- `yosys.exe` - Main Yosys executable
- `yosys-*.exe` - Additional Yosys utilities
- `share/` - Required shared data files
- `README.txt` - Basic usage instructions

## Usage for End Users

After downloading the release:

1. Extract `yosys-win64.zip` to a directory
2. Run `yosys.exe` from the extracted directory
3. Optionally, add the directory to your PATH for easier access

Example:
```cmd
cd yosys-win64
yosys.exe -p "read_verilog mydesign.v; synth; write_verilog output.v"
```

## Troubleshooting

### Build Failures

If the build fails:

1. Check the workflow logs in the GitHub Actions tab
2. Verify that all submodules are properly initialized
3. Ensure the Makefile configuration is compatible with MinGW

### Missing Dependencies

The workflow installs:
- mingw-w64 (cross-compiler)
- bison, flex (parser generators)
- libffi-dev, pkg-config (build tools)
- tcl-dev, zlib1g-dev (optional libraries)

### Release Creation Issues

If release creation fails:
- Ensure the tag doesn't already exist
- Check that GITHUB_TOKEN has proper permissions
- Verify the repository settings allow workflow to create releases

## Customization

To modify the build configuration:

1. Edit the `Configure for Windows cross-compilation` step
2. Adjust the `Makefile.conf` settings
3. Refer to the main Makefile for available options

To change the package contents:

1. Edit the `Prepare release package` step
2. Modify the file copying commands
3. Update the README.txt content as needed

## Related Workflows

- `test-build.yml` - Regular Linux/macOS builds and tests
- `extra-builds.yml` - Additional build configurations (Visual Studio, WASI, Nix)
- `wheels.yml` - Python wheel builds for PyPI

## Notes

- This workflow uses cross-compilation, which may have limitations compared to native Windows builds
- Plugins are disabled because cross-compilation of dynamic libraries can be complex
- The workflow creates releases automatically; consider using draft releases for testing
