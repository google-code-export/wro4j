# Introduction #
By default, wro4j maven plugin generates resources with the same name the group has in wro.xml file. For instance, if you have defined a group named **all":
```
<groups>
  <group name="all">
    <css>/path/to/style/*.css</css>
    <js>/path/to/script/*.js</js>
  </group>
</groups>
```
the output target folder (which is easy to configure using _destinationFolder_ plugin parameter), used by wro4j maven plugin, will contain two files: all.css and all.js.**

This page describe how to change this behavior and take control over the how the name of the generated file.

# Details #
The **WroManagerFactory** used by wro4j maven plugin implements the **NamingStrategyAware** interface:
```
public interface NamingStrategyAware {
  public abstract NamingStrategy getNamingStrategy();
}
```

This interface allow to configure the **NamingStrategy** used after processing is done in maven plugin mojo.

The **NamingStrategy** interface define how resources should be named, it can be used also for versioning or any other type of naming strategy:

```
public interface NamingStrategy {
  public String rename(final String originalName, final InputStream inputStream) throws IOException;
}
```

By default, a couple of implementation of this interface is provided, these are:
  * **NoOpNamingStrategy** - as its name states, it doesn't change the result name of the resource
  * **DefaultHashEncoderNamingStrategy** - encodes a hash into file name, where hash is generated based on resource content. By default the CRC-32 algorithm is used to compute the hash, but this can be easily switched with other algorithms. When this strategy is used, the **all.css** can renamed to something like **all-253f2eba.css**, where **253f2eba** is a CRC-32 checksum of the css resource.
  * **TimestampNamingStrategy** - the strategy which encodes the timestamp into the bundle name.

You can create a custom implementation of this interface, which can encode a version or a timestamp.

## Using Custom NamingStrategy ##
In order to change the naming strategy feature, you have to use a custom **WroManagerFactory** with the **NamingStrategy** implementation set:

```
public class CustomWroManagerFactory
    extends DefaultStandaloneContextAwareManagerFactory {
  public CustomWroManagerFactory() {
    setNamingStrategy(new DefaultHashEncoderNamingStrategy());
  }
}
```

In this example the **DefaultHashEncoderNamingStrategy** is used. You can change this one with a different strategy implementation which suites your needs.

The last step is to configure the **CustomWroManagerFactory** in pom.xml:

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
          <ignoreMissingResources>true</ignoreMissingResources>            
           <wroManagerFactory>test.CustomWroManagerFactory</wroManagerFactory>
        </configuration>
      </plugin>

```