# Multiversion Alternatives Research

## Executive Summary

This document provides a comprehensive analysis of multiversion tooling alternatives for managing multiple Minecraft Fabric versions in the PAPIProxyBridge project. The research focuses on solutions that can support both current versions (1.20.1 through 1.21.8) and future Minecraft releases.

## Current Setup

### Essential Gradle Toolkit (EGT)
- **Version**: 0.6.5
- **Repository**: https://repo.essential.gg/repository/maven-public
- **Gradle Plugins Used**:
  - `gg.essential.multi-version.root` - Root project configuration
  - `gg.essential.multi-version` - Individual version project configuration
  - `gg.essential.defaults` - Default configurations for Loom

### Current Version Support
- Fabric 1.20.1 (Java 17)
- Fabric 1.21.1 (Java 21)
- Fabric 1.21.4 (Java 21)
- Fabric 1.21.5 (Java 21)
- Fabric 1.21.8 (Java 21)

### How It Works
EGT uses a "preprocess" system where:
1. A "main" version is selected (currently 1.21.8 in `fabric/mainProject`)
2. Other versions are linked to the main version in `fabric/root.gradle`
3. Source code is preprocessed using version-specific comments to generate different code for each version
4. Each version builds independently with version-specific dependencies

## Multiversion Alternatives

### 1. Architectury Loom (Standalone)

**Description**: Architectury Loom is an enhanced version of Fabric Loom that provides better multi-loader support, though it can also be used solely for Fabric multiversion.

**Pros**:
- More active development than EGT
- Better compatibility with newer Gradle versions
- Direct support from Architectury team
- Can potentially support multiple loaders (Fabric/Forge/NeoForge) in future
- More granular control over version-specific configurations

**Cons**:
- Would require rewriting the build system
- Less "magic" - more manual configuration needed
- Doesn't have built-in preprocessor (would need manual source sets or Stonecutter)
- Migration effort from current EGT setup

**Compatibility**: 
- Supports Minecraft 1.14+ including all current and future versions
- Requires Gradle 8.1+
- Works with Java 17+ and Java 21+

**Implementation Approach**:
```gradle
// Would require separate source sets for each version
// or integration with another preprocessor
```

### 2. Stonecutter

**Description**: Stonecutter is a modern preprocessor designed specifically for multi-version Minecraft mods. It's actively maintained and used by many large mods.

**Repository**: https://github.com/Stonecutterz/stonecutter-gradle

**Pros**:
- Modern, actively maintained (last update: 2024)
- Specifically designed for Minecraft multiversion
- Works with any build system (Loom, Architectury, etc.)
- Cleaner preprocessing syntax than EGT
- Good documentation and community support
- Can handle both minor and major version differences
- Supports version ranges and version-specific code blocks

**Cons**:
- Would require rewriting preprocessing comments in code
- Migration effort from EGT
- Another dependency to maintain
- Learning curve for new syntax

**Compatibility**:
- Works with any Minecraft version
- Compatible with Fabric Loom 1.0+
- Supports Gradle 7.0+
- Future-proof architecture

**Implementation Example**:
```java
//? if >=1.21 {
/*Modern code for 1.21+*/
//?} else {
Legacy code for 1.20.1
//?}
```

### 3. Essential Gradle Toolkit (Newer Version)

**Description**: Continue using EGT but upgrade to the latest version.

**Latest Version**: 0.6.5 (current), potentially newer versions available

**Pros**:
- Minimal migration effort - just version bump
- Already familiar with the system
- Proven to work with current setup
- All existing preprocessing comments work as-is

**Cons**:
- Less active maintenance compared to alternatives
- Potential deprecation in future
- May have compatibility issues with newer Gradle versions
- Limited community compared to alternatives
- Repository availability concerns (Essential.gg focus may shift)

**Compatibility**:
- Currently supports all needed versions
- Future support uncertain

### 4. Multiple Source Sets (Manual Approach)

**Description**: Use Gradle's native source sets feature to maintain separate source directories for each version.

**Pros**:
- No external dependencies
- Simple to understand
- Full control over version-specific code
- Works with any Loom version
- Future-proof (native Gradle feature)

**Cons**:
- Code duplication across versions
- Difficult to maintain shared code
- High maintenance burden
- Not scalable for many versions
- Merge conflicts when updating shared code

**Compatibility**:
- Works with all Gradle and Minecraft versions
- Requires manual dependency management per version

**Structure Example**:
```
fabric/
  ├── common/    # Shared code
  ├── 1.20.1/    # Version-specific code
  ├── 1.21.1/    # Version-specific code
  └── ...
```

### 5. Preprocessor + Modern Loom

**Description**: Use Stonecutter for preprocessing with modern Fabric Loom or Architectury Loom.

**Pros**:
- Best of both worlds - modern tooling + preprocessing
- Active maintenance from both projects
- Better Gradle 9+ compatibility
- Clean separation of concerns
- Most future-proof option
- Growing community adoption

**Cons**:
- Most complex migration
- Need to learn both tools
- Two dependencies instead of one
- Initial setup complexity

**Compatibility**:
- Excellent - both tools actively maintained
- Full support for current and future versions
- Gradle 8+ recommended

## Detailed Comparison Matrix

| Feature | EGT (Current) | EGT (Newer) | Stonecutter | Architectury Loom | Source Sets | Stonecutter + Loom |
|---------|--------------|-------------|-------------|-------------------|-------------|--------------------|
| **Maintenance** | ⚠️ Moderate | ⚠️ Moderate | ✅ Active | ✅ Very Active | ✅ Native | ✅ Active |
| **Migration Effort** | - | ✅ Minimal | ⚠️ Moderate | ❌ High | ❌ Very High | ❌ High |
| **Future Support** | ⚠️ Uncertain | ⚠️ Uncertain | ✅ Strong | ✅ Strong | ✅ Guaranteed | ✅ Strong |
| **Gradle 9+ Ready** | ❌ Unknown | ⚠️ Unknown | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| **Learning Curve** | - | ✅ None | ⚠️ Moderate | ⚠️ Moderate | ✅ Low | ❌ High |
| **Code Duplication** | ✅ Minimal | ✅ Minimal | ✅ Minimal | ❌ High | ❌ Very High | ✅ Minimal |
| **Community Size** | ⚠️ Small | ⚠️ Small | ✅ Growing | ✅ Large | ✅ Large | ✅ Large |
| **Documentation** | ⚠️ Limited | ⚠️ Limited | ✅ Good | ✅ Excellent | ✅ Excellent | ✅ Good |
| **Build Performance** | ✅ Good | ✅ Good | ✅ Good | ✅ Good | ✅ Excellent | ✅ Good |
| **Multi-Loader Support** | ❌ No | ❌ No | ❌ No | ✅ Yes | ❌ No | ⚠️ Possible |

## Recommendations

### Short-Term (Immediate - 3 months)

**Option**: Continue with EGT 0.6.5 or upgrade to latest EGT version

**Rationale**:
- Minimal disruption to development
- Allows time to plan proper migration
- Can focus on feature development
- Gives time to monitor which alternative gains most traction

**Action Items**:
1. Check for newer EGT versions and test compatibility
2. Document current EGT setup for future migration reference
3. Monitor community trends around multiversion tools

### Medium-Term (3-6 months)

**Option**: Migrate to Stonecutter

**Rationale**:
- Best balance of modern tooling and migration effort
- Strong community momentum
- Designed specifically for Minecraft multiversion
- Can work with existing Loom setup initially
- Future-proof architecture

**Action Items**:
1. Set up Stonecutter alongside EGT in a test branch
2. Migrate one version (e.g., 1.21.8) as proof of concept
3. Update preprocessing comments to Stonecutter syntax
4. Gradually migrate other versions
5. Remove EGT dependency once migration is complete

### Long-Term (6-12 months+)

**Option**: Stonecutter + Architectury Loom (or latest Fabric Loom)

**Rationale**:
- Maximum future-proofing
- Best tooling support
- Potential for multi-loader support if needed
- Industry-standard approach for large multiversion projects
- Excellent Gradle 9+ support

**Action Items**:
1. After Stonecutter migration is stable, evaluate Loom options
2. Consider Architectury Loom if multi-loader support is desired
3. Otherwise, upgrade to latest stable Fabric Loom
4. Optimize build performance and developer experience

## Migration Complexity Assessment

### From EGT to Stonecutter: **MODERATE**

**Estimated Effort**: 2-3 days for initial setup, 1-2 days per version for migration

**Steps**:
1. Add Stonecutter Gradle plugin
2. Configure version tree in build.gradle
3. Convert EGT preprocessing comments to Stonecutter syntax
4. Test each version builds correctly
5. Update CI/CD pipelines
6. Update documentation

**Risk Level**: Low-Medium
- Well-documented process
- Can be done incrementally
- Rollback possible at any stage

### From EGT to Architectury Loom: **HIGH**

**Estimated Effort**: 1-2 weeks

**Steps**:
1. Restructure build.gradle files
2. Add Architectury Loom plugin
3. Either: Use Stonecutter for preprocessing, OR create version-specific source sets
4. Update dependency configurations
5. Test each version
6. Update CI/CD
7. Update documentation

**Risk Level**: Medium-High
- Significant build system changes
- Potential compatibility issues
- More complex testing required

### From EGT to Source Sets: **VERY HIGH**

**Not Recommended**: Excessive code duplication and maintenance burden

## Version Support Analysis

### Current Minecraft Version Support Needed
- **1.20.1**: Long-term support version, many servers still use
- **1.21.x**: Latest releases, active development

### Future Version Considerations
- **1.22+**: Expected to be supported by all modern tools
- **Snapshots**: Stonecutter and modern Loom have best snapshot support

All recommended alternatives (Stonecutter, Architectury Loom) support:
- All currently used versions (1.20.1 - 1.21.8)
- Future versions through active development
- Both Java 17 and Java 21

## Technical Considerations

### Gradle Compatibility
- **Current Gradle**: 8.14
- **Gradle 9**: Coming soon
- **EGT**: Unknown Gradle 9 compatibility
- **Stonecutter**: Confirmed Gradle 9 compatible
- **Architectury Loom**: Confirmed Gradle 9 compatible

### Java Version Requirements
- **1.20.1**: Requires Java 17 (currently supported)
- **1.21.x**: Requires Java 21 (currently supported)
- All alternatives support this requirement

### Build Time Impact
- **EGT**: Current baseline
- **Stonecutter**: Similar or slightly faster
- **Architectury Loom**: Similar to Fabric Loom
- **Source Sets**: Fastest (no preprocessing) but not practical

## Community Trends

### Projects Using Each Tool

**Essential Gradle Toolkit**:
- ViaVersion (but migrating away)
- Some older mods
- Declining adoption

**Stonecutter**:
- Iris Shaders
- Sodium
- Many modern multiversion mods
- Growing adoption

**Architectury Loom**:
- All Architectury-based mods
- Projects targeting multiple loaders
- Very active ecosystem

## Conclusion

**Primary Recommendation**: Migrate to **Stonecutter** (Medium-term)

**Reasoning**:
1. Modern, actively maintained tooling
2. Designed specifically for Minecraft multiversion
3. Reasonable migration effort from EGT
4. Strong community support and adoption
5. Future-proof architecture
6. Better Gradle 9+ compatibility
7. Can work with existing or modern Loom

**Secondary Recommendation**: Combine with **Architectury Loom** or latest **Fabric Loom** (Long-term)

**Reasoning**:
1. Latest Loom features and improvements
2. Better IDE integration
3. Potential multi-loader support (Architectury)
4. Industry standard

**Interim Approach**: Stay on **EGT** while planning migration

**Reasoning**:
1. Allows uninterrupted development
2. Time to evaluate alternatives
3. Wait for any EGT updates
4. Monitor community adoption trends

## Next Steps

1. **Immediate** (Week 1):
   - Document current EGT setup
   - Research latest EGT version availability
   - Set up test environment for Stonecutter

2. **Short-term** (Month 1):
   - Create proof-of-concept with Stonecutter on single version
   - Compare build times and developer experience
   - Get team feedback

3. **Medium-term** (Months 2-3):
   - Plan full migration to Stonecutter
   - Migrate versions incrementally
   - Update documentation and CI/CD

4. **Long-term** (Months 4-6):
   - Evaluate Loom options (Fabric vs Architectury)
   - Implement any additional improvements
   - Share experience with community

## References

- Essential Gradle Toolkit: https://github.com/EssentialGG/architectury-loom
- Stonecutter: https://github.com/Stonecutterz/stonecutter-gradle
- Architectury Loom: https://github.com/architectury/architectury-loom
- Fabric Loom: https://github.com/FabricMC/fabric-loom
- Gradle Documentation: https://docs.gradle.org/current/userguide/userguide.html

---

**Document Version**: 1.0  
**Date**: January 15, 2026  
**Author**: Research for PAPIProxyBridge multiversion strategy
