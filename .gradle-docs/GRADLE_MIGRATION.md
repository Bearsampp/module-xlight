# Gradle Migration Guide - Module Xlight

## Overview

This document describes the migration from Ant to pure Gradle build system for the Bearsampp Module Xlight project.

## Migration Date

**Completed:** November 2025

## What Changed

### Removed
- ❌ `build.xml` - Legacy Ant build file (removed)
- ❌ Ant dependencies and imports
- ❌ `ant-*` prefixed tasks
- ❌ Dependency on `dev/build/build-commons.xml`
- ❌ Dependency on `dev/build/build-bundle.xml`

### Added
- ✅ Pure Gradle build system (`build.gradle`)
- ✅ Native Gradle tasks for all build operations
- ✅ Improved caching and incremental builds
- ✅ Better dependency management
- ✅ Modern build tooling

## Build System Comparison

### Before (Ant)
```bash
ant release -Dinput.bundle=3.9.4.6
```

### After (Gradle)
```bash
# Interactive mode
gradle release

# Non-interactive mode
gradle release -PbundleVersion=3.9.4.6

# Latest version
gradle release -PbundleVersion=*
```

## Key Features

### 1. Version Resolution
- **Interactive Mode**: Prompts user to select version from available options
- **Non-Interactive Mode**: Specify version via `-PbundleVersion=X.Y.Z`
- **Latest Version**: Use `-PbundleVersion=*` to build the latest version

### 2. Build Paths
The build system now uses a centralized external build directory structure:

```
bearsampp-build/
├── tmp/
│   ├── bundles_prep/bins/xlight/     # Preparation directory
│   ├── bundles_build/bins/xlight/    # Build output directory
│   ├── bundles_src/                  # Source cache
│   ├── downloads/xlight/             # Downloaded files
│   └── extract/xlight/               # Extracted archives
└── bins/xlight/2025.7.31/            # Final release archives
```

### 3. Dependency Management
- Downloads Xlight binaries from modules-untouched repository
- Processes extras.properties for additional components
- Caches downloads to avoid re-downloading

### 4. Archive Generation
- Creates 7z or zip archives based on `bundle.format` in build.properties
- Includes version folder at archive root (e.g., `xlight3.9.4.6/`)
- Generates hash files (.md5, .sha1, .sha256, .sha512)

## Task Reference

### Build Tasks
- `gradle release` - Build release package (interactive or with -PbundleVersion)
- `gradle clean` - Clean build artifacts
- `gradle releaseBuild` - Execute release build process
- `gradle packageRelease` - Package release into archive
- `gradle generateHashes` - Generate hash files for archive

### Verification Tasks
- `gradle verify` - Verify build environment
- `gradle validateProperties` - Validate build.properties

### Help Tasks
- `gradle info` - Display build information
- `gradle tasks` - List all available tasks
- `gradle listVersions` - List available versions in bin/
- `gradle listReleases` - List releases from releases.properties
- `gradle listDependencies` - List configured dependencies
- `gradle xlightInfo` - Display Xlight-specific information

## Configuration

### build.properties
```properties
bundle.name = xlight
bundle.release = 2025.7.31
bundle.type = bins
bundle.format = 7z

# Optional: Override default build path
#build.path = C:/bearsampp-build
```

### Environment Variables
- `BEARSAMPP_BUILD_PATH` - Override default build path
- `7Z_HOME` - Path to 7-Zip installation (if not in PATH)

## Build Process Flow

1. **Version Resolution** (`resolveVersion`)
   - Prompts user or uses -PbundleVersion parameter
   - Validates version exists in bin/ or bin/archived/
   - Stores resolved version for subsequent tasks

2. **Release Build** (`releaseBuild`)
   - Locates bundle configuration in bin/
   - Downloads Xlight binaries if needed
   - Copies base files and configuration
   - Processes extras.properties dependencies
   - Prepares final directory structure

3. **Package Release** (`packageRelease`)
   - Creates 7z or zip archive
   - Includes version folder at root
   - Outputs to bearsampp-build/bins/xlight/<release>/

4. **Generate Hashes** (`generateHashes`)
   - Creates MD5, SHA1, SHA256, SHA512 hash files
   - Displays success message with paths

## Success Message Format

```
======================================================================
[SUCCESS] Release build completed successfully for version 3.9.4.6
Output directory: E:\Bearsampp-development\bearsampp-build\tmp\bundles_build\bins\xlight\xlight3.9.4.6
Archive: E:\Bearsampp-development\bearsampp-build\bins\xlight\2025.7.31\bearsampp-xlight-3.9.4.6-2025.7.31.7z
======================================================================
```

## Troubleshooting

### 7-Zip Not Found
**Error:** `7-Zip not found. Cannot extract .7z archive`

**Solution:**
1. Install 7-Zip from https://www.7-zip.org/
2. Or set `7Z_HOME` environment variable to your 7-Zip installation directory

### Version Not Found
**Error:** `Bundle version not found in bin/ or bin/archived/`

**Solution:**
1. Check available versions: `gradle listVersions`
2. Ensure the version folder exists in `bin/` or `bin/archived/`
3. Verify folder naming: `xlight<version>` (e.g., `xlight3.9.4.6`)

### Download Failed
**Error:** `Failed to download Xlight binaries`

**Solution:**
1. Check internet connection
2. Verify version exists in modules-untouched repository
3. Manually download and extract to `bin/<bundleName><version>/`

## Benefits of Gradle Migration

1. **Performance**
   - Incremental builds
   - Build caching
   - Parallel execution

2. **Maintainability**
   - Pure Groovy/Gradle DSL (no XML)
   - Better error messages
   - Easier to extend and customize

3. **Modern Tooling**
   - IDE integration (IntelliJ IDEA, Eclipse, VS Code)
   - Gradle wrapper support
   - Better dependency management

4. **Consistency**
   - Follows Gradle best practices
   - Consistent with other Bearsampp modules
   - Standard task naming conventions

## Migration Checklist

- [x] Convert Ant build.xml to Gradle build.gradle
- [x] Implement all Ant tasks as Gradle tasks
- [x] Test interactive version selection
- [x] Test non-interactive build with -PbundleVersion
- [x] Verify archive creation (7z and zip)
- [x] Verify hash file generation
- [x] Test dependency downloading and extraction
- [x] Verify success message format
- [x] Remove Ant build files
- [x] Create documentation

## Support

For issues or questions about the Gradle build system:
1. Check this documentation
2. Run `gradle tasks` to see available tasks
3. Run `gradle info` to see build configuration
4. Check the build.gradle file for implementation details
