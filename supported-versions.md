# Supported APS Versions & Maven Profiles

The Alfresco Process Services SDK uses Maven profiles and a multi-branch architecture to manage dependencies, container images, and runtime parameters for specific versions and hotfix releases of Alfresco Process Services (APS).

---

## 🌿 Branch & APS Compatibility Overview

The APS SDK repository maintains dedicated branches corresponding to each major era of Alfresco Process Services. To work with a specific generation of APS, select and checkout the corresponding branch:

| Project Branch | SDK Version | APS Generation | Supported APS Versions | Required Java | Branch Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| [**`3.x`**](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/3.x) *(default)* | **APS SDK 3.x** | Modern APS | **APS 24.1.0 up to APS 26.2.0** | Java 17, Java 21 | Active (Latest) |
| [**`2.x`**](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/2.x) | **APS SDK 2.x** | Legacy APS 2.x | **APS 2.0.0 up to APS 2.4.5** | Java 11, Java 17 | Maintenance (Legacy) |
| [**`master`**](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/master) | **APS SDK 1.x** | Legacy APS 1.x | **APS 1.9.0.5 up to APS 1.11.5** | Java 8, Java 11 | Maintenance (Legacy) |

---

## 🚀 Modern APS Matrix (`3.x` Branch)

The **`3.x` branch** (APS SDK 3.x) provides extensive support across modern Alfresco Process Services releases and hotfixes:

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

### How to Switch Profiles in `3.x` Branch

#### Option A: Command Line Flag (`-P`)
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

#### Option B: Change Default Profile in `pom.xml`
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

#### Automatic Resolution in `run.sh`
The `./run.sh` script automatically inspects `pom.xml` and resolves the active `aps.version`:
```bash
export APS_VERSION=$(mvn help:evaluate -Dexpression=aps.version -q -DforceStdout)
```
When running `./run.sh build_start`, it seamlessly compiles and spins up containers for the currently active profile.

---

## 🏛️ Legacy APS 2.x Matrix (`2.x` Branch) :id=legacy-aps-2x

The [**`2.x` branch**](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/2.x) provides support for legacy **APS 2.x** installations:

* **Repository Branch**: [`2.x`](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/2.x)
* **Supported APS Versions**: `2.0.0`, `2.0.1`, `2.1.0`, `2.2.0`, `2.2.0.1`, `2.3.0` through `2.3.10`, `2.4.0` through `2.4.5`
* **Java Requirements**:
  * OpenJDK **17** for APS `>= 2.4.x`
  * OpenJDK **11** for APS `<= 2.3.9`
* **Build Tool**: Apache Maven 3.9+

### Supported Profiles in `2.x` Branch:

| APS Version Range | Profile Examples | Required JDK |
| :--- | :--- | :--- |
| **2.4.0 – 2.4.5** | `aps2.4.5`, `aps2.4.4`, `aps2.4.3`, `aps2.4.2`, `aps2.4.2.1` – `aps2.4.2.15`, `aps2.4.1`, `aps2.4.1.1` – `aps2.4.1.2`, `aps2.4.0` | **Java 17** |
| **2.3.0 – 2.3.10** | `aps2.3.10`, `aps2.3.9`, `aps2.3.8`, `aps2.3.8.1` – `aps2.3.8.7`, `aps2.3.7` – `aps2.3.7.2`, `aps2.3.6` – `aps2.3.6.2`, `aps2.3.5` – `aps2.3.5.1`, `aps2.3.0` – `aps2.3.4` | **Java 11** |
| **2.2.0 – 2.2.0.1** | `aps2.2.0`, `aps2.2.0.1` | **Java 11** |
| **2.0.0 – 2.1.0** | `aps2.1.0`, `aps2.0.1`, `aps2.0.0` | **Java 11** |

### Checking Out & Running `2.x` Branch:
```bash
# Clone the 2.x branch
git clone -b 2.x https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk.git
cd alfresco-process-services-project-sdk

# Build and start targeting a specific APS 2.x profile
mvn clean install docker:build docker:start -Paps2.4.5
```

> [!NOTE]
> For projects using earlier SDK 2.x releases:
> * [Upgrading APS SDK 2.x to Support APS 2.2.0](legacy-upgrade-2.2.0.md) (for versions earlier than 2.0.8)
> * [Adding License Management to Legacy Projects](legacy-license-management.md) (for versions earlier than 2.0.9)

---

## 🏛️ Legacy APS 1.x Matrix (`master` Branch) :id=legacy-aps-1x

The [**`master` branch**](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/master) provides support for legacy **APS 1.x** installations:

* **Repository Branch**: [`master`](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/master)
* **Supported APS Versions**: `1.9.0.5`, `1.10.0`, `1.10.0.1`, `1.11.0`, `1.11.1`, `1.11.1.1`, `1.11.2`, `1.11.3`, `1.11.4`, `1.11.4.1`, `1.11.4.2`, `1.11.4.3`, `1.11.5`
* **Java Requirements**: OpenJDK **8** or OpenJDK **11**
* **Build Tool**: Apache Maven 3.9+

### Supported Profiles in `master` (1.x) Branch:

| APS Version Range | Profile Examples | Required JDK |
| :--- | :--- | :--- |
| **1.11.0 – 1.11.5** | `aps1.11.5`, `aps1.11.4.3`, `aps1.11.4.2`, `aps1.11.4.1`, `aps1.11.4`, `aps1.11.3`, `aps1.11.2`, `aps1.11.1.1`, `aps1.11.1`, `aps1.11.0` | **Java 11** |
| **1.10.0 – 1.10.0.1** | `aps1.10.0.1`, `aps1.10.0` | **Java 8 / 11** |
| **1.9.0.5** | `aps1.9.0.5` | **Java 8 / 11** |

### Checking Out & Running `master` (1.x) Branch:
```bash
# Clone the master branch
git clone -b master https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk.git
cd alfresco-process-services-project-sdk

# Build and start targeting a specific APS 1.x profile
mvn clean install docker:build docker:start -Paps1.11.5
```

> [!NOTE]
> For projects using earlier SDK 1.x releases:
> * [Adding License Management to Legacy Projects](legacy-license-management.md) (for versions earlier than 1.7.4)
