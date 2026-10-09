With the recent release of [APS SDK v2.0.8](https://github.com/OpenPj/alfresco-process-services-project-sdk/releases/tag/v2.0.8) we added support for APS 2.2.0 but if you don't want to migrate your extensions on a brand new project, you can add all the needed snippets in your existing project.

## Upgrade steps
1. Import the [Activiti App Dockerfile](https://github.com/OpenPj/alfresco-process-services-project-sdk/blob/2.x/activiti-app-overlay-docker/src/main/docker/Dockerfile-2.2.0) in this path: `activiti-app-overlay-docker/src/main/docker/Dockerfile-2.2.0`
2. Import the [Activiti App Dockerfile for Arm64](https://github.com/OpenPj/alfresco-process-services-project-sdk/blob/2.x/activiti-app-overlay-docker/src/main/docker/Dockerfile-2.2.0-arm64) in this path: `activiti-app-overlay-docker/src/main/docker/Dockerfile-2.2.0-arm64`
3. Import the [Activiti Admin Dockerfile](https://github.com/OpenPj/alfresco-process-services-project-sdk/blob/2.x/activiti-app-overlay-docker/src/main/docker/admin/Dockerfile-admin-2.2.0) in this path: `activiti-app-overlay-docker/src/main/docker/admin/Dockerfile-admin-2.2.0`
4. Import the [Activiti Admin Dockerfile for Arm64](https://github.com/OpenPj/alfresco-process-services-project-sdk/blob/2.x/activiti-app-overlay-docker/src/main/docker/admin/Dockerfile-admin-2.2.0-arm64) in this path: `activiti-app-overlay-docker/src/main/docker/admin/Dockerfile-admin-2.2.0-arm64`
5. Import the [Open API specs](https://github.com/OpenPj/alfresco-process-services-project-sdk/blob/2.x/activiti-app-swagger-client/src/test/resources/api-docs-2.2.0.json) in this path: `activiti-app-swagger-client/src/test/resources/api-docs-2.2.0.json`
6. Import the new `version.properties` in the new path: `aps-extensions-jar/src/test/resources/activiti/2.2.0/version.properties` with the following content:
```
type=bpmSuite
version.major=2
version.minor=2
version.revision=0
version.edition=Alfresco Process Services (powered by Activiti)
```
7. Add the following snippet in the `aps-extensions-jar/pom.xml` inside the `dependencyManagement/dependencies` element:
```xml
<dependency>
	<groupId>org.elasticsearch.client</groupId>
	<artifactId>elasticsearch-rest-high-level-client</artifactId>
	<version>${elasticsearch-rest-client.version}</version>
</dependency>
<dependency>
	<groupId>org.elasticsearch</groupId>
	<artifactId>elasticsearch-x-content</artifactId>
	<version>${elasticsearch.version}</version>
</dependency>
```
8. Add the following snippet in the `aps-extensions-jar/pom.xml` inside the `dependencies` element:
```xml
<dependency>
	<groupId>org.elasticsearch</groupId>
	<artifactId>elasticsearch</artifactId>
	<scope>test</scope>
</dependency>
<dependency>
	<groupId>org.elasticsearch.client</groupId>
	<artifactId>elasticsearch-rest-high-level-client</artifactId>
	<scope>test</scope>
</dependency>
<dependency>
	<groupId>org.elasticsearch</groupId>
	<artifactId>elasticsearch-x-content</artifactId>
	<scope>test</scope>
</dependency>
```
9. Add the new Maven profile snippet in the pom.xml:
```xml
<profile>
	<id>aps2.2.0</id>
	<activation>
		<activeByDefault>true</activeByDefault>
	</activation>

	<properties>
		<!-- APS 2.2.0 -->
		<aps.version>2.2.0</aps.version>
		<aps.docker.version>2.2.0</aps.docker.version>
		<aps.version.major>2</aps.version.major>
		<aps.version.minor>2</aps.version.minor>
		<aps.version.revision>0</aps.version.revision>
		<opencsv.version>5.5.2</opencsv.version>
		<jackson.version>2.13.1</jackson.version>
		<bcprov.version>1.70</bcprov.version>
		<activiti.version>7.1.359</activiti.version>
		<spring.version>5.3.15.RELEASE</spring.version>
		<spring-boot.version>2.5.3</spring-boot.version>
		<spring-security.version>5.6.1</spring-security.version>
		<elasticsearch.version>7.16.3</elasticsearch.version>
		<elasticsearch-rest-client.version>7.16.3</elasticsearch-rest-client.version>
		<hibernate.version>5.4.32.Final</hibernate.version>
		<postgresql.version>13.1</postgresql.version>
		<skipITs>false</skipITs>
		<skip.integration.test>false</skip.integration.test>
		<skip.swagger.client.generation>false</skip.swagger.client.generation>
		<docker-maven-plugin.skip.save>false</docker-maven-plugin.skip.save>
		<docker-maven-plugin.build.save>false</docker-maven-plugin.build.save>
	</properties>
</profile>
```
10. Be sure that in your `pom.xml` there is no other Maven profile active by default, if your previous version was APS 2.1.0 then change the `activation` element in order to disable it as the following:
```xml
...
<profile>
	<id>aps2.1.0</id>
	<activation>
		<activeByDefault>false</activeByDefault>
	</activation>
...
```