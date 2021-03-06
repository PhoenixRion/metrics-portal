Metrics Portal
==============

Provides a web interface for metrics. This includes viewing telemetry (aka streaming statistics) from one or more hosts running [Tsd Aggregator](https://github.com/ArpNetworking/metrics/blob/master/tsd/tsd-aggregator/README.md) and [ReMet Proxy](https://github.com/ArpNetworking/metrics/blob/master/remet-proxy/README.md). The web interface also provides for browsing hosts reporting metrics as well as viewing and editing alerts and expressions.

Setup
-----

### Building

Prerequisites:
* [JDK8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Play 2.4.3](http://www.playframework.com/download)

Building:

    metrics-portal> ./activator stage

### Installing

The artifacts from the build are in *metrics-portal/target/universal/stage* and should be copied to an appropriate directory on the Metrics Portal host(s).

### Execution

In the installation's *bin* directory there are scripts to start the Metrics Portal: *metrics-portal* (Linux/Mac) and *metrics-portal.bat* (Windows).  One of these should be executed on system start with appropriate parameters.  For example:

    /usr/local/lib/metrics_portal/bin/metrics_portal -J-Xmn150m -J-XX:+UseG1GC -Dhttp.port=80 -Dpidfile.path=/usr/local/var/METRICS_PORTAL_PID

### Configuration

Aside from the JVM command line arguments, you may provide two additional configuration files. The first is the [LogBack](http://logback.qos.ch/) configuration file.  To use a custom logging configuration simply add the following argument to the command line above:

    -Dlogger.file=/usr/local/lib/metrics_portal/logger.xml

Where */usr/local/lib/metrics_portal/logger.xml* is the path to your logging configuration file. The included [default logging configuration file](conf/logger.xml) is automatically applied if one is not specified. Please refer to [LogBack](http://logback.qos.ch/) documentation for more information on how to author a configuration file.

The second configuration is for the application. To use a custom configuration simply add the following argument to the command line above:

    -Dconfig.file=/usr/local/lib/metrics_portal/application.custom.conf

Where */usr/local/lib/metrics_portal/application.custom.conf* is the path to your application configuration file.  The included [default application configuration file](conf/portal.application.conf) in the project documents and demonstrates many of the configuration options available. To use the default application configuration file it needs to be specified on start-up:

    -Dconfig.resource=conf/portal.application.conf

To author a custom application configuration it is recommended you inherit from the default application configuration file and provide any desired configuration as overrides. Please refer to [Play Framework](https://www.playframework.com/documentation/2.4.x/ProductionConfiguration) documentation for more information on how to author a configuration file.

### Extension

The Metrics Portal project intentionally uses a custom default application configuration and custom default routes specification. This allows projects extending the Metrics Portal to supplement functionality more easily with the standard default application configuration and routes. To use these files as extensions rather than replacements you should make the following changes.

First, add dependencies on the Metrics Portal code and assets in __conf/Build.scala__:

    "com.arpnetworking.metrics" %% "metrics-portal" % "VERSION",
    "com.arpnetworking.metrics" %% "metrics-portal" % "VERSION" classifier "assets",

Second, your extending project's application configuration should include the custom default configuration in __conf/application.conf__:

    include "portal.application.conf"

Third, your extending project's application configuration should restore the default router in __conf/application.conf__:

    application.router = null

Finally, your extending project's routes specification should include the custom default routes in __conf/routes__:

    -> / portal.Routes

### Development

To run the application in Play's debug mode execute:

    metrics-portal> ./activator run

To run the application in Play's production execute:

    metrics-portal> ./activator "start -Dconfig.file=conf/portal.application.conf"

The former is configured (see [Build.scala](projectt/Build.scala)) to automatically use the custom default configuration while the latter must be instructed explicitly.

To publish your development version of Metrics Portal locally for extension execute:

    metrics-portal> ./activator publishLocal

License
-------

Published under Apache Software License 2.0, see LICENSE

&copy; Groupon Inc., 2014
