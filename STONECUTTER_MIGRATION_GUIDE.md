# Stonecutter Migration Guide for PAPIProxyBridge

## Overview

This guide provides step-by-step instructions for migrating from Essential Gradle Toolkit (EGT) to Stonecutter for multiversion support.

## Prerequisites

- Java 17+ and Java 21
- Gradle 8.0+
- Existing PAPIProxyBridge source code

## Migration Steps

### Step 1: Add Stonecutter Plugin

In `settings.gradle`, add the Stonecutter plugin to the pluginManagement block:

```gradle
pluginManagement {
    repositories {
        gradlePluginPortal()
        maven { url 'https://maven.fabricmc.net/' }
        maven { url 'https://maven.architectury.dev/' }
        maven { url 'https://maven.minecraftforge.net' }
        maven { url 'https://maven.kikugie.dev/releases' } // Stonecutter repository
    }
    
    plugins {
        id 'dev.kikugie.stonecutter' version '0.5.4'
        // ... other plugins
    }
}
```

### Step 2: Configure Root Build File

In `build.gradle` (root), configure Stonecutter:

```gradle
plugins {
    id 'dev.kikugie.stonecutter'
    // ... other plugins
}

stonecutter {
    versions {
        // Define all supported versions
        create('1.20.1', '1.20.1')
        create('1.21.1', '1.21.1')
        create('1.21.4', '1.21.4')
        create('1.21.5', '1.21.5')
        create('1.21.8', '1.21.8')
    }
    
    // Set the active version for development
    active = '1.21.8'
    
    // Define version constraints for preprocessing
    constraints {
        '1.20.1' to '1.20.1'
        '1.21.1' to '1.21.1'
        '1.21.4' to '1.21.4'
        '1.21.5' to '1.21.5'
        '1.21.8' to '1.21.8'
    }
}
```

### Step 3: Update Fabric Build Configuration

In `fabric/build.gradle`, update to use Stonecutter versioning:

```gradle
plugins {
    id 'fabric-loom' version '1.9.2'
    id 'dev.kikugie.stonecutter'
}

def mcVersion = stonecutter.current.version

loom {
    serverOnlyMinecraftJar()
}

dependencies {
    minecraft "com.mojang:minecraft:${mcVersion}"
    mappings loom.officialMojangMappings()
    
    modImplementation "net.fabricmc:fabric-loader:${fabric_loader_version}"
    modImplementation "net.fabricmc.fabric-api:fabric-api:${fabric_api_version}"
    modImplementation include("eu.pb4:placeholder-api:${fabric_placeholder_api_version}")
    
    // ... other dependencies
}

// Set Java version based on MC version
if (mcVersion == '1.20.1') {
    java.toolchain.languageVersion = JavaLanguageVersion.of(17)
} else {
    java.toolchain.languageVersion = JavaLanguageVersion.of(21)
}
```

### Step 4: Convert Preprocessing Comments

EGT uses specific comment syntax. Stonecutter has different syntax. Here are the conversions:

#### EGT Syntax:
```java
//#if MC >= 12100
// Code for 1.21.0+
//#else
// Code for older versions
//#endif
```

#### Stonecutter Syntax:
```java
//? if >=1.21 {
/*Code for 1.21.0+*/
//?} else {
Code for older versions
//?}
```

### Step 5: Update Project Structure

Current EGT structure:
```
fabric/
  ├── 1.20.1/
  │   ├── gradle.properties
  │   └── build.gradle (symlink)
  ├── 1.21.1/
  │   ├── gradle.properties
  │   └── build.gradle (symlink)
  └── src/
      └── main/
          └── java/
```

Stonecutter structure:
```
fabric/
  ├── versions/
  │   ├── 1.20.1/
  │   │   ├── build.gradle
  │   │   └── gradle.properties
  │   ├── 1.21.1/
  │   │   ├── build.gradle
  │   │   └── gradle.properties
  │   └── ...
  └── src/
      └── main/
          └── java/
```

### Step 6: Update CI/CD

In `.github/workflows/ci.yml`, update the build command:

```yaml
- name: 'Build all versions'
  run: |
    ./gradlew stonecutter:setupAll
    ./gradlew build
```

### Step 7: Common Preprocessing Patterns

#### Version-Specific Imports

```java
//? if >=1.21 {
/*import net.minecraft.server.level.ServerLevel;*/
//?} else {
import net.minecraft.world.level.Level;
//?}
```

#### Method Calls with Different Signatures

```java
//? if >=1.21 {
/*player.sendSystemMessage(Component.literal(text));*/
//?} else {
player.sendMessage(Component.literal(text), Util.NIL_UUID);
//?}
```

#### Conditional Blocks

```java
public void someMethod() {
    // Common code
    
    //? if >=1.21.4 {
    /*// New feature only in 1.21.4+
    useNewFeature();
    */
    //?}
    
    // More common code
}
```

## Comparison Examples

### EGT Preprocessor Comments

```java
//#if MC >= 12104
    // Minecraft 1.21.4+ code
//#elseif MC >= 12101
    // Minecraft 1.21.1+ code
//#else
    // Minecraft 1.20.1 code
//#endif
```

### Stonecutter Preprocessor Comments

```java
//? if >=1.21.4 {
/*// Minecraft 1.21.4+ code*/
//?} elif >=1.21.1 {
/*// Minecraft 1.21.1+ code*/
//?} else {
// Minecraft 1.20.1 code
//?}
```

## Benefits of Stonecutter

1. **Active Development**: Regular updates and bug fixes
2. **Better Documentation**: Comprehensive guides and examples
3. **Modern Gradle Support**: Works with Gradle 8 and 9
4. **Cleaner Syntax**: More readable preprocessing comments
5. **IDE Support**: Better integration with IntelliJ IDEA
6. **Community**: Growing ecosystem of mods using it

## Testing the Migration

1. **Create Test Branch**:
   ```bash
   git checkout -b test/stonecutter-migration
   ```

2. **Migrate One Version**:
   - Start with 1.21.8 (the main version)
   - Convert preprocessing comments
   - Test build

3. **Verify Build**:
   ```bash
   ./gradlew :fabric:1.21.8:build
   ```

4. **Incrementally Migrate**:
   - Add other versions one at a time
   - Test each version builds correctly
   - Verify runtime functionality

5. **Full Build Test**:
   ```bash
   ./gradlew clean build
   ```

## Rollback Plan

If migration encounters issues:

1. Keep EGT configuration in a backup branch
2. Document any blocking issues
3. Revert to EGT while investigating
4. Consider hybrid approach (EGT + Stonecutter)

## Gradual Migration Approach

You don't have to migrate everything at once:

### Phase 1: Proof of Concept (1-2 days)
- Add Stonecutter plugin alongside EGT
- Migrate one simple file to test
- Verify both systems can coexist

### Phase 2: Single Version (3-5 days)
- Fully migrate 1.21.8 to Stonecutter
- Keep other versions on EGT
- Test hybrid build

### Phase 3: All Versions (1-2 weeks)
- Migrate remaining versions
- Remove EGT dependency
- Update all documentation

## Common Issues and Solutions

### Issue: Version Detection Not Working

**Solution**: Ensure version parameters are correctly set in gradle.properties

```properties
stonecutter.mcVersion=1.21.8
```

### Issue: Preprocessing Comments Not Applied

**Solution**: Check comment syntax is correct and run:
```bash
./gradlew stonecutter:process
```

### Issue: Build Fails on CI

**Solution**: Ensure CI runs setup task:
```yaml
- run: ./gradlew stonecutter:setupAll
```

## Resources

- [Stonecutter Documentation](https://stonecutter.kikugie.dev/)
- [Stonecutter GitHub](https://github.com/Stonecutterz/stonecutter-gradle)
- [Example Projects Using Stonecutter](https://github.com/topics/stonecutter)
- [Migration Guide](https://stonecutter.kikugie.dev/guide/migration)

## Support

If you encounter issues during migration:

1. Check Stonecutter documentation
2. Review example projects
3. Ask in Stonecutter Discord
4. Open issue on Stonecutter GitHub

## Conclusion

Migrating to Stonecutter provides a modern, well-supported foundation for multiversion management. While there is initial effort required, the long-term benefits in maintainability, community support, and future compatibility make it worthwhile.

The gradual migration approach allows testing and validation at each step, minimizing risk and allowing rollback if needed.
