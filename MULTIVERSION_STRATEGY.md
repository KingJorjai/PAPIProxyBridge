# Multiversion Strategy Summary

## Quick Reference

This document summarizes the multiversion research and provides actionable recommendations for the PAPIProxyBridge project.

## Current Situation

- **Tool**: Essential Gradle Toolkit (EGT) v0.6.5
- **Versions Supported**: Minecraft 1.20.1, 1.21.1, 1.21.4, 1.21.5, 1.21.8
- **Status**: Working but concerns about long-term maintenance and support

## Key Findings

After comprehensive research of multiversion alternatives (see [MULTIVERSION_RESEARCH.md](MULTIVERSION_RESEARCH.md)), we evaluated:

1. **Essential Gradle Toolkit (EGT)** - Current solution
2. **Stonecutter** - Modern preprocessor
3. **Architectury Loom** - Enhanced Loom with multi-loader support
4. **Fabric Loom (Standalone)** - Standard Fabric tooling
5. **Manual Source Sets** - Native Gradle approach

## Recommended Strategy

### 🎯 Primary Recommendation: Migrate to Stonecutter

**Timeline**: 2-4 weeks  
**Effort**: Moderate  
**Risk**: Low-Medium

**Why Stonecutter?**
- ✅ Actively maintained (2024+)
- ✅ Designed specifically for Minecraft multiversion
- ✅ Reasonable migration effort from EGT
- ✅ Growing community adoption
- ✅ Gradle 9 compatible
- ✅ Better documentation than EGT
- ✅ Cleaner preprocessing syntax

**Key Benefits**:
- Future-proof tooling
- Better IDE integration
- Active community support
- Compatible with current and future MC versions
- Works with existing Loom setup

### 🔄 Migration Plan

#### Phase 1: Preparation (Week 1)
- [ ] Review research documents
- [ ] Set up test environment
- [ ] Create migration branch
- [ ] Backup current working configuration

#### Phase 2: Proof of Concept (Week 2)
- [ ] Add Stonecutter plugin
- [ ] Migrate 1.21.8 (main version) as PoC
- [ ] Test build and functionality
- [ ] Verify preprocessing works correctly

#### Phase 3: Full Migration (Weeks 3-4)
- [ ] Migrate remaining versions (1.20.1, 1.21.1, 1.21.4, 1.21.5)
- [ ] Update preprocessing comments throughout codebase
- [ ] Update CI/CD workflows
- [ ] Test all versions build and run correctly
- [ ] Update documentation

#### Phase 4: Cleanup (Week 4)
- [ ] Remove EGT dependencies
- [ ] Clean up old configuration files
- [ ] Update README and contributing guides
- [ ] Merge to main branch

### 📋 Quick Start Guide

For detailed migration instructions, see: [STONECUTTER_MIGRATION_GUIDE.md](STONECUTTER_MIGRATION_GUIDE.md)

**Basic Steps**:

1. Add Stonecutter repository to settings.gradle:
   ```gradle
   maven { url 'https://maven.kikugie.dev/releases' }
   ```

2. Add Stonecutter plugin:
   ```gradle
   id 'dev.kikugie.stonecutter' version '0.5.4'
   ```

3. Configure versions in build.gradle:
   ```gradle
   stonecutter {
       versions {
           create('1.20.1', '1.20.1')
           create('1.21.1', '1.21.1')
           create('1.21.4', '1.21.4')
           create('1.21.5', '1.21.5')
           create('1.21.8', '1.21.8')
       }
       active = '1.21.8'
   }
   ```

4. Convert preprocessing comments:
   ```java
   // EGT syntax
   //#if MC >= 12100
   
   // becomes Stonecutter syntax
   //? if >=1.21 {
   ```

## Alternative Option: Stay with EGT

If migration is not feasible now:

### Short-Term: Continue with EGT

**Action Items**:
- ✅ Document current EGT setup thoroughly
- ✅ Monitor for EGT updates
- ✅ Plan migration for future
- ⚠️ Be prepared for potential compatibility issues with Gradle 9

**When to Revisit**:
- When Gradle 9 becomes necessary
- When EGT stops receiving updates
- When adding support for new MC versions becomes difficult
- Within 6 months to reassess

## Long-Term Vision (Optional)

After successfully migrating to Stonecutter, consider:

### Upgrade to Modern Loom (6-12 months)

**Option 1: Latest Fabric Loom**
- Stick with Fabric-only support
- Simpler, more focused tooling
- Regular updates from Fabric team

**Option 2: Architectury Loom**
- Opens possibility for multi-loader support (Forge/NeoForge)
- More features and flexibility
- Larger community

**Benefits**:
- Latest Gradle features
- Better IDE integration
- Future MC version support
- Performance improvements

## Decision Matrix

| Factor | Stay with EGT | Migrate to Stonecutter | Full Rewrite (Architectury) |
|--------|---------------|----------------------|---------------------------|
| **Effort** | 🟢 None | 🟡 Moderate | 🔴 High |
| **Risk** | 🟡 Medium | 🟢 Low | 🟡 Medium |
| **Future-Proof** | 🔴 Low | 🟢 High | 🟢 Very High |
| **Time to Complete** | 0 days | 2-4 weeks | 1-2 months |
| **Learning Curve** | 🟢 None | 🟡 Moderate | 🔴 High |
| **Community Support** | 🟡 Limited | 🟢 Good | 🟢 Excellent |
| **Recommendation** | ⚠️ Short-term only | ✅ **Recommended** | 💡 Future consideration |

## Success Criteria

A successful migration to Stonecutter should achieve:

✅ **Functionality**:
- All versions (1.20.1 - 1.21.8) build successfully
- No runtime errors introduced
- Feature parity with current build

✅ **Developer Experience**:
- Faster or equal build times
- Better IDE integration
- Clearer preprocessing syntax

✅ **Maintainability**:
- Easier to add new versions
- Better documentation
- Active community support

✅ **CI/CD**:
- All workflows pass
- Successful artifact generation
- Proper version tagging

## Resources

### Documentation
- [Full Research Report](MULTIVERSION_RESEARCH.md) - Detailed analysis of all options
- [Migration Guide](STONECUTTER_MIGRATION_GUIDE.md) - Step-by-step Stonecutter migration
- [Stonecutter Official Docs](https://stonecutter.kikugie.dev/)

### Example Projects Using Stonecutter
- [Iris Shaders](https://github.com/IrisShaders/Iris)
- [Sodium](https://github.com/CaffeineMC/sodium-fabric)
- Many others in the community

### Community Support
- [Stonecutter Discord](https://discord.gg/TBgNUCfryS)
- [Stonecutter GitHub](https://github.com/Stonecutterz/stonecutter-gradle)
- Fabric Discord #toolchain-dev

## Questions to Consider

Before proceeding with migration:

1. **Timeline**: Can we allocate 2-4 weeks for this migration?
2. **Testing**: Do we have adequate test coverage to validate the migration?
3. **Rollback**: Are we comfortable with the rollback plan if issues arise?
4. **Team Buy-in**: Is the team on board with learning Stonecutter?

## Next Steps

### Immediate Actions

1. **Review Documents**:
   - Read [MULTIVERSION_RESEARCH.md](MULTIVERSION_RESEARCH.md) for full details
   - Read [STONECUTTER_MIGRATION_GUIDE.md](STONECUTTER_MIGRATION_GUIDE.md) for migration steps

2. **Make Decision**:
   - Migrate to Stonecutter now? ✅ **Recommended**
   - Stay with EGT for now? ⚠️ Consider timeline
   - Delay decision? ❌ Not recommended

3. **If Migrating**:
   - Create migration branch
   - Follow migration guide
   - Test thoroughly
   - Update CI/CD

4. **If Staying**:
   - Document decision rationale
   - Set review date (recommend 3-6 months)
   - Monitor EGT community

## Conclusion

Based on comprehensive research, **migrating to Stonecutter** offers the best balance of:
- Modern, maintained tooling
- Reasonable migration effort
- Future compatibility
- Community support

The migration is achievable within 2-4 weeks with moderate effort and low risk. The long-term benefits in maintainability and future-proofing justify the investment.

---

**Document Version**: 1.0  
**Last Updated**: January 15, 2026  
**Next Review**: Q2 2026 (if staying with EGT) or after migration completion
