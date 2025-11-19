# Bearsampp Module Xlight - Gradle Build System

## 🎉 Pure Gradle Build - Conversion Complete!

This module has been successfully converted from Ant to a **pure Gradle build system**.

## Quick Start

### 1. Verify Your Environment
```bash
gradle verify
```

### 2. Build a Release
```bash
# Interactive mode (prompts for version)
gradle release

# Non-interactive mode (specify version)
gradle release -PbundleVersion=3.9.4.6

# Build latest version
gradle release -PbundleVersion=*
```

### 3. View Available Tasks
```bash
gradle tasks
```

## Key Features

✅ **Pure Gradle** - No Ant dependencies  
✅ **Interactive Mode** - User-friendly version selection  
✅ **Non-Interactive Mode** - Perfect for CI/CD  
✅ **Centralized Build Paths** - All outputs in bearsampp-build/  
✅ **Download Caching** - Faster subsequent builds  
✅ **Hash Generation** - MD5, SHA1, SHA256, SHA512  
✅ **Comprehensive Documentation** - See `.gradle-docs/`

## Common Commands

```bash
# Display build information
gradle info

# List available versions
gradle listVersions

# List dependencies
gradle listDependencies

# Validate configuration
gradle validateProperties

# Clean build artifacts
gradle clean

# Verify environment
gradle verify
```

## Build Output

Archives are created in:
```
bearsampp-build/bins/xlight/2025.7.31/
├── bearsampp-xlight-3.9.4.6-2025.7.31.7z
├── bearsampp-xlight-3.9.4.6-2025.7.31.7z.md5
├── bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha1
├── bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha256
└── bearsampp-xlight-3.9.4.6-2025.7.31.7z.sha512
```

## Success Message

When the build completes successfully, you'll see:

```
======================================================================
[SUCCESS] Release build completed successfully for version 3.9.4.6
Output directory: E:\Bearsampp-development\bearsampp-build\tmp\bundles_build\bins\xlight\xlight3.9.4.6
Archive: E:\Bearsampp-development\bearsampp-build\bins\xlight\2025.7.31\bearsampp-xlight-3.9.4.6-2025.7.31.7z
======================================================================
```

## Documentation

Comprehensive documentation is available in `.gradle-docs/`:

- **[README.md](.gradle-docs/README.md)** - Documentation index
- **[GRADLE_MIGRATION.md](.gradle-docs/GRADLE_MIGRATION.md)** - Migration guide
- **[BUILD_GUIDE.md](.gradle-docs/BUILD_GUIDE.md)** - Complete build guide
- **[TASK_REFERENCE.md](.gradle-docs/TASK_REFERENCE.md)** - Task reference
- **[CONVERSION_SUMMARY.md](.gradle-docs/CONVERSION_SUMMARY.md)** - Conversion details

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
- `7Z_HOME` - Path to 7-Zip installation

## Requirements

- **Java** 8 or higher
- **Gradle** 7.0 or higher
- **7-Zip** (for .7z archive support)

## Troubleshooting

### 7-Zip Not Found
Install 7-Zip from https://www.7-zip.org/ or set `7Z_HOME` environment variable.

### Version Not Found
Run `gradle listVersions` to see available versions.

### Build Fails
1. Run `gradle verify` to check environment
2. Check `.gradle-docs/BUILD_GUIDE.md` for troubleshooting
3. Review error messages carefully

## CI/CD Integration

### GitHub Actions Example
```yaml
- name: Build Xlight Module
  run: gradle release -PbundleVersion=*
```

### Jenkins Example
```groovy
stage('Build') {
    steps {
        bat 'gradle release -PbundleVersion=*'
    }
}
```

## What Changed from Ant

### Removed
- ❌ `build.xml` - Ant build file
- ❌ Ant dependencies and imports
- ❌ References to dev/build/ files

### Added
- ✅ `build.gradle` - Pure Gradle build (1,200+ lines)
- ✅ `.gradle-docs/` - Comprehensive documentation
- ✅ Interactive version selection
- ✅ Enhanced error handling
- ✅ Better caching and performance

### Preserved
- ✅ All original functionality
- ✅ Same archive format and structure
- ✅ Same configuration files
- ✅ Same output paths

## Support

For detailed information:
1. Check `.gradle-docs/` documentation
2. Run `gradle tasks` to see available tasks
3. Run `gradle info` to see configuration
4. Review `build.gradle` for implementation details

## License

See [LICENSE](LICENSE) file in the project root.

---

**Build System:** Pure Gradle  
**Version:** 2025.7.31  
**Status:** ✅ Production Ready

For more information, see the comprehensive documentation in `.gradle-docs/`
