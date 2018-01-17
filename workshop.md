# lgtm.com workshop on finding security vulnerabilities

In this workshop, we will use QL to find Java Messaging Service (JMS) deserialization vulnerabilities in the workshop test project. There are three tasks: complete as many as you can to score points, and don't hesitate to ask the Semmle team for help with writing QL!

## Getting started

* Log in to lgtm.com using your Google account (or sign up)
* Search for project `lgtm-workshop`
* Click “View Project”, then “Follow”
* Open the lgtm query console by clicking “Query this project”


Keyboard shortcuts in the query console:
* `Ctrl+Space` for autocomplete suggestions
* `F3` to jump to the definition of a selected element

## Task 1: where to start looking

Use QL to find the interface `javax.jms.ObjectMessage` and all classes that are its subtypes.

Next, in these classes, find all the methods that implement (override or instantiate) the `getObject` method declared in the interface `javax.jms.ObjectMessage`.

### Scoring
5 points per method.

## Task 2: identify vulnerabilities

Where do these `getObject` methods get their input data from? Find all the calls to `ObjectInputStream.readObject()` whose value flows to a return value in one of the `getObject` methods you found in  Task 1.

### Hint
There is already a class called `ReadObjectMethod` in the standard library.

### Hint
The standard lgtm.com deserialization query works by defining a _taint-tracking configuration_. Such a configuration defines the _sources_ of tainted data and the _sinks_ that should not receive such tainted data.

Add the following import statement:
```ql
import semmle.code.java.dataflow.TaintTracking
```

Then fill in the following outline to create your own configuration.

```ql
class JMSConfig extends TaintTracking::Configuration {
  /** Any name here will do. */
  JMSConfig() { this = "JMSConfig" }
  
  /** Change this predicate to define what sources of untrusted data you want to look for. */
  override predicate isSource(DataFlow::Node source) {
    // TODO
  }
  
  /** Change this predicate to define sinks, i.e. places you want to check for the presence of untrusted data. */
  override predicate isSink(DataFlow::Node sink) {
    // TODO
  }
}
```

The final query you use may look something like:
```ql
from JMSConfig config, DataFlow::Node source, DataFlow::Node sink
where config.hasFlow(source, sink)
select source, sink
```

### Scoring
10 points per valid source and sink pair.

## Task 3: eliminate false positives

Are all the results you found actually vulnerable? Look at the surrounding code to distinguish between true and false positives in your results. Which of the sources have whitelists on the `ObjectInputStream` that restrict deserialization to a set of trusted packages? What do those whitelists look like?

Try to modify your `isSource` predicate to ignore these false positives.

### Hint
Use the Definitions and Uses QL library for Java, which tracks variables and their values. Add the following import statement:
```ql
import semmle.code.java.dataflow.DefUse
```

There are two useful predicates you can call from this library:
* `defUsePair` lets you find a definition of a variable and a use of the same variable value.
* `useUsePair` lets you find two successive accesses to the same variable value.

### Scoring
10 points for identifying each true positive from Task 2.

5 points for each false positive from Task 2 that your improved query ignores.