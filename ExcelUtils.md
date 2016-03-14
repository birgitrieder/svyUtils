# Introduction
svyExcelUtils is a wrapper for Apache POI's excel library and allows to read and write xls and xlsx files directly from within Servoy.

# Installation
svyExcelUtils is dependent on some external libraries, some of which are already part of Servoy's Jasper plugin. So to use the functionality, you need to install at least the basic version of the Jasper plugin available here: https://www.servoyforge.net/projects/servoy-jasperreports/files

For support of xlsx and docx (as supported by svyWordUtils), some additional libraries are required that can be downloaded here: . Please place all files inside the archive in application_server/plugins/poi.

To let a smart client know it needs to download these libraries, some adjustments to the jnlp file of the Jasper plugin (application_server/plugins/servoy_jasperreports.jar.jnlp) need to be made:

##### Comment or remove poi-xxx.jar and xalan-xxx.jar

In the jnlp file you will find some entries like these

```xml
<jar href="/plugins/servoy_jasperreports/poi-3.10.1.jar" download="lazy" part="poi" version="3.10.1"/> 
   <package name="org.apache.poi.*" part="poi" recursive="true"/> 
<jar href="/plugins/servoy_jasperreports/xalan-2.7.1.jar" download="lazy" part="xalan" version="2.7.1"/> 
   <package name="org.apache.*" part="xalan" recursive="true"/> 
   <package name="java_cup.runtime.*" part="xalan" recursive="true"/>
```

Either remove or comment these lines out (by nesting them into a comment <!-- ... -->). To not run into conflicts in developer, remove these two files from application_server\plugins\servoy_jasperreports or rename them from *.jar to *.unused or whatever, so they don't get loaded by Servoy developer.

##### Add a few entries to the jnlp file

```xml
<!-- Files needed for Apache POI -->
<jar href="/plugins/poi/poi-3.14-20160307.jar" download="eager" version="%%version%%"/>
			
<!-- Files needed for xlsx and docx -->
<jar href="/plugins/poi/poi-ooxml-3.14-20160307.jar" download="eager" version="%%version%%"/>
<jar href="/plugins/poi/curvesapi-1.03.jar" download="eager" version="%%version%%"/>
<jar href="/plugins/poi/ooxml-schemas-1.3.jar" download="eager" version="%%version%%"/>
<jar href="/plugins/poi/xmlbeans-2.6.0.jar" download="eager" version="%%version%%"/>
    
<!-- Files needed for docx -->
<jar href="/plugins/poi/poi-scratchpad-3.14-20160307.jar" download="eager" version="%%version%%"/>
<jar href="/plugins/poi/xdocreport-1.0.4.jar" download="eager" version="%%version%%"/>
```

You may sign the libraries in the poi directory just as you would sign any plugin. For convenience all these libraries are signed with Servoy's certificate. So if you do not sign the application server with your own certificate, you should not run into signature problems.

# Creating Excel files
Want to get started and do the reading later? Check out the following snippet:
```javascript
var log = scopes.svyLogManager.getLogger('com.mycompany.mysolution')
log.debug("Hello, I'm debuggin'")
var name = 'you'
log.trace("Hello {}", name) //outputs "Hello you":
```

# Architecture

### Logger Hierarchy


### Loggers

 
# API

# Configuration
 
# Plugins