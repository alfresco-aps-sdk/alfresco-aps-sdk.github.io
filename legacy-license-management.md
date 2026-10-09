Starting from APS SDK 2.0.9 and 1.7.4 we added a new license management to make easier the license propagation in terms of classloading for unit testing but also for executing integration tests building the Activiti App container correctly.

If you are currently working on any previous version of APS SDK 2.x or 1.x, now you can add the new License Management to your current APS SDK project applying the following Maven configuration:

1. Create the new `license` folder in the root of the project
2. Add the following `plugin` snippet in the `aps-extensions-jar/pom.xml` inside the `build/plugins` element:
```xml
<plugin>
    <artifactId>maven-resources-plugin</artifactId>
    <executions>
	<execution>
	    <id>copy-license-for-unit-tests</id>
	    <phase>generate-test-resources</phase>
		<goals>
			<goal>copy-resources</goal>
		</goals>
		<configuration>
			<outputDirectory>${project.basedir}/src/test/resources</outputDirectory>
			<overwrite>false</overwrite>
			<resources>
				<resource>
					<directory>${project.parent.basedir}/license</directory>
					<filtering>false</filtering>
					<includes>
						<include>*.lic</include>
					</includes>
				</resource>
			</resources>
		</configuration>
	 </execution>
    </executions>
</plugin>
```
3. Add the following `execution` snippet in the `activiti-app-overlay-docker/pom.xml` inside the `build/plugins/maven-resources-plugin` element:
```xml
<execution>
	<id>copy-license-for-containers-and-ITs</id>
	<phase>pre-integration-test</phase>
	<goals>
		<goal>copy-resources</goal>
	</goals>
	<configuration>
		<outputDirectory>${project.basedir}/src/main/docker/license</outputDirectory>
		<overwrite>false</overwrite>
		<resources>
			<resource>
				<directory>${project.parent.basedir}/license</directory>
				<filtering>false</filtering>
				<includes>
					<include>*.lic</include>
				</includes>
			</resource>
		</resources>
	</configuration>
</execution>
```