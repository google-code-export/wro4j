# Introduction #
Rhino is an open-source implementation of JavaScript written entirely in Java.

Rhino is used in wro4j in implementation of the following processors:
CoffeeScript, LessCss, SassCss, JsHint, JsLint, CssLint, UglifyJs, PackerJs, etc.
One problem with Rhino is that it is slow comparing to native javascript processing.

This page describes how rhino based processors were improved using various tricks and a benchmark proving that newer versions of wro4j (since >= 1.4.2) are faster.

# Details #
The time taken by a processor using Rhino is
directly proportional to amount of script to be interpreted. For instance LessCssProcessor uses [less.js](https://github.com/cloudhead/less.js/) script to parse a less script and transform it into vanilla css.


## Reusing ScriptableObject ##
One possible solution to improve the performance it is to reuse the ScriptableObject (an object from Rhino API). That means that once the less.js script is loaded into Rhino scope, it can be reused for future executions. Thus, multiple subsequent calls would take less.

A sample implementation of this can be found in LessCss.java:

```
  private ScriptableObject scope;

  /**
   * Initialize script builder for evaluation.
   */
  private RhinoScriptBuilder initScriptBuilder() {
    try {
      RhinoScriptBuilder builder = null;
      if (scope == null) {
        final InputStream initStream = LessCss.class.getResourceAsStream(SCRIPT_INIT);
        final InputStream runStream = LessCss.class.getResourceAsStream(SCRIPT_RUN);
        builder = RhinoScriptBuilder.newClientSideAwareChain().evaluateChain(initStream, SCRIPT_INIT).evaluateChain(
          getScriptAsStream(), DEFAULT_LESS_JS).evaluateChain(runStream, SCRIPT_RUN);
        scope = builder.getScope();
      } else {
        builder = RhinoScriptBuilder.newChain(scope);
      }
      return builder;
    } catch (final IOException ex) {
      throw new IllegalStateException("Failed reading javascript less.js", ex);
    } catch (final Exception e) {
      LOG.error("Processing error:" + e.getMessage(), e);
      throw new WroRuntimeException("Processing error", e);
    }
  }
```

The implementation loads the main script only once and reuses the same scope for subsequent calls.

## Using a Pool of Rhino Objects ##
A further step of optimization is to use a pool (GenericObjectPool from commons-pool library) of Rhino objects which are reused during processing. This improves the performance especially in multithreaded environment, which is very important since Rhino is not thread-safe by default.

An example of how LessCssProcessor uses this technique is shown here:

```
  private ObjectPoolHelper<LessCss> enginePool;
  public LessCssProcessor() {
    enginePool = new ObjectPoolHelper<LessCss>(new ObjectFactory<LessCss>() {
      @Override
      public LessCss create() {
        return newLessCss();
      }
    });
  }
  public void process(final Resource resource, final Reader reader, final Writer writer) throws IOException {
    final LessCss lessCss = enginePool.getObject();
    try {
      //use lessCss rhino to process the code
    } finally {
      enginePool.returnObject(lessCss);
    }
  }
```

The above code abstracts the pool usage in the class called ObjectPoolHelper. This class hides details about creation and configuration of GenericObjectPool. All it expose is two simple methods:

```
public T getObject();
public void returnObject(final T object);
```

The client is responsible to call `getObject()` whenever he needs to access the object from the pool. Also, it is important to put the object back to the pool once it is not required anymore with `returnObject(object)`.

# Benchmark #
In order to prove that using these techniques has a benefic effect on performance, a benchmark with real  numbers is the best way to prove it.

The benchmark test does the following: use LessCssProcessor to process a number of files (more exactly 23 files) from a folder. The time spent on processing all files is logged. There are 4 types of tests:

  1. Pool is disabled (each LessCssProcessor execution uses a fresh instance of LessCss).
  1. Pool is enabled
  1. Concurrent test with Pool disabled
  1. Concurrent test with Pool enabled

The benchmark has a couple of variables, these are:
  1. numberOfTests - how many times the test should be run
  1. threadPoolSize - the size of the thread pool used when the concurrent tests is performed

Based on above variables, these are the results obtained on a machine with the following configuration:
  * Processor: 8 CPUs 3.4GHz.
  * RAM: 8GB

## Synchronous execution ##
The below results describes a synchronous execution.
Each test process 23 files.
The X asis represents the number of times the same test is performed.
The Y axis represents the number of milliseconds required for the test to complete.

The results when the test is performed 10 times.

http://wiki.wro4j.googlecode.com/git/img/benchmark/benchmark1.PNG

The results when the test is performed 20 times.

http://wiki.wro4j.googlecode.com/git/img/benchmark/benchmark2.PNG

The results when the test is performed 30 times.

http://wiki.wro4j.googlecode.com/git/img/benchmark/benchmark3.PNG

## Concurrent execution ##
The below results describes concurrent execution.
Each test process 23 files.
The X asis represents the number of times the same test is performed.
The Y axis represents the number of milliseconds required for the test to complete.

The results when the test is performed using a thread pool with size 1 - this is similar to using synchronous execution and has bad performance results.

http://wiki.wro4j.googlecode.com/git/img/benchmark/benchmark-concurrent1.PNG

The results when the test is performed using a thread pool with size 4 - meaning that 4 concurrent threads are processing in parallel. The results are better than in first case.

http://wiki.wro4j.googlecode.com/git/img/benchmark/benchmark-concurrent2.PNG

The results when the test is performed using a thread pool with size 8. The results are the best, since the pool size equals to the number of cores.

http://wiki.wro4j.googlecode.com/git/img/benchmark/benchmark-concurrent3.PNG

# Conclusions #
  * Using object pool for rhino based processing has a good impact on performance improvement.
  * When running synchronous benchmark processing, there is a performance penalty at the beginning. But the performance is better after a small warmup.
  * On the long run, using the pool can be about twice as faster when comparing to no-pool processor.
  * The results can vary based on hardware on which the benchmark is run.