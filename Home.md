[LogManager](https://github.com/Servoy/svyUtils/wiki/LogManager)
----------
Provides finegrained logging functionality built with performance and configurability in mind.

EventManager
------------
Provides the ability to (de)register listeners for events and fire events. Aims to provide a way to decouple hard links in code between otherwise stand-alone code

ApplicationCore
------------
It offers default implementations for the solutions onError and onDataBroadcast events and provides a module initialization mechanism.

The aim of this module is to provide the API's for 3rd party modules to allow easy integration.

Web Client utils
----------------
Set of utility methods specifically for the Servoy Web Client
Also replaces (most) of the functionality of the [WebClientUtils plugin](https://www.servoyforge.net/projects/webclientutils)

Smart Client utils
------------------
Set of utility methods specifically for the Servoy Smart Client

[Excel utils](https://github.com/Servoy/svyUtils/wiki/ExcelUtils)
------------------
Set of utility methods to read, create and manipulate Excel Workbooks. Does not require Microsoft Excel.

Table Grid
------------------
Creates in-memory data sources and table view forms of any JSDatSet or JSFoundSet that can be shown in a given tab panel. This is useful if you need to show for example the result of an aggregate query in a table form or just need an intermediate table to display.

Custom Dialogs
------------------
Provides the ability to create simple dialogs containing any number of different fields and text labels. Dialogs are laid out in a a simple two column label/field grid. Dialogs are web client compatible, meaning they handle continuations.

LogManagerAppenders Utils
------------------
Provides a set of Appenders plugins for the [LogManager](https://github.com/Servoy/svyUtils/wiki/LogManager) Loggers. The implemented appenders allow to append logs in database and in a log file 

UnitTest Utils
------------------
Utility methods for unit testing