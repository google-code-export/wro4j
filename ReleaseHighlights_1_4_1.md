# Introduction #
wro4j-1.4.1 is a minor release of 1.4.x branch, containing several small fixes and improvements. You can easily migrate to 1.4.1 from 1.4.0, because it is backward compatible. For a complete list of fixed issues, check-out the [release notes](ReleaseNotes.md) page.

Small note: if you are not using maven for dependency management and add wro4j manually into your classpath, be sure you add other transitive dependencies used by wro4j-extensions module (ex: [dojo-shrinksafe.jar](http://search.maven.org/remotecontent?filepath=org/dojotoolkit/dojo-shrinksafe/1.6.1/dojo-shrinksafe-1.6.1.jar))


# Details #
Below are described the most notable features & improvements of the 1.4.0 release.

## Maven plugin and jshint, csslint goals ##
When you use wro4j maven plugin with custom wroManagerFactory implementation with one of the following goals: jshint or csslint, only jshint (or cssLint) processor will be used even if you have defined a different processorsFactory.

## Upgrades ##
The following processors have been updated to use latest versions of embedded libraries:
  * jshint
  * csslint
  * uglifyJs
  * coffeeScript

## Create ExtensionAwareProcessorDecorator ##
ExtensionAwareProcessorDecorator is a decorator for other processors used to simplify the way you restrict the usage of some processors on resources having a specific extension.

One use-case is, using CoffeeScriptProcessor on resources having `*.coffee` extension. This can be achieved this way:
```
    ResourcePreProcessor processor = ExtensionsAwareProcessorDecorator.decorate(new CoffeeScriptProcessor()).addExtension(
        "coffee");
```

When a post processor is decorated, there is no restriction regarding the processing. In other words, when resource extension is unknown, the processor is applied with no restrictions.

Also, this decorator is used by default when you configure processors with alias (using ConfigurableWroFilter or similar (wro.properties file, wro4j-runner command line configuration). For example, the same as the above code can be achieved with:
```
preProcessors=coffeeScript.coffee
```
Notice the following pattern:
```
<ALIAS>.<EXTENSION>
```
The processor associated with `ALIAS` will be processed only on resources having specified extension.

## Extend JsHint option configuration ##
Until now, jsHint processor allowed only options of the following format:
```
devel, curly, forin, immed
```
Now the following options are also accepted:
```
predef=['YUI'], maxerr=1000
```
These options are a valid type of configuration options for jsHint.