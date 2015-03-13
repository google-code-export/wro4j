# Introduction #
Starting with 1.4.0 release, wro4j provides 3 types of DSL for building WroModel: xml, groovy & json.

SmartWroModelFactory was created in order to simplify the way you can use any of existing DSL.

# Details #
SmartWroModelFactory uses a composite pattern and tries to create WroModel based on provided stream using all existing DSL. The flow is:
  1. Use [XmlModelFactory](WroFileFormat.md) to create model. If creation fails, go to next step.
  1. Use [GroovyModelFactory](GroovyWroModel.md) to create model. If creation fails, go to next step.
  1. Use [JsonModelFactory](JsonWroModel.md) to create model. If creation fails, the overall model creation fails.

This WroModelFactory is used by default by BaseWroManager. As a result, you can easily switch between xml to groovy DSL, just by replacing wro.xml with wro.groovy. Similar applies to json DSL, which expects a file named wro.json. The file name autodetection works only if you do not explicitly specify the name of the wro file. Otherwise, it will use only the specified file to build the WroModel.