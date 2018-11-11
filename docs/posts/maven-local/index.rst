.. title: Managing local jars with maven
.. slug: maven-local
.. date: Jun 02, 2018
.. link: 
.. description:
.. tags: maven
.. category: software engineering


Maven has for me a non user friendly documentation, with 80% of xml content.
For long time I have been stuck in how easily manage both dependency from maven
central and from local jars.

.. END_TEASER

I finally got something quite general and functional. Here is how to make this
working fine:

The pom.xml file:

.. code-block:: xml
        
  <!-- Add the manifest -->
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>2.4</version>
    <configuration>
      <archive>
        <manifest>
          <mainClass>your.class.Main</mainClass>
        </manifest>
      </archive>
    </configuration>
  </plugin>

.. code-block:: xml
        
  <!-- Make a standalone build -->
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>2.2</version>
    <executions>
      <execution>
        <phase>package</phase>
        <goals>
          <goal>shade</goal>
        </goals>
        <configuration>
          <outputFile>
          ${project.build.directory}/${artifactId}-${version}-standalone.jar
          </outputFile>
        </configuration>
      </execution>
    </executions>
  </plugin>

.. code-block:: xml
        
  <!--  import your local jar-->
  <dependency>
    <groupId>org.hsqldb</groupId>
    <artifactId>jdbcDriver</artifactId>
    <version>2.4.1</version>
  </dependency>

.. code-block:: xml
        
  <!-- create a local respository  -->
  <repositories>
    <repository>
      <id>my-local-repo</id>
      <url>file://${basedir}/repos</url>
    </repository>
  </repositories>

By the way one can add dependency jars by such command line. This will populate the *repos* folder:

.. code-block:: bash

        mvn org.apache.maven.plugins:maven-install-plugin:2.3.1:install-file 
        -Dfile=lib/hsqldb.jar 
        -DgroupId='org.hsqldb' 
        -DartifactId='jdbcDriver' 
        -DgeneratePom=true 
        -Dpackaging=jar 
        -Dversion=2.4.1 
        -DlocalRepositoryPath=repos

As a result, the compilation will result as a standalone jar, containing both
local and central dependencies.
