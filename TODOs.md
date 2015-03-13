# TODO #
This page will contain a list of features which are planned to be developed in future versions.

### Rest API ###

### ParameterSourceProvider ###
  * Implement a ParameterSource Provider for a more generic parametrization support.
In more detail:
Let the developer specify where to take the values of the parameters from: Eg. request, session, application or some registered object instance (somehow configurable from wro.xml.)
In case when no ParameterSource is registered in wro.xml, we mai assume some defaults just like in JSP (request->session->application)
### Gzip on/off parameter ###
  * Turn gzip ON/OFF using some request parameter (ex: /wro/group.css?gzip=on)

### Cache discrete groups ###
### Create processor for image flicker bug ###

```
      try {
        document.execCommand("BackgroundImageCache", false, true);
      } catch(err) {}
```


# Resolved #
### Unavailable Resources ###
Check what happens when unavailable resource is included
### Processors Management ###
  * Expose append (pre&post processors) methods in WroManageFactoryImpl.
### SemicolonAppenderPreProcessor ###
  * Since some JavaScript files do not end with a semicolon ( they can cause problems when joined with onother JavaScript file.
  * Solution: On each joining point output a semicolon character if the current file does not end with semicolon.
### Remove the logic from the filter class ###
### Develop the following feature: WroManagerFactory configuration using xml ###

### Add a settings property: ignoreInvalidResources ###
(by default false). When this value is true, the invalid resources will be ignored & will not affect the merged result.

### Add css extension ability ###
> .module {}
> .saleModule {extends:module;}


### Create maven repository in google code ###
http://babyloncandle.blogspot.com/2009/04/deploying-maven-artifacts-to-googlecode.html

### Create Css pre processor for dynamic variables ###

# Deprecated features (will not be supported) #
  * Add condition attribute to js, css & group tags in xml. In order to be able to include resources based on some logic. The default implementation will provide browser dependent inclusion. Example:

```
<js condition="isBrowser(ie,ff,chrome)">..</js>
```

The same idea can be applied to other custom logic which can be easily plugable:

```
<js condition="isEnvironment(DEVELOPMENT)">..</js>
```

The plugability will be possible by implementing a ConditionProcessor interface & adding this implementation to factory holding all existing conditions (same as for (pre|post)processors).