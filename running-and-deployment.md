# Running & Deployment Guide

The APS SDK provides two primary methods for building, packaging, deploying, and managing your local environment:
1. **Interactive Run Scripts** (`run.sh` / `run.bat`)
2. **Full Maven Lifecycle Commands**

---

## Method 1: Using Run Scripts (`run.sh` / `run.bat`)

Run scripts wrap common Maven and Docker Compose commands to accelerate day-to-day development.

* **Linux / macOS**: `./run.sh <command>`
* **Windows**: `run.bat <command>`

### Commands Reference

#### Standard Operations (Activiti App, PostgreSQL, Elasticsearch)

| Command | Action | Description |
| :--- | :--- | :--- |
| `build_start` | Clean build, create volumes, start containers, tail logs | Standard command for fresh starts or code changes. |
| `start` | Start containers, tail logs | Starts existing containers without rebuilding code or images. |
| `stop` | Stop containers | Shuts down containers gracefully. |
| `reload_aps` | Fast reload | Rebuilds only `aps-extensions-jar`, `activiti-app-overlay-war`, and Docker container, replacing the container without touching the database or Elasticsearch. |
| `purge` | Remove volumes | Deletes persistent Docker volumes (`aps-db-volume`, `aps-contentstore-volume`, `aps-es-volume`) for a clean state. |
| `tail` | Follow logs | Follows container log output (`docker compose logs -f`). |
| `tail_all` | View all logs | Dumps all existing container logs without following. |
| `build_test` | Build, start, run tests, shutdown | Runs end-to-end integration tests in CI or verification pipelines. |
| `test` | Run tests | Runs unit and module tests for `aps-extensions-jar`. |
| `build_start_it_supported` | Build & start with test prep | Builds artifacts, prepares integration test setup, and launches containers. |

#### Activiti Admin Operations (Adding the `activiti-admin` app)

Append `_admin` to the command to run with the Activiti Admin management container enabled:

* `./run.sh build_start_admin`
* `./run.sh start_admin`
* `./run.sh stop_admin`
* `./run.sh reload_aps_admin`
* `./run.sh purge_admin`
* `./run.sh tail_admin`
* `./run.sh build_test_admin`

---

## Method 2: Full Maven Lifecycle

If you prefer using Maven directly or are configuring a CI/CD pipeline, use the standard goals:

### Build, Package, and Start Stack
```bash
mvn clean install docker:build docker:start
```

### Stop the Stack
```bash
mvn docker:stop
```

### Enable Activiti Admin Application
For the first run building and launching Activiti Admin:
```bash
mvn clean install docker:build docker:start -Pactiviti-admin
```

To stop:
```bash
mvn docker:stop -Pactiviti-admin
```

### Fast Subsequent Builds (Skipping Admin Rebuild)
Once the Activiti Admin container image is already built, skip re-packaging it to reduce build time:
```bash
mvn clean install docker:build docker:start -Pactiviti-admin,skip.admin
```

### Purging Persistent Volumes
```bash
mvn clean -Ppurge-volumes
```

---

## Changing the Extension Deployment Strategy

By default, the SDK embeds `aps-extensions-jar` directly into the overlayed `activiti-app.war` (`WEB-INF/lib/`).

If your architecture prefers deploying extensions as shared libraries in Tomcat's `tomcat/lib/` folder:

1. In `activiti-app-overlay-war/pom.xml`, comment or remove the `aps-extensions-jar` dependency:
   ```xml
   <!--
   <dependency>
       <groupId>org.alfresco.activiti</groupId>
       <artifactId>aps-extensions-jar</artifactId>
       <version>${project.version}</version>
   </dependency>
   -->
   ```
2. In `activiti-app-overlay-docker/src/main/docker/Dockerfile-<version>`, uncomment the line that copies the extension JAR to Tomcat's library directory:
   ```dockerfile
   COPY target/aps-extensions-jar.jar /usr/local/tomcat/lib/
   ```
