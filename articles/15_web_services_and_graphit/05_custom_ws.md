# Project Custom-Built Web Services/APIs

## Overview

While the built-in out-of-the-box web services are very useful and comprehensive, Fabric provides a very powerful capability of creating custom-built web services. This enables building new web services, according to the project needs, deploying these web services and using them immediately, without the need to wait for a new product release. This expedites the project's go-to-market strategy and ongoing updates.

In order to build and expose web services, use Graphit, a visual development Editor, which is aimed for building structured documents - with minimal coding needs, like JSON and XML - to be published as web services.

Note that Fabric also provides methods and helpers to build web services as Java functions, but it is recommended to use these only in specific cases, where Graphit does not fit the needs.

Similar to the built-in web services, the custom-built web services outcome is in either JSON, XML or CSV formats. 

Fabric also provides utilities and helpers to ease the building of the web services logic and the output. 

In addition to helping implementors to easily craft web services logic and output, Fabric lets you, via the Studio UI, to:

* Set versioning - read [here](09_custom_ws_properties.md). 
* Control and strict the outcome format - read [here](09_custom_ws_properties.md).
* Control and set verb - read [here](09_custom_ws_properties.md). 
* Control and strict permissions - read [here](09_custom_ws_properties.md).
* Control the path - read [here](09_custom_ws_properties.md).
* Set and use input parameters - read [here](08_custom_ws_input_parameters.md).
* Test the web services - read [here](11_swagger.md). 



## Invoking Custom APIs

The custom-built web services are exposed in a similar pattern to the built-in web services:

`/api/[VERSION_NO]/<web-service name>[?<parameters>&format=<FORMAT>&graphitProfiler=true]`

Where:

* **version** can be used for maintaining different versions of the same web service, for example to serve various clients. For more information about web service version usage - read [here](09_custom_ws_properties.md)

* parameters can be sent also as part of the URL path, as explained [here](08_custom_ws_input_parameters.md).

* **format** parameter can be either JSON, XML or CSV.

* **graphitProfiler** parameter, when set to true, turn on the profiler for this web-service call. 

  To see the profiling information:

  * Open the [Fabric Logs](/articles/21_Fabric_troubleshooting/02_Fabric_troubleshooting_log_files.md#log-files) of the Fabric session that processed the Web Service in the $K2_HOME/logs/k2fabric.log. At the Studio, the Log files are accessible from the log viewer.
  * Search for the lines containing 'Starting webservice':





[![Previous](/articles/images/Previous.png)](/articles/15_web_services_and_graphit/04_built_in_fabric_ws.md)[<img align="right" width="60" height="54" src="/articles/images/Next.png">](/articles/15_web_services_and_graphit/06_custom_ws_create_graphit_ws.md)



