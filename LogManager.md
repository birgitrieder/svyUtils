# Introduction
Every application includes log statements, either to log runtime issues for later analysis or to debug issues while developing. Observation shows that in some applications log statements alone make up up to 4% of the entire code base.

Logging does have its drawbacks. It can slow down an application and when logging is verbose, it can be hard to find the relevant log statements.

Withing Servoy logging is done through the application.output(message, [Level]) method, which takes the message to log and an optional parameter to specify the Log Level for the specific Log statement. The actual Log level threshold is determined on client (in case of the Smart Client) or on application server (in case of the Web, BatchProcessor or Headless Client) level. This has several drawbacks:
* There is no mechanism to enable logging for a specific part of the code while disabling other parts
* There is no way to know in code which is the active Log Level, thus log statements that require (complex) String concatenation to build up the Log message cannot be executed conditionally. This means that the overhead of the String concatenation is always there, even if the level of the Log statement is such that the Log statement will be ignored
* There is no option to specify a different log configuration for a specific client
* The action of inserting a Log statement for a certain level requires specifying a second parameter, which is A: often forgotten and B: takes a few extra steps while coding

The LogManager part of svyUtils is designed to:
1. Reduce the overhead of Log statements 
2. Allow fine-grained control over which Log statements result in actual logged messages at runtime
3. Provide an easy to use API, to make inserting log  statements as straight forward as possible

The LogManager started live as a port of the open source [log4javascript](http://log4javascript.org/) project, which in turn is based on the [log4j](http://logging.apache.org/log4j/2.x/) project. In it current incarnation, the LogManager is loosely based on log4javascript, as it has been heavily adapted to align itself with the upcoming log4j 2 version and to improve performance and encapsulation.

# Quick start
Want to get started and do the reading later? Check out the following snippet:
```javascript
var log = scopes.svyLogManager.getLogger('com.mycompany.mysolution')
log.debug("Hello, I'm debuggin'")
var name = 'you'
log.trace("Hello {}", name) //outputs "Hello you":
```

# Architecture
The LogManager's architecture consists of Loggers, Appenders and Layouts. Loggers expose the API for developers to log messages. Loggers can have Appenders, which determine where log messages are send to. An Appender has a Layout which determines how the log message is formatted.

### Logger Hierarchy
In order to allow enabling or disabling the logging for certain parts of the code, Loggers allow developers to categorize and group log statements. Loggers are categorized based on their name and the name case-sensitive.

Loggers are hierarchical, based on their name, which follows the dot notation. For example a logger with the name 'com.foo' is the parent of a logger with the name ```'com.foo.Bar'``` and a logger with the name ```'com'``` is the ancestor of both.

Loggers are retrieved using the ```scopes.svyLogManager.getLogger``` method, passing the name of the desired logger as parameter.

At the top of the hierarchy is the so-called RootLogger, which can be retrieved using ```scopes.svyLogManager.getLogger(scopes.svyLogManager.ROOT_LOGGER_NAME)```

### Loggers
As stated before, Loggers are retrieved using the ```scopes.svyLogManager.getLogger``` method, passing the name of the desired logger as parameter. Loggers expose the API to log messages for the available Log Levels (OFF, FATAL, ERROR, WARNING, INFO, DEBUG, TRACE, ALL) and the API to see if a certain Log Level is enabled for the Logger.

Loggers are configured through the ```scopes.svyLogManager.loadConfig``` method, which takes a JavaScript object of type ```scopes.svyLogManager.CONFIG_TYPE_DEF``` as parameter.
 
# API

# Configuration
 
# Plugins