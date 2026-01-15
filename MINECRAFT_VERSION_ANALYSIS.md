# Minecraft Version Support Analysis - PAPIProxyBridge

## Executive Summary

**Maximum currently supported version:** Minecraft 1.21.8  
**Analysis date:** January 15, 2026

---

## Current Support Status

### Bukkit/Spigot/Paper
- **Minimum API:** 1.16 (defined in `plugin.yml`)
- **Minimum version:** 1.16.5
- **Development/test version:** 1.21.7 (configured in `bukkit/build.gradle`)
- **Versions declared in CI:** 1.17.1 through 1.21.8
- **Folia support:** Yes
- **Java required:** 17+

### Fabric
The project uses a multi-version system with Essential Gradle Toolkit (EGT) that allows compiling for multiple Minecraft versions simultaneously.

**Currently supported versions:**

#### 1.20.1
- **Java:** 17
- **Fabric Loader:** 0.15.11
- **Fabric API:** 0.92.2+1.20.1
- **PlaceholderAPI:** 2.1.4+1.20.1
- **Adventure Platform:** 5.9.0

#### 1.21.1
- **Java:** 21
- **Fabric Loader:** 0.16.10
- **Fabric API:** 0.115.4+1.21.1
- **PlaceholderAPI:** 2.4.2+1.21
- **Adventure Platform:** 5.14.2

#### 1.21.4
- **Java:** 21
- **Fabric Loader:** 0.16.10
- **Fabric API:** 0.116.1+1.21.4
- **PlaceholderAPI:** 2.5.2+1.21.3
- **Adventure Platform:** 6.3.0

#### 1.21.5
- **Java:** 21
- **Fabric Loader:** 0.16.14
- **Fabric API:** 0.122.0+1.21.5
- **PlaceholderAPI:** 2.6.3+1.21.5
- **Adventure Platform:** 6.4.0

#### 1.21.8 (Main version - mainProject)
- **Java:** 21
- **Fabric Loader:** 0.16.14
- **Fabric API:** 0.133.0+1.21.8
- **PlaceholderAPI:** 2.7.2+1.21.8
- **Adventure Platform:** 6.6.0

### Proxy (BungeeCord/Velocity)
- **Minecraft version agnostic**
- **BungeeCord:** Compatible with Minecraft 1.17.1+
- **Velocity:** Compatible with Minecraft 1.17.1+
- **Java required:** 21 (Bungee), 17 (Velocity)

---

## Update Possibilities Analysis

### Key Limitations

1. **PlaceholderAPI Mod (Fabric)**
   - The most critical component is the availability of PlaceholderAPI mod for Fabric
   - Developed by pb4 at https://maven.nucleoid.xyz
   - Latest known version: 2.7.2+1.21.8
   - **Requires investigation:** Check if versions exist for Minecraft 1.21.9+ or 1.22+

2. **PlaceholderAPI Plugin (Bukkit)**
   - Compiled version: 2.11.6
   - Generally compatible with future Minecraft versions
   - Less restrictive than the Fabric version

3. **Fabric API**
   - Latest known version: 0.133.0+1.21.8
   - Available at https://maven.fabricmc.net
   - Usually updates quickly for new Minecraft versions

### Available Minecraft Versions (Known)

Based on CI configuration, the project already declares support up to:
- **Bukkit/Spigot:** 1.21.8
- **Fabric:** 1.21.8
- **Proxy:** 1.21.8

### Minecraft 1.21.9+

**Status:** To be investigated
- Check if Minecraft 1.21.9 has been released
- Check availability of:
  - Fabric API for 1.21.9+
  - PlaceholderAPI mod for 1.21.9+
  - Compatible Fabric Loader

### Minecraft 1.22+

**Status:** Future
- Minecraft 1.22 has not been officially announced yet (as of January 2026)
- Once released, it will require:
  - Update Fabric Loader
  - Wait for Fabric API to be compatible
  - Wait for PlaceholderAPI mod to be compatible
  - Possible changes in Minecraft APIs that may require code modifications

---

## Proposed Update Plan

### Phase 1: Research (Immediate)

1. **Verify available versions:**
   ```bash
   # Check latest versions in Maven repositories
   - Fabric API: https://maven.fabricmc.net/net/fabricmc/fabric-api/fabric-api/
   - PlaceholderAPI (Fabric): https://maven.nucleoid.xyz/eu/pb4/placeholder-api/
   - Fabric Loader: https://maven.fabricmc.net/net/fabricmc/fabric-loader/
   ```

2. **Consult official sources:**
   - Modrinth.com for PlaceholderAPI mod
   - CurseForge for available versions
   - PlaceholderAPI project GitHub (https://github.com/Patbox/TextPlaceholderAPI)

3. **Check Minecraft changelog:**
   - Review API changes between versions
   - Identify potential breaking changes

### Phase 2: Preparation for Minecraft 1.21.9+ (If available)

1. **Create new version structure:**
   ```
   fabric/1.21.9/
   └── gradle.properties
   ```

2. **Update configuration files:**
   - `fabric/mainProject` → update to 1.21.9
   - `fabric/1.21.9/gradle.properties` → define dependencies
   - `.github/workflows/ci.yml` → add steps for 1.21.9
   - `settings.gradle` → will automatically detect new version

3. **Update dependencies (example for 1.21.9):**
   ```properties
   essential.defaults.loom.mappings=net.fabricmc:yarn:1.21.9+build.X:v2
   
   fabric_loader_version=0.16.X
   fabric_api_version=0.XXX.X+1.21.9
   fabric_adventure_platform_version=6.X.X
   fabric_placeholder_api_version=2.X.X+1.21.9
   ```

4. **Test compilation:**
   ```bash
   ./gradlew clean build
   ```

5. **Test on development server:**
   ```bash
   ./gradlew :fabric:1.21.9:runServer
   ```

### Phase 3: Bukkit Update (If necessary)

1. **Update test version:**
   - Modify `bukkit/build.gradle` → `runServer.minecraftVersion("1.21.9")`

2. **Verify PlaceholderAPI compatibility:**
   - Generally requires no changes
   - Check at https://www.spigotmc.org/resources/placeholderapi.6245/

3. **Update version list in CI:**
   - Add 1.21.9 to the `game-versions` list in `.github/workflows/ci.yml`

### Phase 4: Testing and Validation

1. **Functional tests:**
   - Placeholder formatting on Bukkit
   - Placeholder formatting on Fabric
   - Proxy-backend communication via PluginMessage
   - Proxy-backend communication via Redis
   - Compatibility with Paper and Folia

2. **Integration tests:**
   - Multi-server network tests
   - Tests with different proxies (Velocity, BungeeCord)

3. **CI/CD:**
   - Verify all builds pass
   - Verify artifacts are generated correctly

### Phase 5: Deployment

1. **Update documentation:**
   - `README.md` → update supported versions
   - Changelog/Release notes

2. **Publish to repositories:**
   - Maven (repo.william278.net)
   - Modrinth
   - (Optional) Hangar

---

## Required Changes per Version

### To add Minecraft 1.21.9 (Example)

**Files to modify:**

1. **`fabric/1.21.9/gradle.properties`** (CREATE)
   ```properties
   essential.defaults.loom.mappings=net.fabricmc:yarn:1.21.9+build.X:v2
   
   fabric_loader_version=0.16.X
   fabric_api_version=0.XXX.X+1.21.9
   fabric_adventure_platform_version=6.X.X
   fabric_placeholder_api_version=2.X.X+1.21.9
   ```

2. **`fabric/mainProject`** (MODIFY)
   ```
   1.21.9
   ```

3. **`.github/workflows/ci.yml`** (MODIFY)
   - Add publication step for Fabric 1.21.9
   - Add 1.21.9 to `game-versions` lists
   - Add JAR file to `files` list

4. **`bukkit/build.gradle`** (OPTIONAL, for development)
   ```groovy
   runServer {
       minecraftVersion("1.21.9")
   }
   ```

**Files that DO NOT require changes:**
- `settings.gradle` - Automatically detects new folders with version pattern
- `fabric/build.gradle` - Works with all versions
- `fabric/src/` - Shared code between versions
- Java code files (unless there are breaking changes in Minecraft)

---

## Risk Factors

### High Risk
- **PlaceholderAPI mod availability:** If pb4 doesn't maintain the mod for new versions, Fabric cannot be supported
- **Breaking changes in Minecraft:** Changes in networking or text APIs could require significant refactoring

### Medium Risk
- **Changes in Fabric API:** Could require code adjustments
- **Changes in Adventure Platform:** Could affect text component handling

### Low Risk
- **PlaceholderAPI plugin (Bukkit):** Historically very stable and forward compatible
- **Proxies (Velocity/Bungee):** Minecraft version agnostic

---

## Effort Estimation

### Adding support for 1.21.9 (assuming all dependencies exist)

- **Estimated time:** 2-4 hours
- **Complexity:** Low
- **Steps:**
  1. Dependency version research: 30 min
  2. Create structure and configuration: 30 min
  3. Initial compilation and adjustments: 30 min
  4. Functional testing: 1-2 hours
  5. Update CI/CD: 30 min

### Adding support for 1.22+ (major version)

- **Estimated time:** 1-2 weeks (depending on breaking changes)
- **Complexity:** Medium to High
- **Factors:**
  - Wait for all dependencies to be available
  - Possible changes in Minecraft APIs
  - Possible code refactoring
  - Extensive testing

---

## Recommendations

1. **Immediate:**
   - Check if Minecraft 1.21.9 exists and if dependencies are available
   - If so, add support following Phase 2 of the plan

2. **Short term (1-3 months):**
   - Monitor Minecraft 1.22 announcements
   - Prepare testing infrastructure for new versions
   - Consider automation for detecting new dependency versions

3. **Long term:**
   - Consider migrating to a more automated system for adding versions
   - Implement more robust integration tests
   - Document the process of adding versions for contributors

---

## Conclusions

**Is it possible to add support for new versions?**
- **Yes**, as long as:
  1. PlaceholderAPI mod is available for Fabric
  2. Fabric API is compatible with the new version
  3. There are no significant breaking changes in Minecraft

**Maximum currently supported version:**
- **1.21.8** (Fabric and Bukkit)

**Up to which version could we upgrade with changes:**
- **1.21.9+:** Minimal changes (just update dependencies)
- **1.22+:** Requires investigation and possible code changes
- **Main limitation:** PlaceholderAPI mod availability for Fabric

The project is well-structured to support multiple versions thanks to the Essential Gradle Toolkit multi-version system. Adding new versions is relatively straightforward once dependencies are available.
