# lgtm.com workshop on finding security vulnerabilities

In this workshop, we will use QL to find Java Messaging Service (JMS) deserialization vulnerabilities in the workshop test project.

## Getting started

* Log in to [lgtm.com](lgtm.com) using your Google account (or sign up)
* Go to the [lgtm-workshop](https://lgtm.com/projects/g/adityasharad/lgtm-workshop) project and click “Follow”
* Open the lgtm query console by clicking “Query this project”


Keyboard shortcuts in the query console:
* `Ctrl+Space` for autocomplete suggestions
* `F3` to jump to the definition of a selected element

## Task 1: where to start looking

Write QL queries to find the following:
* The interface whose fully-qualified name is `javax.jms.ObjectMessage`.
* The `getObject` method declared in the interface `javax.jms.ObjectMessage`.
* All classes that implement the interface `javax.jms.ObjectMessage`.
* All methods (declared in the classes from the previous step) that override the `getObject` method from the interface `javax.jms.ObjectMessage`.

You may wish to write predicates or classes that model each of these steps, to make it easier to reuse your logic.

## Task 2: identify vulnerabilities

Where do these `getObject` methods get their input data from? Find all the calls to `ObjectInputStream.readObject()` whose value flows to a return value in one of the `getObject` methods you found in Task 1.

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

## Task 3: eliminate false positives

Are all the results you found actually vulnerable? Look at the surrounding code to distinguish between true and false positives in your results. Which of the sources use an `ObjectInputStream` object that is constructed to restrict deserialization to a set of trusted packages? What do those whitelists look like?

Try to modify your `isSource` predicate to ignore these false positives.

### Hint
Use the Definitions and Uses QL library for Java, which tracks variables and their values. Add the following import statement:
```ql
import semmle.code.java.dataflow.DefUse
```

There are two useful predicates you can call from this library:
* `defUsePair` lets you find a definition of a variable and a use of the same variable value.
* `useUsePair` lets you find two successive accesses to the same variable value.

## References
* lgtm.com: _Deserialization of user-controlled data_ query help. https://lgtm.com/rules/1823453799/
* Matthias Kaiser: _Pwning your Java Messaging with Deserialization Vulnerabilities_, Black Hat USA 2016. https://www.blackhat.com/docs/us-16/materials/us-16-Kaiser-Pwning-Your-Java-Messaging-With-Deserialization-Vulnerabilities.pdf
* Chris Frohoff and Gabriel Lawrence: _OWASP SD: Deserialize My Shorts, Or How I Learned to Start Worrying and Hate Java Object Deserialization._ http://frohoff.github.io/owaspsd-deserialize-my-shorts/