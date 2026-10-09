# Docker & Environment Configuration

The APS SDK automates the creation and orchestration of a multi-container Docker cluster mirroring an enterprise Alfresco Process Services installation.

---

## Container Architecture & Port Mappings

All services communicate over the internal bridge network `aps-network`:

| Container Name | Role | Host Port | Internal Port | Healthcheck |
| :--- | :--- | :--- | :--- | :--- |
| `aps-current-project` | Activiti App (Tomcat 9/10) | `8080` | `8080` | `curl -f http://localhost:8080/activiti-app` |
| | Remote Debugging (JDWP) | `5005` | `5005` | N/A |
| `postgre` | PostgreSQL Database | `5432` | `5432` | `pg_isready -d activiti -U alfresco` |
| `elasticsearch` | Search & Analytics Index | `9200` | `9200` | `curl -f http://localhost:9200/_cat/health` |
| `aps-admin` *(optional)* | Activiti Admin Console | `8081` | `8081` | `curl -f http://localhost:8081/activiti-admin` |

### Default Credentials
* **Activiti App UI**: `http://localhost:8080/activiti-app` (`admin@app.activiti.com` / `admin`)
* **Activiti Admin UI**: `http://localhost:8081/activiti-admin` (`admin` / `admin`)
* **PostgreSQL**: `localhost:5432`, DB: `activiti`, User: `alfresco`, Password: `alfresco`
* **Elasticsearch**: `http://localhost:9200`

---

## Persistent Docker Volumes

To ensure consistent testing and prevent accidental data loss across restarts, three external named Docker volumes persist all data:

1. **`aps-db-volume`**: Persists PostgreSQL database records (`/var/lib/postgresql/data`).
2. **`aps-contentstore-volume`**: Persists document attachments, form content, and binary files (`/act_data`).
3. **`aps-es-volume`**: Persists Elasticsearch indices (`/usr/share/elasticsearch/data`).

### Volume Management
To completely reset and purge all state:
```bash
./run.sh purge
# or
mvn clean -Ppurge-volumes
```

---

## Apple Silicon (ARM64) Support

The APS SDK features native support for Apple Silicon chips (M1/M2/M3/M4). 

The Docker module maintains paired Dockerfiles for each APS version:
* `Dockerfile-${aps.version}` (x86_64)
* `Dockerfile-${aps.version}-arm64` (ARM64)

The build dynamically detects architecture and uses the correct Dockerfile, ensuring native ARM64 performance without emulation overhead.

---

## Remote Debugging

The APS container has JVM debugging enabled by default on port `5005` using JDWP:
`-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=0.0.0.0:5005`

### Connecting Your IDE:
* **IntelliJ IDEA**: Run > Edit Configurations > Add New Configuration > **Remote JVM Debug** (Host: `localhost`, Port: `5005`).
* **Eclipse**: Run > Debug Configurations > **Remote Java Application** (Host: `localhost`, Port: `5005`).
* **VS Code**: Attach to remote process with `"type": "java"`, `"request": "attach"`, `"port": 5005`.

Set breakpoints in your service tasks, listeners, or REST controllers in `aps-extensions-jar` to step through execution live!

### Disabling Debugging for Production Builds
In `activiti-app-overlay-docker/pom.xml`, comment the `CATALINA_OPTS` and debug port mapping:
```xml
<run>
    <env>
        <!-- <CATALINA_OPTS>${catalina.opts.debug}</CATALINA_OPTS> -->
    </env>
    <ports>
        <port>${docker.tomcat.port.external}:${docker.tomcat.port.internal}</port>
        <!-- <port>${aps.debug.port}:${aps.debug.port}</port> -->
    </ports>
```

---

## Configuration Overrides

### Application Properties
To override APS application properties (e.g., mail server, authentication provider, LDAP):
* Edit or add property files in `activiti-app-overlay-docker/src/main/docker/properties/activiti-app.properties`.

### Logging Configuration
To adjust Log4j 2 verbosity:
* Edit `activiti-app-overlay-war/src/main/webapp/WEB-INF/classes/log4j2.properties` (or `log4j2-dev.properties`).
