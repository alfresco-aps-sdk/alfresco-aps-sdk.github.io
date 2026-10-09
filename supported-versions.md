# Supported APS Versions & Maven Profiles

The Alfresco Process Services SDK uses Maven profiles to manage dependencies, container images, and runtime parameters for specific versions and hotfix releases of Alfresco Process Services.

---

## Supported Version Matrix

The SDK 3.x series provides extensive support across major APS versions and latest hotfix releases:

| APS Version | Profile ID | Required JDK | Spring Boot | Spring Framework | Elasticsearch | PostgreSQL |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **26.2.0** *(default)* | `aps26.2.0` | **Java 21** | 4.1.0 | 7.0.8 | 9.3.4 | 17 |
| **26.1.0** | `aps26.1.0` | **Java 21** | 4.0.3 | 7.0.5 | 9.3.1 | 17 |
| **25.5.0** | `aps25.5.0` | **Java 17** | 3.5.16 | 6.2.19 | 8.19.20 | 13.12 |
| **25.4.1** | `aps25.4.1` | **Java 17** | 3.5.15 | 6.2.19 | 8.19.8 | 13.12 |
| **25.4.0** | `aps25.4.0` | **Java 17** | 3.5.14 | 6.2.18 | 8.19.8 | 13.12 |
| **25.3.0** | `aps25.3.0` | **Java 17** | 3.5.7 | 6.2.12 | 8.18.8 | 13.12 |
| **25.2.4** | `aps25.2.4` | **Java 17** | 3.5.3 | 6.2.8 | 8.17.4 | 13.12 |
| **25.2.0 - 25.2.3** | `aps25.2.0` - `aps25.2.3` | **Java 17** | 3.4.x | 6.2.x | 8.16.x - 8.17.x | 13.12 |
| **25.1.0 - 25.1.1** | `aps25.1.0`, `aps25.1.1` | **Java 17** | 3.3.x | 6.1.x | 8.15.x | 13.12 |
| **24.7.1** | `aps24.7.1` | **Java 17** | 3.5.13 | 6.2.17 | 8.19.7 | 13.12 |
| **24.7.0** | `aps24.7.0` | **Java 17** | 3.3.x | 6.1.x | 8.14.x | 13.12 |
| **24.6.0** | `aps24.6.0` | **Java 17** | 3.2.x | 6.1.x | 8.13.x | 13.12 |
| **24.5.0** | `aps24.5.0` | **Java 17** | 3.2.x | 6.1.x | 8.13.x | 13.12 |
| **24.4.0 - 24.4.7** | `aps24.4.0` - `aps24.4.7` | **Java 17** | 3.2.x | 6.1.x | 8.13.x | 13.12 |
| **24.3.0 - 24.3.1** | `aps24.3.0`, `aps24.3.1` | **Java 17** | 3.2.x | 6.1.x | 8.13.x | 13.12 |
| **24.2.0 - 24.2.1** | `aps24.2.0`, `aps24.2.1` | **Java 17** | 3.2.x | 6.1.5 | 8.13.0 | 13.12 |
| **24.1.0** | `aps24.1.0` | **Java 17** | 3.1.8 | 6.0.17 | 7.17.18 | 13.12 |

---

## How to Switch Profiles

### Option A: Command Line Flag (`-P`)
Target any supported version or hotfix dynamically by passing `-P`:

```bash
# Target default APS 26.2.0
mvn clean test

# Target APS 26.1.0
mvn clean test -Paps26.1.0

# Target latest 25.x hotfix (APS 25.5.0)
mvn clean install docker:build docker:start -Paps25.5.0

# Target APS 24.7.1
mvn clean install docker:build docker:start -Paps24.7.1
```

### Option B: Change Default Profile in `pom.xml`
To change the default APS version across all executions without flags:

1. Open `pom.xml`.
2. Locate the `<profile>` you wish to activate by default.
3. Set `<activeByDefault>true</activeByDefault>`:
   ```xml
   <profile>
       <id>aps26.2.0</id>
       <activation>
           <activeByDefault>true</activeByDefault>
       </activation>
       ...
   </profile>
   ```
4. Set `<activeByDefault>false</activeByDefault>` on any other profiles.

### Automatic Resolution in `run.sh`
The `./run.sh` script automatically inspects `pom.xml` and resolves the active `aps.version`:
```bash
export APS_VERSION=$(mvn help:evaluate -Dexpression=aps.version -q -DforceStdout)
```
When running `./run.sh build_start`, it seamlessly compiles and spins up containers for the currently active profile.
