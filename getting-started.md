# Getting Started & Prerequisites

Before building or running the Alfresco Process Services (APS) SDK, ensure that your local development environment meets the system, repository, and licensing requirements outlined below.

---

## 1. System Requirements

### Java Development Kit (JDK)
The required JDK version depends on the target APS version specified in your build profile:

* **APS versions <= 25.x**: OpenJDK **17**
* **APS versions >= 26.x**: OpenJDK **21**

Ensure your `JAVA_HOME` environment variable points to the corresponding JDK installation:
```bash
java -version
```

### Apache Maven
* **Maven 3.9.x** (3.9.0 or higher is required and enforced by `maven-enforcer-plugin`).
Verify your Maven installation:
```bash
mvn -version
```

### Docker & Docker Compose
* **Docker Desktop** (macOS / Windows) or **Docker Engine** (Linux).
* Docker Compose v2 (`docker compose` command support).
* Docker must have sufficient resources allocated (recommended: at least 4 CPU cores and 6 GB - 8 GB RAM).

---

## 2. Alfresco Nexus Authentication

Alfresco Process Services enterprise artifacts (including base WAR files, engine libraries, and private dependencies) are hosted in private Alfresco Nexus repositories. You must have valid Alfresco customer or partner credentials.

Configure your Maven user settings file (`~/.m2/settings.xml`) with the following server entries:

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                              https://maven.apache.org/xsd/settings-1.0.0.xsd">
  <servers>
    <server>
      <id>activiti-enterprise-releases</id>
      <username>yourAlfrescoUsername</username>
      <password>yourAlfrescoPassword</password>
    </server>
    <server>
      <id>enterprise-releases</id>
      <username>yourAlfrescoUsername</username>
      <password>yourAlfrescoPassword</password>
    </server>
    <server>
      <id>internal-thirdparty</id>
      <username>yourAlfrescoUsername</username>
      <password>yourAlfrescoPassword</password>
    </server>
  </servers>
</settings>
```

> **Note**: The repository URLs are already declared in the project's root `pom.xml`, pointing to `https://artifacts.alfresco.com/nexus/content/repositories/...`. The IDs in your `settings.xml` must match the server IDs defined in the POM.

---

## 3. License Configuration

Alfresco Process Services requires valid licenses for runtime startup, unit testing with the embedded engine, and building Docker containers.

Create or check the `/license` directory located at the root of the project:

```text
aps-sdk-3.x/
├── license/
│   ├── activiti.lic
│   └── transform.lic   (or Aspose.Total.Java.lic)
```

### Required Files:
1. **`activiti.lic`**: The core APS license file.
2. **`transform.lic`** (or **`Aspose.Total.Java.lic`**): Document transformation and rendering license.

### Automated License Propagation:
You do not need to manually copy licenses into submodule target folders. The SDK's Maven build automatically propagates licenses:
* Copies `license/*.lic` to `aps-extensions-jar/src/test/resources/` during the `generate-test-resources` phase for embedded unit tests.
* Copies `license/*.lic` to `activiti-app-overlay-docker/src/main/docker/license/` during the `pre-integration-test` phase for Docker image packaging.

---

## 4. Verification Check

Once prerequisites and credentials are in place, run a quick unit test execution to verify your environment:

```bash
mvn clean test
```

If the build compiles and tests pass, your environment is ready to start development!
