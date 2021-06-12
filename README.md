This is a sample project that goes along with [my Medium article](https://medium.com/@itzgeoff/including-react-in-your-spring-boot-maven-build-ae3b8f8826e) that shows how to build a [ReactJS](https://reactjs.org/) UI and Spring Boot backend with Maven. [This Spring Guide](https://spring.io/guides/tutorials/react-and-spring-data-rest/) provides a similar description of a hybrid technology/build.

## Build

To build a jar that includes the production build of the ReactJS code, run

```shell
./mvnw package
```

## Bootstrapping your own project like this one

### Spring Boot + Maven content

Initialize your project with [Spring Initializr](https://start.spring.io/) or the integration thereof in your favorite IDE.

### ReactJS module setup

From a terminal, go into the `src/main` directory of this project and use [create-react-app](https://create-react-app.dev/) to bootstrap the ReactJS content:

```shell
npx create-react-app ui
```

In the created `ui` directory, create the file `.env` with the content

```
BUILD_PATH = ../../../target/classes/public
```

### Frontend Maven Plugin setup

In the `pom.xml` add the following two properties where `node.version` should be changed to the desired NodeJS version:

```xml
    <ui.directory>${project.basedir}/src/main/ui</ui.directory>
    <node.version>16.1.0</node.version>
```

In the build->plugins section of the `pom.xml` add [the frontend maven plugin](https://github.com/eirslett/frontend-maven-plugin) with the version updated to the latest:

```xml
      <plugin>
        <artifactId>frontend-maven-plugin</artifactId>
        <groupId>com.github.eirslett</groupId>
        <!-- update this version to the latest -->
        <version>1.12.0</version>
  
        <executions>
          
          <execution>
            <id>install node and npm</id>
            <goals>
              <goal>install-node-and-npm</goal>
            </goals>
          </execution>
          
          <execution>
            <id>react test</id>
            <goals>
              <goal>npm</goal>
            </goals>
            <phase>test</phase>
            <configuration>
              <arguments>test</arguments>
              <workingDirectory>${ui.directory}</workingDirectory>
              <environmentVariables>
                <!-- Enable non-watch mode -->
                <CI>true</CI>
              </environmentVariables>
            </configuration>
          </execution>
          
          <execution>
            <id>react build</id>
            <goals>
              <goal>npm</goal>
            </goals>
            <phase>prepare-package</phase>
            <configuration>
              <arguments>run build</arguments>
              <workingDirectory>${ui.directory}</workingDirectory>
            </configuration>
          </execution>
          
        </executions>
    
        <configuration>
          <nodeVersion>v${node.version}</nodeVersion>
          <installDirectory>${project.build.directory}</installDirectory>
        </configuration>
      </plugin>
```