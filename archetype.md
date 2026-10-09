# Maven Project Archetype

The **APS Project Archetype** (`aps-project-archetype`) allows developers to bootstrap a brand new, fully configured Alfresco Process Services (APS) SDK project with a single Maven command.

The archetype sets up a multi-module reactor project pre-configured for modern APS development, including Java extensions, Docker Compose orchestration, Apple Silicon (ARM64) support, and end-to-end integration tests.

* **GitHub Repository**: [`alfresco-aps-sdk/aps-project-archetype`](https://github.com/alfresco-aps-sdk/aps-project-archetype)
* **Archetype Coordinates**: `org.alfresco.activiti:aps-project-archetype:3.1.3-SNAPSHOT`

---

## 1. Quickstart: Generating a Project

To bootstrap a new APS SDK project, open your terminal and run:

```bash
mvn archetype:generate \
  -DarchetypeGroupId=org.alfresco.activiti \
  -DarchetypeArtifactId=aps-project-archetype \
  -DarchetypeVersion=3.1.3-SNAPSHOT \
  -DgroupId=com.example \
  -DartifactId=my-aps-project \
  -Dversion=1.0.0-SNAPSHOT \
  -Dpackage=com.example.aps \
  -DinteractiveMode=false
```

### Parameters Reference:

| Parameter | Description | Example |
| :--- | :--- | :--- |
| `archetypeGroupId` | Archetype group ID | `org.alfresco.activiti` |
| `archetypeArtifactId` | Archetype artifact ID | `aps-project-archetype` |
| `archetypeVersion` | Version of the archetype | `3.1.3-SNAPSHOT` |
| `groupId` | Your organization's Maven group ID | `com.mycompany.process` |
| `artifactId` | Your new project folder and artifact name | `claim-process-solution` |
| `version` | Initial project version | `1.0.0-SNAPSHOT` |
| `package` | Base Java package for custom classes | `com.mycompany.process` |

---

## 2. Generated Project Structure

Once the command finishes, navigate into your project:

```bash
cd my-aps-project
```

The generated project has the following modular structure:

```text
my-aps-project/
├── pom.xml                                 # Root reactor POM with APS 24.x - 26.x profiles
├── aps-extensions-jar/                     # Custom Java logic, delegates, event listeners, REST APIs
│   ├── src/main/java/                      # Production Java classes
│   └── src/test/java/                      # Embedded Spring Boot / H2 unit tests
├── activiti-app-overlay-war/               # Custom webapp overlay combining extensions with APS WAR
├── activiti-app-overlay-docker/            # Docker Compose orchestration and multi-arch Dockerfiles
│   └── src/main/docker/                    # Compose files and Dockerfile variants
├── activiti-app-integration-tests/         # End-to-end integration tests using REST client
├── license/
│   └── README.md                           # License placement guide
├── run.sh                                  # macOS / Linux automation script
└── run.bat                                 # Windows automation script
```

---

## 3. License Configuration

> [!IMPORTANT]
> **No Licenses Included**: In accordance with Alfresco software licensing terms, **`activiti.lic`** and **`transform.lic`** are **not** bundled in the archetype artifacts.

Before starting your containers or executing integration tests:
1. Obtain your valid licenses from the Alfresco customer/partner portal.
2. Copy **`activiti.lic`** and **`transform.lic`** (or `Aspose.Total.Java.lic`) into the `license/` folder at the root of your generated project:
   ```text
   my-aps-project/
   └── license/
       ├── activiti.lic
       └── transform.lic
   ```

The Maven build and run scripts will automatically propagate these licenses to the Docker container builds and test environments.

---

## 4. Building and Testing

### Run Unit Tests
To verify the generated project structure and compile all modules:
```bash
mvn clean test
```

### Start the Local Docker Environment
To build the Docker image and start the full environment (PostgreSQL + Elasticsearch + APS Tomcat):
```bash
./run.sh start
```
*(On Windows, run `run.bat start`)*

Once started, the services will be accessible at:
* **APS Application**: [http://localhost:8080/activiti-app](http://localhost:8080/activiti-app)
* **APS Admin App**: [http://localhost:8081/activiti-admin](http://localhost:8081/activiti-admin)

To stop the containers:
```bash
./run.sh stop
```

---

## 5. Installing the Archetype from Source

If you want to build or customize the archetype locally:

```bash
git clone https://github.com/alfresco-aps-sdk/aps-project-archetype.git
cd aps-project-archetype
mvn clean install
```

This installs `aps-project-archetype` into your local `~/.m2/repository` catalog.
