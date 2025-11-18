# Task Reference - Bearsampp Module Xlight

## Overview

This document provides a comprehensive reference for all Gradle tasks available in the Bearsampp Module Xlight build system.

## Task Groups

- **build** - Build and package tasks
- **help** - Help and information tasks
- **verification** - Verification and validation tasks

## Build Tasks

### `release`
**Group:** build  
**Description:** Build release package (interactive by default; -PbundleVersion=* or X.Y.Z for non-interactive)

**Usage:**
```bash
# Interactive mode (prompts for version)
gradle release

# Non-interactive mode (specify version)
gradle release -PbundleVersion=3.9.4.6

# Build latest version
gradle release -PbundleVersion=*
```

**Dependencies:**
- `clean` - Cleans build artifacts first
- `resolveVersion` - Resolves the version to build
- `packageRelease` - Packages the release

**Finalized By:**
- `generateHashes` - Generates hash files
- `cleanupTempFiles` - Cleans temporary files

**Output:**
- Archive: `bearsampp-build/bins/xlight/<release>/bearsampp-xlight-<version>-<release>.7z`
- Hash files: `.md5`, `.sha1`, `.sha256`, `.sha512`

---

### `clean`
**Group:** build  
**Description:** Clean build artifacts and temporary files

**Usage:**
```bash
gradle clean
```

**What it cleans:**
- `build/` directory
- `bearsampp-build/tmp/` directory
- `.gradle-bundleVersion` temp file

**Output:**
```
Cleaned: E:\Bearsampp-development\module-xlight\build
Cleaned: E:\Bearsampp-development\bearsampp-build\tmp
[SUCCESS] Build artifacts cleaned
```

---

### `resolveVersion`
**Group:** build  
**Description:** Resolve bundleVersion (interactive by default, or use -PbundleVersion=*,<ver>)

**Usage:**
```bash
# Interactive mode
gradle resolveVersion

# Non-interactive mode
gradle resolveVersion -PbundleVersion=3.9.4.6

# Latest version
gradle resolveVersion -PbundleVersion=*
```

**Behavior:**
- **Interactive:** Displays menu of available versions, prompts for selection
- **Non-Interactive:** Uses provided version or resolves latest (*)
- **Validation:** Checks version exists in `bin/` or `bin/archived/`

**Output:**
- Stores resolved version in `bearsampp-build/tmp/.gradle-bundleVersion`
- Displays: `Selected version: <version>`

---

### `releaseBuild`
**Group:** build  
**Description:** Execute the release build process

**Usage:**
```bash
gradle releaseBuild
```

**Dependencies:**
- `resolveVersion` - Must resolve version first

**Process:**
1. Locates bundle configuration in `bin/<bundleName><version>/`
2. Checks for Xlight binaries (xlight.exe)
3. Downloads from modules-untouched if needed
4. Copies base files and configuration
5. Processes extras.properties dependencies
6. Prepares final directory structure

**Output:**
- Prepared bundle: `bearsampp-build/tmp/bundles_prep/bins/xlight/<bundleName><version>/`

---

### `packageRelease`
**Group:** build  
**Description:** Package release into archive (7z or zip) including the version folder at root

**Usage:**
```bash
gradle packageRelease
```

**Dependencies:**
- `resolveVersion` - Resolves version
- `releaseBuild` - Builds release
- `assertVersionResolved` - Validates version

**Delegates To:**
- `packageRelease7z` - If bundle.format = 7z
- `packageReleaseZip` - If bundle.format = zip

**Output:**
- Archive: `bearsampp-build/bins/xlight/<release>/bearsampp-xlight-<version>-<release>.<format>`

---

### `packageRelease7z`
**Group:** build  
**Description:** Package release into a .7z archive (includes version folder at root)

**Usage:**
```bash
gradle packageRelease7z
```

**Dependencies:**
- `assertVersionResolved`
- `releaseBuild`

**Requirements:**
- 7-Zip must be installed
- `7Z_HOME` environment variable (optional)

**Output:**
- Archive: `bearsampp-xlight-<version>-<release>.7z`
- Archive structure includes version folder at root

---

### `packageReleaseZip`
**Group:** build  
**Description:** Package release into a .zip archive (includes version folder at root)

**Usage:**
```bash
gradle packageReleaseZip
```

**Dependencies:**
- `assertVersionResolved`
- `releaseBuild`

**Output:**
- Archive: `bearsampp-xlight-<version>-<release>.zip`
- Archive structure includes version folder at root

---

### `generateHashes`
**Group:** build  
**Description:** Generate hash sidecar files for the packaged archive

**Usage:**
```bash
gradle generateHashes
```

**Generated Files:**
- `.md5` - MD5 hash
- `.sha1` - SHA-1 hash
- `.sha256` - SHA-256 hash
- `.sha512` - SHA-512 hash

**Output:**
```
Created: bearsampp-xlight-3.9.4.6-2025.7.31.7z.md5
Created: bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha1
Created: bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha256
Created: bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha512

======================================================================
[SUCCESS] Release build completed successfully for version 3.9.4.6
Output directory: E:\Bearsampp-development\bearsampp-build\tmp\bundles_build\bins\xlight\xlight3.9.4.6
Archive: E:\Bearsampp-development\bearsampp-build\bins\xlight\2025.7.31\bearsampp-xlight-3.9.4.6-2025.7.31.7z
======================================================================
```

---

### `cleanupTempFiles`
**Group:** build  
**Description:** Cleanup temporary Gradle-specific files after build

**Usage:**
```bash
gradle cleanupTempFiles
```

**Cleans:**
- `.gradle-bundleVersion` - Temporary version storage file

---

### `assertVersionResolved`
**Group:** build  
**Description:** Fail fast if bundleVersion was not resolved by resolveVersion

**Usage:**
```bash
gradle assertVersionResolved
```

**Dependencies:**
- `resolveVersion`

**Purpose:**
- Validates version is resolved before packaging
- Prevents packaging with undefined version

---

## Verification Tasks

### `verify`
**Group:** verification  
**Description:** Verify build environment and dependencies

**Usage:**
```bash
gradle verify
```

**Checks:**
- Java version (8+)
- build.properties exists
- releases.properties exists
- bin/ directory exists

**Output:**
```
Verifying build environment for module-xlight...

Environment Check Results:
------------------------------------------------------------
  [PASS]     Java 8+
  [PASS]     build.properties
  [PASS]     releases.properties
  [PASS]     bin directory
------------------------------------------------------------

[SUCCESS] All checks passed! Build environment is ready.

You can now run:
  gradle release                        - Interactive release
  gradle release -PbundleVersion=3.9.4.6 - Non-interactive release
```

---

### `validateProperties`
**Group:** verification  
**Description:** Validate build.properties configuration

**Usage:**
```bash
gradle validateProperties
```

**Validates:**
- `bundle.name` - Present and non-empty
- `bundle.release` - Present and non-empty
- `bundle.type` - Present and non-empty
- `bundle.format` - Present and non-empty

**Output:**
```
Validating build.properties...
[SUCCESS] All required properties are present:
    bundle.name = xlight
    bundle.release = 2025.7.31
    bundle.type = bins
    bundle.format = 7z
```

---

## Help Tasks

### `info`
**Group:** help  
**Description:** Display build configuration information

**Usage:**
```bash
gradle info
```

**Output:**
```
================================================================
          Bearsampp Module Xlight - Build Info
================================================================

Project:        module-xlight
Version:        2025.7.31
Description:    Bearsampp Module - xlight

Bundle Properties:
  Name:         xlight
  Release:      2025.7.31
  Type:         bins
  Format:       7z

Paths:
  Project Dir:  E:\Bearsampp-development\module-xlight
  Root Dir:     E:\Bearsampp-development
  Dev Path:     E:\Bearsampp-development\dev
  Build Base:   E:\Bearsampp-development\bearsampp-build
  Output Dir:   E:\Bearsampp-development\bearsampp-build\bins\xlight\2025.7.31
  Tmp Root:     E:\Bearsampp-development\bearsampp-build\tmp
  Tmp Prep:     E:\Bearsampp-development\bearsampp-build\tmp\bundles_prep\bins\xlight
  Tmp Build:    E:\Bearsampp-development\bearsampp-build\tmp\bundles_build\bins\xlight
  Tmp Src:      E:\Bearsampp-development\bearsampp-build\tmp\bundles_src
  Downloads:    E:\Bearsampp-development\bearsampp-build\tmp\downloads\xlight
  Extract:      E:\Bearsampp-development\bearsampp-build\tmp\extract\xlight

Java:
  Version:      11.0.12
  Home:         C:\Program Files\Java\jdk-11.0.12

Gradle:
  Version:      7.6
  Home:         C:\Users\user\.gradle\wrapper\dists\gradle-7.6-bin\...

Available Task Groups:
  * build        - Build and package tasks
  * help         - Help and information tasks
  * verification - Verification and validation tasks

Quick Start:
  gradle tasks                          - List all available tasks
  gradle info                           - Show this information
  gradle release                        - Interactive release build
  gradle release -PbundleVersion=3.9.4.6 - Non-interactive release
  gradle clean                          - Clean build artifacts
  gradle verify                         - Verify build environment
```

---

### `tasks`
**Group:** help  
**Description:** Displays the tasks runnable from root project 'module-xlight'

**Usage:**
```bash
# List main tasks
gradle tasks

# List all tasks (including internal)
gradle tasks --all
```

---

### `listVersions`
**Group:** help  
**Description:** List all available bundle versions in bin/ directory

**Usage:**
```bash
gradle listVersions
```

**Output:**
```
Available xlight versions (index, version, location):
------------------------------------------------------------
   1. 3.9.4.2       [bin/archived]
   2. 3.9.4.3       [bin/archived]
   3. 3.9.4.4       [bin/archived]
   4. 3.9.4.5       [bin/archived]
   5. 3.9.4.6       [bin]
------------------------------------------------------------
Total versions: 5

To build a specific version:
  gradle release -PbundleVersion=3.9.4.6
```

---

### `listReleases`
**Group:** help  
**Description:** List all available releases from releases.properties

**Usage:**
```bash
gradle listReleases
```

**Output:**
```
Available Xlight Releases:
--------------------------------------------------------------------------------
  3.9.4.2    -> https://github.com/Bearsampp/module-xlight/releases/download/2024.6.8/bearsampp-xlight-3.9.4.2-2024.6.8.7z
  3.9.4.3    -> https://github.com/Bearsampp/module-xlight/releases/download/2024.9.14/bearsampp-xlight-3.9.4.3-2024.9.17.7z
  3.9.4.4    -> https://github.com/Bearsampp/module-xlight/releases/download/2024.12.8/bearsampp-xlight-3.9.4.4-2024.12.8.7z
  3.9.4.5    -> https://github.com/Bearsampp/module-xlight/releases/download/2025.2.13/bearsampp-xlight-3.9.4.5-2025.2.13.7z
  3.9.4.6    -> https://github.com/Bearsampp/module-xlight/releases/download/2025.7.31/bearsampp-xlight-3.9.4.6-2025.7.31.7z
--------------------------------------------------------------------------------
Total releases: 5
```

---

### `listDependencies`
**Group:** help  
**Description:** List dependencies configured in bin directories

**Usage:**
```bash
gradle listDependencies
```

**Output:**
```
Scanning for dependency configurations...
================================================================================

Xlight 3.9.4.6:
--------------------------------------------------------------------------------
  help                 -> https://github.com/Bearsampp/modules-untouched/releases/download/xlight-2024.12.8/xlight_help.zip
  remote               -> https://github.com/Bearsampp/modules-untouched/releases/download/xlight-2024.12.8/remote_admin.zip
  language             -> https://github.com/Bearsampp/modules-untouched/releases/download/xlight-2024.12.8/language_en.zip
================================================================================
```

---

### `xlightInfo`
**Group:** help  
**Description:** Display Xlight-specific build information

**Usage:**
```bash
gradle xlightInfo
```

**Output:**
```
================================================================
          Xlight Module Build - Specific Information
================================================================

This module includes special build processes for:

1. Xlight FTP Server Binaries
   - Downloads from modules-untouched repository
   - Extracts and prepares Xlight FTP server files

2. Dependencies (extras.properties)
   - Downloads required extras (help, remote admin, language packs)
   - Integrates dependencies into the build

Configuration Files:
  - extras.properties    : Additional components to include

Useful Commands:
  gradle listDependencies   - Show dependencies
  gradle xlightInfo         - Show this information
```

---

## Task Dependencies

### Release Task Flow
```
release
├── clean
├── resolveVersion
└── packageRelease
    ├── resolveVersion
    ├── releaseBuild
    │   └── resolveVersion
    ├── assertVersionResolved
    │   └── resolveVersion
    └── packageRelease7z (or packageReleaseZip)
        ├── assertVersionResolved
        └── releaseBuild

Finalized by:
├── generateHashes
└── cleanupTempFiles
```

## Common Task Combinations

### Full Clean Build
```bash
gradle clean release -PbundleVersion=3.9.4.6
```

### Verify and Build
```bash
gradle verify
gradle release -PbundleVersion=3.9.4.6
```

### List and Build Latest
```bash
gradle listVersions
gradle release -PbundleVersion=*
```

### Validate and Build
```bash
gradle validateProperties
gradle release -PbundleVersion=3.9.4.6
```

## Task Properties

### Project Properties
- `bundleVersion` - Version to build (e.g., `3.9.4.6` or `*`)

**Usage:**
```bash
gradle release -PbundleVersion=3.9.4.6
```

### Environment Variables
- `BEARSAMPP_BUILD_PATH` - Override default build path
- `7Z_HOME` - Path to 7-Zip installation

**Usage:**
```bash
set BEARSAMPP_BUILD_PATH=C:/custom-build
set 7Z_HOME=C:/Program Files/7-Zip
gradle release -PbundleVersion=3.9.4.6
```

## Task Output Locations

### Temporary Files
- **Prep:** `bearsampp-build/tmp/bundles_prep/bins/xlight/<bundleName><version>/`
- **Build:** `bearsampp-build/tmp/bundles_build/bins/xlight/<bundleName><version>/`
- **Downloads:** `bearsampp-build/tmp/downloads/xlight/`
- **Extract:** `bearsampp-build/tmp/extract/xlight/`

### Final Output
- **Archives:** `bearsampp-build/bins/xlight/<release>/`
- **Hash Files:** Same directory as archives

## Error Handling

### Common Errors

#### "bundleVersion property not set"
**Cause:** Version not resolved before packaging  
**Solution:** Run `gradle resolveVersion` first or use `-PbundleVersion`

#### "7-Zip not found"
**Cause:** 7-Zip not installed or not in PATH  
**Solution:** Install 7-Zip or set `7Z_HOME` environment variable

#### "Bundle version not found"
**Cause:** Version doesn't exist in bin/ or bin/archived/  
**Solution:** Check available versions with `gradle listVersions`

#### "Failed to download Xlight binaries"
**Cause:** Network issue or version not in modules-untouched  
**Solution:** Check internet connection or manually download binaries

## Performance Considerations

### Task Caching
- Gradle caches task outputs automatically
- Downloads are cached in `bearsampp-build/tmp/downloads/`
- Extracted files are cached in `bearsampp-build/tmp/extract/`

### Incremental Builds
- `clean` task removes all cached data
- Without `clean`, subsequent builds reuse cached downloads

### Parallel Execution
- Enabled by default in `gradle.properties`
- Multiple independent tasks run in parallel

## Best Practices

1. **Always verify first:**
   ```bash
   gradle verify
   ```

2. **Use clean for fresh builds:**
   ```bash
   gradle clean release -PbundleVersion=3.9.4.6
   ```

3. **Check available versions:**
   ```bash
   gradle listVersions
   ```

4. **Validate configuration:**
   ```bash
   gradle validateProperties
   ```

5. **Use non-interactive mode in CI/CD:**
   ```bash
   gradle release -PbundleVersion=*
   ```
