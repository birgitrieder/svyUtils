## Introduction
This module provides a set of extra appenders for the svyLogManager scope. Currently, these appenders are available:

* [RollingFileAppender](#rollingfileappender): logs to file
* [DbAppender](#dbappender): log to a database table
* [EmailAppender](#emailappender): send log events via email
* [PopupAppender](#popupappender): logs to a small console window

## Adding appenders to the svyLogManager configuration

Each appender needs to be added to the svyLogManager config as a plugin and appender. The default config for svyLogManager looks like this

```javascript
{
	status: "error", 
	appenders: [
		{
			type: "ApplicationOutputAppender", 
			name: "ApplicationOutput",
			PatternLayout: { 
				pattern: "%5level %logger{1.} - %msg" 
			}
		}
	],
	loggers: {
		root: { 
			level: "error", 
			AppenderRef: { 
				ref: "ApplicationOutput" 
			}
		}
	}
}
```

In this example, only the default ApplicationOutputAppender is used to output log events. To add another appender, a plugin needs to be registered and a config for the appender needs to be added as this example:

```javascript
{
		status: "error",
		plugins: 'scopes.svyLogManager$rollingFileAppender.RollingFileAppender',
		appenders: [{
			type: "ApplicationOutputAppender",
			name: "ApplicationOutput",
			PatternLayout: {
				pattern: "%5level %logger{1.} - %msg"
			}
		}, {
			type: "scopes.svyLogManager$rollingFileAppender.RollingFileAppender",
			name: "RollingFileAppender",
			fileName: "logs/application.log",
			maxFileSize: 10485760,
			maxBackupIndex: 5,
			PatternLayout: {
				pattern: "%date %5level %30logger - %msg"
			}
		}],
		loggers: {
			root: {
				level: "info",
				AppenderRef: [{ref: "RollingFileAppender", level: "warn"}, {ref: "ApplicationOutput"}]
			}
		}
	});
```

The `plugins` property takes a comma separated list of constructors for the appenders to be added and the `appenders` array can be used to add a configuration for the appender added. In the example above, a RollingFileAppender named `RollingFileAppender` has been added, which will log to a file `application.log` in directory `logs` until the maximum size of 10MB is reached. Then it will create a backup file and start with a new file. A maximum number of 5 backups are held. The appender is added through the `AppenderRef` array to the root logger and will log everything from level "warn" upwards to the log file. All loggers inheriting from root unless their `additivity` is set to false.

Each logger can be configured to use a given set of appenders individually as in this example:

```javascript
"loggers": {
		"root": {
			"level": "warn",
			"AppenderRef": [{
				"ref": "ApplicationOutput"
			}]
		},
		"logger": [{
			"name": "com.mycompany.test",
			"level": "debug",
			"additivity": true,
			"AppenderRef": [{
				"ref": "ApplicationOutput"
			}, {
				"ref": "DbAppender",
				"level": "warn"
			}, {
				"ref": "testFileAppender",
				"level": "info"
			}]
		}]
	}
```

The logger com.mycompany.test will output to the `ApplicationOutput` appender with level "debug" and above, to the appender named `testFileAppender` with level "info" and to the `DbAppender` with level "warn".

Appenders can also be added at runtime to any logger created like this:

```javascript
var logger = scopes.svyLogManager.getLogger('com.mycompany.testlogger');
var dbAppender = scopes.svyLogManager.getAppender('myDbAppender');
logger.addAppender(dbAppender, scopes.svyLogManager.Level.INFO);
```

## Appender properties

Each appender has different properties that are described below. 

### RollingFileAppender

The constructor for the RollingFileAppender that needs to be added to the list of plugins is 

`scopes.svyLogManager$rollingFileAppender.RollingFileAppender`

A typical configuration for the RollingFileAppender is

```javascript
{
	type: "scopes.svyLogManager$rollingFileAppender.RollingFileAppender",
	name: "RollingFileAppender",
	fileName: "logs/application.log",
	maxFileSize: 10 * 1024 * 1024,
	maxBackupIndex: 5,
	PatternLayout: {
		pattern: "%date %5level %30logger - %msg"
	}
}
```

The appender's properties are

* type: the class name of the appender (the name of the constructor function)
* name: the name of this specific appender (more than one RollingFileAppender can be added)
* layout: a subclass of AbstractLayout of svyLogManager, mostly PatternLayout is used
* fileName: the name of the log file created in the application_server dir including optional directory path
* maxFileSize: the maximum file size of the log file in bytes (defaults to 10MB)
* maxBackupIndex: the maximum number of backup files (defaults to 5)

### DbAppender

The constructor for the DbAppender that needs to be added to the list of plugins is 

`scopes.svyLogManager$dbAppender.DbAppender`

A typical configuration for the DbAppender is

```javascript
{
	type: "scopes.svyLogManager$dbAppender.DbAppender",
	name: "DbAppender",
	datasource: "db:/my_db/log_events",
	userId: globals.myUserId,
	dbMapping: {
		eventTimeColumnName: "event_time",
		loggerColumnName: "logger_name",
		logLevelColumnName: "log_level",
		logMessageColumnName: "log_message",
		userIdColumName: "user_id",
		solutionColumnName: "solution_name"
	},
	customColumns: [{
		columnName: 'my_custom_field',
		value: 'Hello'
	}, {
		columnName: 'my_custom_field_2',
		value: 'scopes.test.getMyValue'
	}, {
		columnName: 'my_custom_field_3'
	}],
	PatternLayout: {
		pattern: "%msg"
	}
}
```

The appender's properties are

* type: the class name of the appender (the name of the constructor function)
* name: the name of this specific appender (more than one DbAppender can be added)
* layout: a subclass of AbstractLayout of svyLogManager, mostly PatternLayout is used
* datasource: the name of the datasource of the table to be logged to
* dbMapping: object holding the mapping of log relevant columns
  * eventTimeColumnName: datetime column that holds the log event's time
  * logLevelColumnName: integer column that holds the log level constant (any of svyLogManager.LEVEL)
  * loggerColumnName: text column that holds the name of the logger
  * logMessageColumnName: text column that holds the log message
  * [logLevelNameColumnName]: text column that holds the log level name (e.g. "WARN")
  * [solutionColumnName]: text column that holds the solution name
  * [userIdColumnName]: text column that holds the userId
* [customColumns]: array of custom columns that can either receive a fixed value, a method result or given as parameters to the log message
  * columnName: the name of the custom column
  * [value]: a fix value or the name of a method to be called when the log event is written to the DB
* [userId]: a user ID that is written to the userIdColumnName column when set

When a PatternLayout is used, custom column values can be provided as parameters to the logger's log method as for example:

```javascript
logger.debug('This is a {} {} message', 'Hello', 'World', {my_custom_field: 'Hello', my_custom_field_2: 'World'});
```

This will lead to message 'This is a Hello World message' and my_custom_field gets value 'Hello', my_custom_field_2 'World'.

### EmailAppender

The constructor for the EmailAppender that needs to be added to the list of plugins is 

`scopes.svyLogManager$emailAppender.EmailAppender`

A typical configuration for the EmailAppender is

```javascript
{
	type: "scopes.svyLogManager$emailAppender.EmailAppender",
	name: "EmailAppender",
	toAddress: "john@doe.com",
	fromAddress: "john@doe.com",
	subject: "Logging message from Servoy solution",
	customFields: [{
		name: "userName",
		value: security.getUserName()
	}, {
		name: "ipAddress",
		value: application.getIPAddress()
	}],
	PatternLayout: {
		pattern: "Date: %date\nSolution: %s\nClient type: %t\nLevel: %level\nLogger: %logger\nMessage: %msg\nUser: %f{1}\nIP Address: %f{2}"
	}
}
```

The appender's properties are

* type: the class name of the appender (the name of the constructor function)
* name: the name of this specific appender (more than one EmailAppender can be added)
* layout: a subclass of AbstractLayout of svyLogManager, mostly PatternLayout is used
* toAddress: the address to send to
* fromAddress: the from address
* subject: the subject of the email sent

The EmailAppender uses the standard mail plugin of Servoy using the SMTP properties entered in servoy-admin/plugin-settings.

### PopupAppender

The constructor for the PopupAppender that needs to be added to the list of plugins is 

`scopes.svyLogManager$popupAppender.PopupAppender`

A typical configuration for the PopupAppender is

```javascript
{
	type: "scopes.svyLogManager$popupAppender.PopupAppender",
	name: "PopupAppender",
	style: 'test',
	maxMessages: 200,
	lazyInit: true,
	initiallyMinimized: false,
	newestMessageAtTop: false,
	PatternLayout: {
		pattern: "%d{HH:mm:ss,SSS} %8level %20logger{5.} - %msg"
	}
}
```

The appender's properties are

* type: the class name of the appender (the name of the constructor function)
* name: the name of this specific appender (more than one EmailAppender can be added)
* layout: a subclass of AbstractLayout of svyLogManager, mostly PatternLayout is used
* lazyInit: Set this to true to open the pop-up only when the first log message reaches the appender. Otherwise, the pop-up window opens as soon as the appender is created. If not specified, defaults to false.
* initiallyMinimized: Whether the console window should start off hidden / minimized. If not specified, defaults to false.
* width: The outer width in pixels of the pop-up window. If not specified, defaults to 600
* height: The outer height in pixels of the pop-up window. If not specified, defaults to 400.
* styleName: The name of the style of the form
* styleClass: The name of the style class of the form
* newestMessageAtTop: Whether to display new log messages at the top inside the pop-up window. Defaults to false.
* maxMessages: The maximum number of messages held in the stack. Set this to 0 or -1 if you want unlimited messages. Defaults to 1000.

The PopupAppender scope offers 3 methods to allow a developer to show/hide the console window and to clear the stack of a popup:

* scopes.svyLogManager$popupAppender.showPopup(appenderName): shows the given appender's console window
* scopes.svyLogManager$popupAppender.hidePopup(appenderName): hides the given appender's console window
* scopes.svyLogManager$popupAppender.clearStack(appenderName): clears all messages of the given appender