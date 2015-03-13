# Introduction #
Since release 1.4.3 it is possible to register LifecycleCallback (aka callback). A callback is an interface exposing methods which are invoked during different wro4j processing phases. This might be useful if you want to perform some kind of benchmarking or want to add a custom logic at some particular point.

# Details #
The LifecycleCallback interface expose the following methods:

```
public interface LifecycleCallback {
  /**
   * Invoked before starting model creation.
   */
  void onBeforeModelCreated();

  /**
   * Invoked after the model is created.
   */
  void onAfterModelCreated();

  /**
   * Called before each resource is processed.
   */
  void onBeforePreProcess();

  /**
   * Called after a resource is pre processed.
   */
  void onAfterPreProcess();

  /**
   * Called before a resource is post processed.
   */
  void onBeforePostProcess();

  /**
   * Called after a resource is post processed.
   */
  void onAfterPostProcess();
  
  /**
   * Called before resources are merged and before any processing is applied.
   */
  void onBeforeMerge();
  
  /**
   * Called after all resources are merged and the preProcessing is completed.
   */
  void onAfterMerge();
  /**
   * Called after all postProcessors are applied and overall processing is complete.
   */
  void onProcessingComplete();
}
```

## Creating Custom LifecycleCallback ##
When creating a custom LifecycleCallback, it is very unlikely that you will want to override all of the methods. That is why, you can extend LifecycleCallbackSupport class which overrides all the methods for you. This simplifies the development. For instance, if you want to override hte `onProcessingComplete()` method only, your implementation would look like this:

```
public CustomCallback extends LifecycleCallbackSupport {
  public void onProcessingComplete() {
    //do something
  }
}
```

The wro4j-core module provides a sample implementation of a callback called `PerformanceLoggerCallback` which logs the amount of time spent during model creation, pre processing and post processing.

## Registering Custom LifecycleCallback ##
In order to register a callback, you have to call:

```
LifecycleCallback callback = //create custom callback
WroManager manager = //..
manager.getCallbackRegistry().registerCallback(callback);
```

Alternatively, register the callback in your custom managerFactory:

```
public class CustomExtensionsWroManagerFactory
    extends ConfigurableWroManagerFactory {
  @Override
  protected void onAfterInitializeManager(final WroManager manager) {
    manager.registerCallback(new PerformanceLoggerCallback());
  }
}
```