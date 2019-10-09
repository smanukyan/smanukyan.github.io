---
layout: post
title:  "Spring Cloud Config Server - which version?"
date:   2019-10-08
categories: config-server spring-cloud-services update
---
With the introduction of `Spring Cloud Services (SCS)` version 3.0, that obsoleted all but Spring Cloud Config Server from the offering, there may arise a need to find out which version of a Config Service is currently installed in PCF Marketplace. This tutorial will help you do that. Unless otherwise noted the command line instructions are provided for Mac OS X that has a `brew` tool installed.

# Install the cf cli tool to access the PCF
{% highlight bash %}
brew install cloudfoundry/tap/cf-cli 
{% endhighlight %}
(other OS instructions see [here][cf-cli])

# Log in into PCF
{% highlight bash %}
cf login -a <PCF API URL>
{% endhighlight %}

# Install the cf plugin to manage Spring Cloud Services
{% highlight bash %}
cf install-plugin spring-cloud-services
{% endhighlight %}

# Find out the service name of the Config Server
{% highlight bash %}
cf services
{% endhighlight %}
Will be listed as a service type `p-config-server`. Here is a sample output :
{% highlight console %}
name            service           plan    bound apps   last operation     broker                    upgrade available
config-server   p-config-server   standard             create succeeded   p-spring-cloud-services
{% endhighlight %}
The name of our service is `config-server`

# View the Spring Cloud Service
{% highlight bash %}
cf scs-view config-server
{% endhighlight %}
Sample output:
{% highlight console %}
backing app name: config-b458dcfb-e136-484d-975c-beb37dea5523
requested state:  started
instances:        1/1
usage:            1G x 1 instances
routes:           config-b458dcfb-e136-484d-975c-beb37dea5523.cfapps.io
last uploaded:    Sun 06 Oct 23:20:18 EDT 2019
stack:            cflinuxfs3
buildpack:

     state     since                  cpu    memory       disk           details
#0   running   2019-10-07T03:21:33Z   0.3%   251.1M of 1G 147.5M of 1G
{% endhighlight %}
The `routes` represent the HTTP route to the `config-server`

# Fetch the info actuator on the service HTTP route
{% highlight bash %}
curl http://config-b458dcfb-e136-484d-975c-beb37dea5523.cfapps.io/actuator/info
{% endhighlight %}
Sample output:
{% highlight json %}
{
   "version":"20",
   "git":{
      "commit":{
         "time":"2019-05-15T01:46:22Z",
         "id":"dad433f"
      },
      "branch":"HEAD"
   },
   "build":{
      "version":"",
      "artifact":"spring-cloud-config-server",
      "name":"config-server",
      "group":"io.pivotal.spring.cloud",
      "time":"2019-06-18T20:22:25.623Z"
   }
}
{% endhighlight %}
The version 20 means `v2.0`, and 30 will mean `v3.0` of Spring Cloud Services. So in our hypothetical case we found out that v2.0 is currently installed. 

*P.S. By the way it is possible to install v2.0 and v3.0 side by side, and as of today v3.1 has been released, adding back the Service Discovery. Also would like to note that `credhub` integration with Config Server is available only starting from v3.0 of SCS.*

[cf-cli]: https://docs.pivotal.io/platform/2-7/cf-cli/install-go-cli.html

