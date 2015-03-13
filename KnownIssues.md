# Introduction #
This page describes any possible issues you may have with wro4j while integrating in your web application.

## JsMinProcessor fails during minimization ##
The JsMinProcessor fails during minimization if the script contains an expression which contains new lines. Example:
```
Math.round(4
	/3);
```

The solution is to write the expression in a single line or use a different minimizer.

## Parameters in included js ##
If you include in your group something like this:

```
  <group name="roller-js"> 
    <js>/themes/andreas08/scripts/prototype.js</js> 
    <js>/themes/andreas08/scripts/scriptaculous.js?load=effects</js>
    ..... 
  </group>
```
you may have some troubles. This is because the request for scriptaculous?load=effects loads a js which add another resource to the head
```
<script type="text/javascript" src="http://www.mysite.com/themes/andreas08/scripts/effects.js"></script> 
```
In the case of wro4j you do not have any parameters. Anyway, the above approach increase the number of request.. and as in case of @import statement for css, this is something you should try to avoid. The solution is to include each js resource separately:

```
<script type="text/javascript" src="/themes/andreas08/scripts/prototype.js"></script> 
<script type="text/javascript" src="/themes/andreas08/scripts/scriptaculous.js"></script> 
<script type="text/javascript" src="http://www.mysite.com/themes/andreas08/scripts/effects.js"></script> 
```

## YUIJs processor is broken in release 1.4.6 ##
The problem is that YUI require older version of Rhino. There is an open issue on YUI for migrating to Rhino 1.7R3, but it doesn't seem to be a priority for them.
The recommendation is to use a different minifier, there are plenty of alternatives.

The YuiJsCompressor processor was removed from wro4j since 1.5.0.

# Fixed known issues #

## Using wildcard pattern for css resources ##
This issue was fixed since >= 1.4.0 release.

It is not recommended to use deep recursive wildcard pattern **`**`** (double asterisk) when declaring css resources in the group model. For example:
```
  <group name="NotOk">
    <css>/static/css/**.css</css>
  </group>
```
The problem with this is that it will include all files with css extension from /static/css folder and its subfolders and there is a potential risk that relative background url will not be rewritten properly.

However, you can do this to avoid the problem:

```
  <group name="Ok">
    <css>/static/css/*.css</css>
    <css>/static/css/folder1/*.css</css>
    <css>/static/css/folder2/*.css</css>
    <css>/static/css/folder3/*.css</css>
  </group>
```

By specifying each folder explicitly, you can be sure that the relative url backgrounds will be overwritten correctly.



## CssUrlRewritingProcessor issue ##
The CssUrlRewritingProcessor handles the rewriting of background url's in css files after merging. It works as expected except of one case:
when using this processor after the CssImportPreProcessor. Fixing this issue is easy: use the CssImportPreProcessor after the CssUrlRewritingProcessor.

The reason of misbehavior is that when applying CssImportPreProcessor, wro4j will apply all pre processors on imported css resources. Thus, applying the CssUrlRewritingProcessor  afterwards would mean that the same resource is processed twice - which may cause unexpected results.

In conclusion, use CssUrlRewritingProcessor before CssImportPreProcessor.

Wrong Order:
```
preProcessors = cssImport, cssUrlRewriting
```

Correct Order:
```
preProcessors = cssUrlRewriting, cssImport
```

## XmlModelFactory cannot build valid model and throws `java.lang.ArrayStoreException` ##
The problem may occure when running the web application on websphere container. The reason is the existence of some dependency containing `javax.xml.transform.stream.StreamSource` class (example: jaxrpc-11.5r2-upgraded.jar or xml-apis-1.3.03.jar). Depending on the order used by classloader to load the class the problem may or may not happen.

The solution is to remove the offending dependency or change the order used by servlet container.

Read more details about this issue: [issue371](https://code.google.com/p/wro4j/issues/detail?id=371)

## CssDataUri & CssMin issue ##
When using CssDataUri processor together with cssMin, the processing fails.
The reason is that CssMin processor fails to minimized DataURI's. The workaround is to use cssMin before cssDataUri processor.

Read more about this issue: [issue609](https://code.google.com/p/wro4j/issues/detail?id=609)