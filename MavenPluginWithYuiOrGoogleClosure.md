# Introduction #
Since 1.3.x branch release, wro4j provides some fixes which allows easily customize the way your resources are compressed.
<br />
**Prerequisites**: [GettingStarted](GettingStarted.md) & [MavenPlugin](MavenPlugin.md)

# Wro4j Maven Plugin with Google Closure #
[Google closure compiler](http://code.google.com/closure/compiler/) is one of the best compressors out there. It parses your JavaScript, analyzes it, removes dead code and rewrites and minimizes what's left. It also checks syntax, variable references, and types, and warns about common JavaScript pitfalls.

**Note:** the wro4j factory invokes Google Closure Compiler with SIMPLE\_OPTIMIZATIONS.

In order to make it work with wro4j maven plugin you have to do the following:

  1. Add wro4j-extensions library to the classpath. It is recommended to use maven for adding this dependency, because other transitive dependencies will be resolved by maven.
```
    <dependency>
      <groupId>ro.isdc.wro4j</groupId>
      <artifactId>wro4j-extensions</artifactId>
      <version>${wro4j.version}</version>
    </dependency>
```

  1. Configure wro4j maven plugin in pom.xml
```
      <plugin>
        <groupId>ro.isdc.wro4j</groupId>
        <artifactId>wro4j-maven-plugin</artifactId>
        <version>${wro4j.version}</version>
        <executions>
          <execution>
            <goals>
              <goal>run</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <wroManagerFactory>ro.isdc.wro.extensions.manager.standalone.GoogleStandaloneManagerFactory</wroManagerFactory>
        </configuration>
      </plugin>
```

Alternatively you can configure the managerFactory from the command line when invoking manually the plugin:
```
mvn wro4j:run -DwroManagerFactory=ro.isdc.wro.extensions.manager.standalone.GoogleStandaloneManagerFactory
```

# Wro4j Maven Plugin with YUICompressor #
[YUICompressor](http://developer.yahoo.com/yui/compressor/) is also very popular tool. Tests on the YUI Library have shown savings of over 20% compared to JSMin (becoming 10% after HTTP compression). It does also compress CSS files, not only javascripts.

In order to make it work with wro4j maven plugin, you have to follow similar steps as with google closure compiler:

  1. Add wro4j-extensions library to the classpath. It is recommended to use maven for adding this dependency, because other transitive dependencies will be resolved by maven.
```
    <dependency>
      <groupId>ro.isdc.wro4j</groupId>
      <artifactId>wro4j-extensions</artifactId>
      <version>${wro4j.version}</version>
    </dependency>
```
  1. Configure wro4j maven plugin in pom.xml
```
      <plugin>
        <groupId>ro.isdc.wro4j</groupId>
        <artifactId>wro4j-maven-plugin</artifactId>
        <version>${wro4j.version}</version>
        <executions>
          <execution>
            <goals>
              <goal>run</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <wroManagerFactory>ro.isdc.wro.extensions.manager.standalone.YUIStandaloneManagerFactory</wroManagerFactory>
        </configuration>
      </plugin>
```

Alternatively you can configure the managerFactory from the command line when invoking manually the plugin:
```
mvn wro4j:run -DwroManagerFactory=ro.isdc.wro.extensions.manager.standalone.YUIStandaloneManagerFactory
```