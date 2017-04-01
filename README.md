# Citurs Setup with Maven

In this tutorial we will setup a project with Maven and configure the Maven POM to execute all Citrus tests during the Maven integration-test phase.

# Preconditions
- Java 8 or higher
- Maven 3.0.x or higher

# Maven project
First of all we create a new Java project called citrus-sample. We use the Maven command line tool in combination with Maven’s archetype plugin. 
```
D:\2017\hub\citrus-setup-maven>mvn archetype:generate -DarchetypeCatalog=http://
citrusframework.org
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building Maven Stub Project (No POM) 1
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] >>> maven-archetype-plugin:3.0.0:generate (default-cli) > generate-source
s @ standalone-pom >>>
[INFO]
[INFO] <<< maven-archetype-plugin:3.0.0:generate (default-cli) < generate-source
s @ standalone-pom <<<
[INFO]
[INFO] --- maven-archetype-plugin:3.0.0:generate (default-cli) @ standalone-pom
---
[INFO] Generating project in Interactive mode
[INFO] No catalog defined. Using internal catalog
[INFO] No archetype defined. Using maven-archetype-quickstart (org.apache.maven.
archetypes:maven-archetype-quickstart:1.0)
Choose archetype:
1: internal -> org.apache.maven.archetypes:maven-archetype-archetype (An archety
pe which contains a sample archetype.)
2: internal -> org.apache.maven.archetypes:maven-archetype-j2ee-simple (An arche
type which contains a simplifed sample J2EE application.)
3: internal -> org.apache.maven.archetypes:maven-archetype-plugin (An archetype
which contains a sample Maven plugin.)
4: internal -> org.apache.maven.archetypes:maven-archetype-plugin-site (An arche
type which contains a sample Maven plugin site.
      This archetype can be layered upon an existing Maven plugin project.)
5: internal -> org.apache.maven.archetypes:maven-archetype-portlet (An archetype
 which contains a sample JSR-268 Portlet.)
6: internal -> org.apache.maven.archetypes:maven-archetype-profiles ()
7: internal -> org.apache.maven.archetypes:maven-archetype-quickstart (An archet
ype which contains a sample Maven project.)
8: internal -> org.apache.maven.archetypes:maven-archetype-site (An archetype wh
ich contains a sample Maven site which demonstrates
      some of the supported document types like APT, XDoc, and FML and demonstra
tes how
      to i18n your site. This archetype can be layered upon an existing Maven pr
oject.)
9: internal -> org.apache.maven.archetypes:maven-archetype-site-simple (An arche
type which contains a sample Maven site.)
10: internal -> org.apache.maven.archetypes:maven-archetype-webapp (An archetype
 which contains a sample Maven Webapp project.)
Choose a number or apply filter (format: [groupId:]artifactId, case sensitive co
ntains): 7:7
Downloading: https://repo.maven.apache.org/maven2/org/apache/maven/archetypes/ma
ven-archetype-bundles/4/maven-archetype-bundles-4.pom
Downloaded: https://repo.maven.apache.org/maven2/org/apache/maven/archetypes/mav
en-archetype-bundles/4/maven-archetype-bundles-4.pom (4 KB at 1.3 KB/sec)
Downloading: https://repo.maven.apache.org/maven2/org/apache/maven/archetype/mav
en-archetype/2.0-alpha-5/maven-archetype-2.0-alpha-5.pom
Downloaded: https://repo.maven.apache.org/maven2/org/apache/maven/archetype/mave
n-archetype/2.0-alpha-5/maven-archetype-2.0-alpha-5.pom (9 KB at 12.6 KB/sec)
Define value for property 'groupId': user.nagendra.citrus.samples
Define value for property 'artifactId': citrus-sample
Define value for property 'version' 1.0-SNAPSHOT: :
Define value for property 'package' user.nagendra.citrus.samples: :
Confirm properties configuration:
groupId: user.nagendra.citrus.samples
artifactId: citrus-sample
version: 1.0-SNAPSHOT
package: user.nagendra.citrus.samples
```

Citrus provides custom Maven archetypes. We load the archetype information from http://citrusframework.org and choose the Citrus basic archetype. Now you have to define several values for your project: the groupId, the artifactId, the package and the project version. After that we are done! Maven created a Citrus project structure for us which is ready for testing. You should see the following basic project folder structure.

```
citrus-sample
  |   + src
  |   |   + main
  |   |    |   + java
  |   |    |   + resources
  |   |   + test
  |   |    |   + java
  |   |    |   + resources
  pom.xml
  ```
  
  The Citrus project is absolutely ready for testing. With Maven we can build, package, install and test our project right away without any adjustments. The project comes with a sample Citrus test SampleIT. You can find this test in src/test/resources; and src/test/java. The Citrus test was automatically executed in the integration test phase in Maven project lifecycle.
  
  The Citrus project libraries are loaded as dependencies in our Maven POM.
  
The surefire and failsafe plugin configuration is responsible for executing all available tests in your project. Unit tests are traditionally executed with maven-surefire. Integration tests are executed with maven-failsafe. As Citrus tests are integration tests these tests will be executed in the Maven integration-test phase using maven-failsafe:

```
<plugin>
        <groupId>com.consol.citrus.mvn</groupId>
        <artifactId>citrus-maven-plugin</artifactId>
        <version>2.7.1</version>
        <configuration>
          <author>Nagendra Padamara</author>
          <targetPackage>com.consol.citrus</targetPackage>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
          <encoding>${project.build.sourceEncoding}</encoding>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.19.1</version>
        <configuration>
          <forkMode>once</forkMode>
          <failIfNoTests>false</failIfNoTests>
          <excludes>
            <exclude>**/IT*.java</exclude>
            <exclude>**/*IT.java</exclude>
          </excludes>
          <workingDirectory>${project.build.directory}</workingDirectory>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-failsafe-plugin</artifactId>
        <version>2.19.1</version>
        <executions>
          <execution>
            <id>integration-tests</id>
            <goals>
              <goal>integration-test</goal>
              <goal>verify</goal>
            </goals>
            <configuration>
              <argLine>${failsafeArgLine}</argLine>
              <failIfNoTests>false</failIfNoTests>
            </configuration>
          </execution>
        </executions>
      </plugin>
```

The tests are separated by naming convention. All integration tests follow the **/IT.java** or ****/IT.java naming pattern. These tests are explicitly excluded from surefire plugin. This makes sure that the tests are not executed twice. Now you are ready to use both unit and integration tests in your Maven project.

Another notable POM configuration is the repository section. The Citrus stable release versions are available on Maven central repository. So Maven will automatically download the artifacts from that servers. Now if you want to use the latest snapshot preview version of Citrus you need to add the ConSol Labs repository in your POM.

# Run

The sample application uses Maven as build tool. So you can use the Maven commands to compile, package and test the sample.
```
mvn clean install
```

Congratulations! You just have built the complete project and you also have executed the first Citrus tests in your project.

If you just want to execute the Citrus tests with a Maven build execute
```
mvn integration-test
```
This executes all Citrus test cases during the build and you will see Citrus performing some integration test logging output. You can also execute single test cases by defining the test name as Maven system property.

```
                        Thanks for reading!
```

