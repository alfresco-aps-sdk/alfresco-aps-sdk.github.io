# Development & Extension Guide

This guide covers best practices and coding patterns for writing custom business logic, listeners, REST services, and process artifacts within the `aps-extensions-jar` module.

---

## Package Naming Conventions

To keep extensions organized and compatible across the platform, follow standard package structures:

| Package | Purpose | Authentication |
| :--- | :--- | :--- |
| `com.activiti.extension.api` | Custom Enterprise REST APIs | Required (Session / Basic Auth) |
| `com.activiti.extension.rest` | Public or internal utility endpoints | Unauthenticated or custom filter |
| `com.activiti.extension.bean` | Shared Spring components & helper services | N/A |
| `com.activiti.extension.<app>.service.tasks` | BPMN Service Task delegates (`JavaDelegate`) | N/A |
| `com.activiti.extension.<app>.listeners` | Process and Task lifecycle listeners | N/A |

---

## 1. Implementing BPMN Service Tasks (`JavaDelegate`)

A service task executes custom Java code during process execution. Implement `org.activiti.engine.delegate.JavaDelegate`:

```java
package com.activiti.extension.foureyes.service.tasks;

import org.activiti.engine.delegate.DelegateExecution;
import org.activiti.engine.delegate.JavaDelegate;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class FourEyesAppServiceTask implements JavaDelegate {

    private final Logger log = LoggerFactory.getLogger(FourEyesAppServiceTask.class);

    @Override
    public void execute(DelegateExecution execution) {
        log.info("Executing custom business logic for process instance: {}", execution.getProcessInstanceId());
        
        // Reading a process variable
        String reviewTitle = (String) execution.getVariable("reviewtitle");
        
        // Setting a process variable
        execution.setVariable("helloWorld", "Service task processed: " + reviewTitle);
    }
}
```

In the APS App Designer, reference this class in your Service Task using the **Class delegate** field:  
`com.activiti.extension.foureyes.service.tasks.FourEyesAppServiceTask`.

---

## 2. Implementing Listeners

### Execution Listeners
Triggered on process start, process end, sequence flow transitions, or activity boundaries:

```java
package com.activiti.extension.foureyes.listeners;

import org.activiti.engine.delegate.DelegateExecution;
import org.activiti.engine.delegate.ExecutionListener;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class FourEyesAppLoggingListener implements ExecutionListener {

    private final Logger log = LoggerFactory.getLogger(FourEyesAppLoggingListener.class);

    @Override
    public void notify(DelegateExecution execution) {
        log.info("Execution event: {} for element: {}", execution.getEventName(), execution.getCurrentActivityId());
    }
}
```

### Task Listeners
Triggered on user task lifecycle events (`create`, `assignment`, `complete`, `delete`):

```java
import org.activiti.engine.delegate.DelegateTask;
import org.activiti.engine.delegate.TaskListener;

public class CustomTaskAssignmentListener implements TaskListener {
    @Override
    public void notify(DelegateTask task) {
        // Access task assignee, form keys, or add custom candidate groups
    }
}
```

---

## 3. Developing Spring Beans & Accessing Core APS Services

APS provides powerful built-in Spring services that can be injected using `@Autowired` or constructor injection:

```java
package com.activiti.extension.bean;

import org.activiti.engine.ProcessEngine;
import org.activiti.engine.RuntimeService;
import org.activiti.engine.TaskService;
import org.activiti.engine.task.Task;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component("customWorkflowUtils")
public class WorkflowUtils {

    @Autowired
    protected ProcessEngine processEngine;

    @Autowired
    protected TaskService taskService;

    @Autowired
    protected RuntimeService runtimeService;

    public String getTaskOutcomeVariable(Task task) {
        if (task != null && task.getFormKey() != null) {
            return "form" + task.getFormKey() + "outcome";
        }
        return "";
    }
}
```

### Key Available APS Services:
* **Engine Services**: `ProcessEngine`, `RuntimeService`, `TaskService`, `RepositoryService`, `HistoryService`, `ManagementService`.
* **Identity Management**: `UserService`, `GroupService`, `TenantService`.
* **Security Context**: `SecurityUtils` (e.g. `SecurityUtils.getCurrentUserObject()` retrieves the authenticated `User`).

---

## 4. Creating Custom REST Endpoints

### Authenticated Enterprise Endpoint
Endpoints mapped under `/enterprise/...` are secured by default through APS security filters:

```java
package com.activiti.extension.api;

import com.activiti.domain.idm.User;
import com.activiti.security.SecurityUtils;
import org.activiti.engine.TaskService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/enterprise/my-api-endpoint")
public class MyApiEndpoint {

    @Autowired
    private TaskService taskService;

    @GetMapping(produces = "application/json")
    public ApiResponse getMyTasks() {
        User currentUser = SecurityUtils.getCurrentUserObject();
        long taskCount = taskService.createTaskQuery()
                                    .taskAssignee(String.valueOf(currentUser.getId()))
                                    .count();

        return new ApiResponse(currentUser.getFullName(), taskCount);
    }

    public record ApiResponse(String fullName, long taskCount) {}
}
```

Access URL: `http://localhost:8080/activiti-app/api/enterprise/my-api-endpoint`

### Public / Custom REST Endpoint
Endpoints placed under `com.activiti.extension.rest` can be mapped under `/app/rest/...`:
```java
@RestController
@RequestMapping("/app/rest/my-rest-endpoint")
public class MyRestEndpoint { ... }
```
Access URL: `http://localhost:8080/activiti-app/app/rest/my-rest-endpoint`

---

## 5. Security Whitelisting Configuration

For secure process execution, APS enforces strict whitelisting on Java classes, Spring beans, and script calls accessed within BPMN expressions or script tasks.

The SDK includes default configuration files in `src/test/resources/activiti/` (propagated to the runtime):

* **`beans-whitelist.conf`**: Lists bean names accessible from expression language (e.g., `#{customWorkflowUtils.method()}`).
* **`whitelisted-classes.conf`**: Fully-qualified Java classes permitted to be instantiated or referenced in scripts.
* **`whitelisted-scripts.conf`**: Allowed script invocations.
* **`shell-commands-whitelist.conf`**: Allowed external shell execution commands.

If you add a new Spring bean or helper class used directly in process definitions, append its identifier or class name to the appropriate whitelist file.

---

## 6. Managing Process Applications (App ZIPs)

You can maintain version-controlled APS application definitions inside:
`aps-extensions-jar/src/test/resources/apps/<app-name>/`

Structure:
```text
apps/fourEyes/
├── 4 Eyes Principle.json          (Application definition metadata)
├── bpmn-models/
│   ├── 4 Eyes Principle-9011.bpmn20.xml
│   ├── 4 Eyes Principle-9011.json
│   └── 4 Eyes Principle-9011.png
└── form-models/
    ├── Insert document-9009.json
    └── Validate document-9010.json
```

During the build, the Maven Assembly plugin (`src/main/assembly/assembly.xml`) packages these into:
`target/aps-extensions-jar-${version}-App.zip`

This ZIP file can be automatically imported by the integration tests or uploaded via the APS App Designer UI.
