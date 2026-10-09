# Alfresco Process Services SDK (APS SDK 3.x)

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg?logo=apache)](https://www.apache.org/licenses/LICENSE-2.0)
[![Java](https://img.shields.io/badge/Java-17%20%7C%2021-orange.svg?logo=openjdk&logoColor=white)](https://openjdk.org/)
[![Maven](https://img.shields.io/badge/Maven-3.9%2B-C71A36.svg?logo=apachemaven&logoColor=white)](https://maven.apache.org/)
[![Docker Multi-Arch](https://img.shields.io/badge/Docker-Multi--Arch%20(x86__64%20%7C%20ARM64)-2496ED.svg?logo=docker&logoColor=white)](https://www.docker.com/)
[![APS Support](https://img.shields.io/badge/APS%20Support-1.x%20%7C%202.x%20%7C%2024.x--26.x-009900.svg)](#/supported-versions)
[![Enterprise Support](https://img.shields.io/badge/Enterprise%20Support-TAI%20Solutions-red.svg)](https://www.taisolutions.com/)

The **Alfresco Process Services SDK (APS SDK)** is an enterprise development acceleration kit for building, extending, testing, and deploying custom solutions on **Alfresco Process Services (powered by Activiti)**.

The SDK standardizes developer workflows by providing embedded test runtimes, Dockerized multi-container environments, automated packaging (JAR, WAR overlays, and container images), and native multi-architecture support.

---

## 🚀 Key Capabilities

* **Native Multi-Architecture Support**: Native container execution on Apple Silicon (M1/M2/M3/M4) as well as x86_64, using dedicated Dockerfiles and automatic Maven profile resolution.
* **Production-Mirror Local Stack**: Multi-container Docker environment mirroring production topologies (Alfresco Process Services, Activiti Admin, PostgreSQL, and Elasticsearch).
* **Storage Persistence**: Dedicated persistent Docker volumes for the database (`aps-db-volume`), contentstore attachments (`aps-contentstore-volume`), and search index (`aps-es-volume`).
* **Dual Execution Modes**: Flexible operations via intuitive terminal run scripts (`./run.sh` / `run.bat`) or direct Maven lifecycles (`mvn clean install docker:build docker:start`).
* **Comprehensive Testing Pipeline**: Embedded in-memory H2 test runtime for fast unit tests, coupled with an integration test suite driven by a generated Swagger/OpenAPI Java client.
* **Remote Debugging**: Out-of-the-box JDWP remote debugging over port `5005`.

---

## 🌿 Project Branches & APS Version Compatibility

The APS SDK repository maintains dedicated branches corresponding to each major generation of Alfresco Process Services, providing continuous support for legacy environments as well as modern enterprise deployments:

| Project Branch | SDK Version | APS Generation | Supported APS Versions | Supported Java | Branch Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| [**`3.x`**](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/3.x) *(current)* | **APS SDK 3.x** | Modern APS | **APS 24.1.0 up to APS 26.2.0** | Java 17, Java 21 | Active (Latest) |
| [**`2.x`**](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/2.x) | **APS SDK 2.x** | Legacy APS 2.x | **APS 2.0.0 up to APS 2.4.5** | Java 11, Java 17 | Maintenance (Legacy) |
| [**`master`**](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/master) | **APS SDK 1.x** | Legacy APS 1.x | **APS 1.9.0.5 up to APS 1.11.5** | Java 8, Java 11 | Maintenance (Legacy) |

> [!TIP]
> **Working with legacy APS?**  
> If your enterprise is running legacy versions of Alfresco Process Services:
> * For **APS 2.x** (APS 2.0.0 through 2.4.5), checkout the [`2.x` branch](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/2.x).
> * For **APS 1.x** (APS 1.9.0.5 through 1.11.5), checkout the [`master` branch](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/master).
> * For all **modern APS releases** (APS 24.1.0 up to 26.2.0+), use the [`3.x` branch](https://github.com/alfresco-aps-sdk/alfresco-process-services-project-sdk/tree/3.x) or bootstrap your project with the [Maven Archetype](archetype.md).

---

## 📚 Documentation Guides

Navigate through the developer guides to get started with the APS SDK:

* 🚀 **[Prerequisites & Setup](getting-started.md)**  
  System requirements, configuring Alfresco Nexus credentials in Maven `settings.xml`, and license files placement.

* 📦 **[Maven Project Archetype](archetype.md)**  
  Bootstrap a customized, production-ready APS project in seconds using `mvn archetype:generate` directly from **Maven Central** (`io.github.alfresco-aps-sdk:aps-project-archetype:3.1.3`).

* 🏗️ **[Architecture & Modules](architecture.md)**  
  Modular breakdown of `aps-extensions-jar`, `activiti-app-overlay-war`, `activiti-app-overlay-docker`, and `activiti-app-integration-tests`.

* 💻 **[Development & Extension Guide](development-guide.md)**  
  Best practices for writing BPMN `JavaDelegate` tasks, task/execution listeners, Spring components, custom REST endpoints, and security whitelisting.

* ⚡ **[Running & Deployment](running-and-deployment.md)**  
  Complete command reference for interactive run scripts (`./run.sh` / `run.bat`) and full Maven lifecycle commands.

* 🐳 **[Docker & Environment Configuration](docker-configuration.md)**  
  Container topology, persistent volume management, Apple Silicon (ARM64) support, and IDE remote debugging setup.

* 🧪 **[Testing Guide](testing-guide.md)**  
  Fast unit tests with the embedded H2 engine and end-to-end integration tests using the Swagger OpenAPI Java SDK.

* 📋 **[Supported APS Versions & Profiles](supported-versions.md)**  
  Compatibility matrix covering modern APS 24.1.0 through 26.2.0, legacy APS 1.x and 2.x branch mappings, dependency alignments, and Maven profile switching.

---

## ⚡ Quickstart: Bootstrap with Maven Archetype

Create a complete APS project skeleton in seconds using the official **APS Project Archetype**:

```bash
mvn archetype:generate \
  -DarchetypeGroupId=io.github.alfresco-aps-sdk \
  -DarchetypeArtifactId=aps-project-archetype \
  -DarchetypeVersion=3.1.3 \
  -DgroupId=com.example \
  -DartifactId=my-aps-project \
  -Dversion=1.0.0-SNAPSHOT \
  -Dpackage=com.example.aps \
  -DinteractiveMode=false
```

Learn more in the **[Archetype Guide](archetype.md)**.

---

## 👥 Contributors & Maintainers

* **Piergiorgio Lucidi** (`piergiorgio@apache.org`) — Project Creator & Maintainer
* **Jeff Potts** — Documentation updates
* **Luca Stancapiano** — Testing & feature improvements
* **Bindu Wavell** — Testing & tooling extensions
* **Stanley Arnold** — Maven configuration enhancements

---

## 💼 Enterprise Support

This project is maintained as an open-source community effort. Enterprise maintenance, support, and consulting are provided by [**TAI Solutions**](https://www.taisolutions.com/).
