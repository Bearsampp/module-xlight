# Build Guide - Bearsampp Module Xlight

## Prerequisites

### Required Software
- **Java Development Kit (JDK)** 8 or higher
- **Gradle** 7.0 or higher (or use Gradle wrapper)
- **7-Zip** (for .7z archive support)

### Optional Software
- **Git** (for version control)
- **IDE** with Gradle support (IntelliJ IDEA, Eclipse, VS Code)

## Quick Start

### 1. Verify Environment
```bash
gradle verify
```

This checks:
- Java version (8+)
- Required files (build.properties, releases.properties)
- Directory structure (bin/)

### 2. List Available Versions
```bash
gradle listVersions
```

Shows all versions available in `bin/` and `bin/archived/` directories.

### 3. Build Release (Interactive)
```bash
gradle release
```

This will:
1. Prompt you to select a version
2. Download Xlight binaries if needed
3. Process dependencies
4. Create archive with hash files
5. Display success message

### 4. Build Release (Non-Interactive)
```bash
gradle release -PbundleVersion=3.9.4.6
```

Or build the latest version:
```bash
gradle release -PbundleVersion=*
```

## Detailed Build Process

### Step 1: Version Resolution

The build system supports three modes:

#### Interactive Mode
```bash
gradle release
```

You'll see a menu like:
```
======================================================================
Available xlight versions (index, version, location):
----------------------------------------------------------------------
   1. 3.9.4.2       [bin/archived]
   2. 3.9.4.3       [bin/archived]
   3. 3.9.4.4       [bin/archived]
   4. 3.9.4.5       [bin/archived]
   5. 3.9.4.6       [bin]
----------------------------------------------------------------------

Enter version to build (index or version string):
```

Enter either:
- Index number (e.g., `5`)
- Version string (e.g., `3.9.4.6`)

#### Non-Interactive Mode
```bash
gradle release -PbundleVersion=3.9.4.6
```

#### Latest Version Mode
```bash
gradle release -PbundleVersion=*
```

### Step 2: Binary Download

If Xlight binaries are not found in `bin/<bundleName><version>/`, the build system will:

1. Check cache in `bearsampp-build/tmp/extract/xlight/<version>/`
2. If not cached, download from modules-untouched repository
3. Extract to cache directory
4. Use for build

**Note:** Downloaded files are cached to avoid re-downloading.

### Step 3: Preparation

The build system:

1. Creates preparation directory: `bearsampp-build/tmp/bundles_prep/bins/xlight/<bundleName><version>/`
2. Copies Xlight binaries (excluding extras.properties)
3. Copies configuration files from `bin/<bundleName><version>/`
4. Processes extras.properties dependencies

### Step 4: Dependency Processing

If `extras.properties` exists in the version folder, the build system:

1. Downloads each dependency (help, remote, language packs)
2. Extracts archives
3. Copies files to preparation directory

Example `extras.properties`:
```properties
help = https://github.com/Bearsampp/modules-untouched/releases/download/xlight-2024.12.8/xlight_help.zip
remote = https://github.com/Bearsampp/modules-untouched/releases/download/xlight-2024.12.8/remote_admin.zip
language = https://github.com/Bearsampp/modules-untouched/releases/download/xlight-2024.12.8/language_en.zip
```

### Step 5: Archive Creation

The build system creates an archive based on `bundle.format` in build.properties:

#### 7z Format (Default)
```bash
# Creates: bearsampp-xlight-3.9.4.6-2025.7.31.7z
```

Archive structure:
```
bearsampp-xlight-3.9.4.6-2025.7.31.7z
└── xlight3.9.4.6/
    ├── xlight.exe
    ├── bearsampp.conf
    ├── ftpd.hosts
    ├── ftpd.rules
    ├── ftpd.users
    ├── ftpd.option
    ├── ftpd.password
    ├── Logs/
    └── ... (other files)
```

#### Zip Format
Change `bundle.format` in build.properties:
```properties
bundle.format = zip
```

### Step 6: Hash Generation

The build system generates hash files:
- `.md5` - MD5 hash
- `.sha1` - SHA-1 hash
- `.sha256` - SHA-256 hash
- `.sha512` - SHA-512 hash

Example:
```
bearsampp-xlight-3.9.4.6-2025.7.31.7z
bearsampp-xlight-3.9.4.6-2025.7.31.7z.md5
bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha1
bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha256
bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha512
```

## Build Output

### Directory Structure

```
bearsampp-build/
├── tmp/
│   ├── bundles_prep/
│   │   └── bins/
│   │       └── xlight/
│   │           └── xlight3.9.4.6/          # Prepared files
│   ├── bundles_build/
│   │   └── bins/
│   │       └── xlight/
│   │           └── xlight3.9.4.6/          # Build output
│   ├── downloads/
│   │   └── xlight/                         # Downloaded files (cached)
│   └── extract/
│       └── xlight/                         # Extracted archives (cached)
└── bins/
    └── xlight/
        └── 2025.7.31/                      # Final release archives
            ├── bearsampp-xlight-3.9.4.6-2025.7.31.7z
            ├── bearsampp-xlight-3.9.4.6-2025.7.31.7z.md5
            ├── bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha1
            ├── bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha256
            └── bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha512
```

### Success Message

```
======================================================================
[SUCCESS] Release build completed successfully for version 3.9.4.6
Output directory: E:\Bearsampp-development\bearsampp-build\tmp\bundles_build\bins\xlight\xlight3.9.4.6
Archive: E:\Bearsampp-development\bearsampp-build\bins\xlight\2025.7.31\bearsampp-xlight-3.9.4.6-2025.7.31.7z
======================================================================
```

## Configuration

### build.properties

```properties
# Bundle identification
bundle.name = xlight
bundle.release = 2025.7.31
bundle.type = bins
bundle.format = 7z

# Optional: Override default build path
# Default: <project-root>/../bearsampp-build
#build.path = C:/bearsampp-build
```

### Environment Variables

#### BEARSAMPP_BUILD_PATH
Override the default build path:
```bash
set BEARSAMPP_BUILD_PATH=C:/custom-build-path
gradle release -PbundleVersion=3.9.4.6
```

#### 7Z_HOME
Specify 7-Zip installation directory:
```bash
set 7Z_HOME=C:/Program Files/7-Zip
gradle release -PbundleVersion=3.9.4.6
```

## Advanced Usage

### Clean Build
```bash
gradle clean
gradle release -PbundleVersion=3.9.4.6
```

### Validate Configuration
```bash
gradle validateProperties
```

### List All Tasks
```bash
gradle tasks
```

### Display Build Information
```bash
gradle info
```

### List Dependencies
```bash
gradle listDependencies
```

### Xlight-Specific Information
```bash
gradle xlightInfo
```

## Troubleshooting

### Build Fails with "7-Zip not found"

**Solution 1:** Install 7-Zip
1. Download from https://www.7-zip.org/
2. Install to default location
3. Retry build

**Solution 2:** Set 7Z_HOME
```bash
set 7Z_HOME=C:/Program Files/7-Zip
gradle release -PbundleVersion=3.9.4.6
```

### Build Fails with "Version not found"

**Check available versions:**
```bash
gradle listVersions
```

**Verify folder structure:**
```
bin/
├── xlight3.9.4.6/
│   ├── bearsampp.conf
│   ├── extras.properties
│   └── ... (config files)
└── archived/
    └── xlight3.9.4.2/
        └── ... (older versions)
```

### Download Fails

**Check internet connection:**
```bash
ping github.com
```

**Manual download:**
1. Check modules-untouched repository
2. Download Xlight binaries manually
3. Extract to `bin/xlight<version>/`
4. Retry build

### Build Path Issues

**Check build path:**
```bash
gradle info
```

Look for "Build Base" path in output.

**Override build path:**
```bash
# In build.properties
build.path = C:/bearsampp-build

# Or via environment variable
set BEARSAMPP_BUILD_PATH=C:/bearsampp-build
```

## Performance Tips

### 1. Use Gradle Daemon
The Gradle daemon is enabled by default in `gradle.properties`:
```properties
org.gradle.daemon=true
```

### 2. Enable Parallel Execution
```properties
org.gradle.parallel=true
```

### 3. Use Build Cache
```properties
org.gradle.caching=true
```

### 4. Increase Memory
```properties
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

## CI/CD Integration

### GitHub Actions Example

```yaml
name: Build Xlight Module

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: windows-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'
    
    - name: Install 7-Zip
      run: choco install 7zip -y
    
    - name: Build with Gradle
      run: gradle release -PbundleVersion=*
    
    - name: Upload artifacts
      uses: actions/upload-artifact@v3
      with:
        name: xlight-release
        path: ../bearsampp-build/bins/xlight/**/*
```

## Best Practices

1. **Always verify environment first:**
   ```bash
   gradle verify
   ```

2. **Use version control for bin/ configurations:**
   - Commit `bin/<version>/` folders
   - Include extras.properties
   - Document version-specific changes

3. **Test builds locally before CI/CD:**
   ```bash
   gradle clean
   gradle release -PbundleVersion=3.9.4.6
   ```

4. **Keep build.properties up to date:**
   - Update `bundle.release` for new releases
   - Document changes in version control

5. **Cache downloads:**
   - The build system caches downloads automatically
   - Don't delete `bearsampp-build/tmp/downloads/` unnecessarily

## Support

For build issues:
1. Check this guide
2. Run `gradle tasks --all` to see all available tasks
3. Run `gradle info` to see build configuration
4. Check build.gradle for implementation details
5. Review error messages carefully
