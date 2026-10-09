# Testing Guide

The APS SDK offers a two-tier testing architecture:
1. **Embedded Unit Tests**: Lightning-fast tests running inside a Spring test context with an in-memory H2 database.
2. **Containerized Integration Tests**: End-to-end tests driving live Docker containers via the official generated OpenAPI/Swagger Java client.

---

## 1. Embedded Unit Tests (`aps-extensions-jar`)

Unit tests reside in:
`aps-extensions-jar/src/test/java/org/alfresco/activiti/unit/tests/`

### Anatomy of a Unit Test
Unit tests leverage `@SpringJUnitConfig` with `ApplicationWhitelistingTestConfiguration.class` to instantiate an embedded Activiti Engine connected to an in-memory H2 database.

```java
package org.alfresco.activiti.unit.tests;

import static org.junit.jupiter.api.Assertions.*;
import org.activiti.engine.RuntimeService;
import org.activiti.engine.TaskService;
import org.activiti.engine.runtime.ProcessInstance;
import org.activiti.engine.task.Task;
import org.alfresco.activiti.conf.ApplicationWhitelistingTestConfiguration;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;

@SpringJUnitConfig(classes = ApplicationWhitelistingTestConfiguration.class)
public class FourEyesTest {

    @Autowired
    protected RuntimeService runtimeService;

    @Autowired
    protected TaskService taskService;

    @Test
    public void testProcessExecution() {
        // 1. Deploy process definition from test classpath
        // 2. Start process instance
        ProcessInstance instance = runtimeService.startProcessInstanceByKey("fourEyesPrinciple");
        assertNotNull(instance);

        // 3. Query active task
        Task task = taskService.createTaskQuery().singleResult();
        assertEquals("Insert document", task.getName());

        // 4. Complete task with outcome variable
        taskService.complete(task.getId());
    }
}
```

### Running Unit Tests:
```bash
mvn clean test
# or
./run.sh test
```

---

## 2. Containerized Integration Tests (`activiti-app-integration-tests`)

Integration tests reside in:
`activiti-app-integration-tests/src/test/java/com/activiti/sdk/integrationtests/`

They execute against the live Tomcat and PostgreSQL/Elasticsearch Docker containers running on `http://localhost:8080`.

### Client Architecture
Integration tests utilize the official generated Java Swagger/OpenAPI client:

* **Client Context**: `com.activiti.sdk.client.ApiClient` configured with target base URL, credentials (`admin@app.activiti.com` / `admin`).
* **API Modules**:
  * `AboutApi`: System status and version reporting.
  * `AppDefinitionsApi` & `RuntimeAppDefinitionsApi`: App deployment, query, and deletion.
  * `ProcessDefinitionsApi` & `ProcessInstancesApi`: Process orchestration.
  * `TasksApi` & `TaskFormsApi`: Task queries and form completion.

### Integration Test Workflow (e.g. `FourEyesAppIT`):
1. **`testAboutApi`**: Verifies connectivity and confirms the APS edition name.
2. **`testFourEyesApp`**:
   - Imports `target/aps-extensions-jar-*-App.zip` into the running container.
   - Starts a process instance with variables.
   - Submits First Approval form via `completeTaskFormUsingPOST`.
   - Submits Second Approval form.
   - Verifies process reaches end state and cleans up deployed app definition.
3. **`testCustomPrivateRestEndpoint`**: Confirms authenticated GET requests against custom endpoints (`/api/enterprise/...`).
4. **`testCustomPublicRestEndpoint`**: Confirms unauthenticated GET requests against custom endpoints (`/app/rest/...`).

### Running Integration Tests:

**Using the automated run script (builds, starts, tests, shuts down):**
```bash
./run.sh build_test
```

**Using Maven lifecycle:**
```bash
mvn clean verify
```

---

## 3. Test Troubleshooting Tips

* **Missing License Error**: If tests fail with a licensing exception, confirm that `activiti.lic` exists in the root `/license/` directory.
* **Port Conflict on 8080 or 5432**: Ensure no other local Tomcat, PostgreSQL, or container instances occupy ports `8080`, `5432`, `9200`, or `5005`.
* **Container Startup Timeout**: If the integration test starts before Tomcat is fully initialized, check Docker healthcheck status with `docker compose ps` to ensure all services report `healthy`.
