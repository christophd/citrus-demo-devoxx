Demo for "Testing Microservices with a Citrus twist" ![Logo][1]
==============

Citrus Devoxx Demo
---------

The cookie bakery demo sample application uses a multi module Maven project with each module being deployed in a separate
Docker container. Citrus integration tests are provided on multiple levels (module level and acceptance test level)
The modules are:

* web
* worker
* report
* acceptance

Using this example with Docker
---------

This sample is using Docker as infrastructure for starting up the services in separate containers. The sample application
makes use of Docker environment variables and hsit name settings that need to be set befoer using the application. Non-Linux 
users might want to add dockerhost to your /etc/hosts configuration in order to simply access the
services running in Docker containers without any port forwarding:

```
echo $(docker-machine ip your-docker-machine-name) dockerhost | sudo tee -a /etc/hosts
```

In the parent Maven POM you will find properties pointing to the dockerhost and Citrus host, where Citrus host is your host ip or 
local area network host name.

```
<citrus.host.name>192.168.99.1</citrus.host.name>
<docker.host.name>dockerhost</docker.host.name>
```

You might want to adjust these properties by adding following parameters to the Maven commands shown in this readme:

```
-Dcitrus.host.name=myHostName
-Ddocker.host.name=dockerHostName
```

Now you can build the Docker containers. Be sure that Docker is setup and running on your local machine. Lets build the
sample Docker application images by calling:

```
mvn clean package docker:build
```

Now you will be able to see some more docker images on your host.

```
docker images
```

Lets start the complete Docker container infrastructure

```
mvn -pl acceptance docker:start
```

This may take a while when executed for the first time as Docker images will be loaded from DockerHub repository for Java,
Tomcat, ActiveMQ and so on. After that you will then see some Docker containers started on your host

```
docker ps
```

You should see Docker containers running on your host:

* bakery-web-server
* report-server
* activemq-server
* worker-chocolate
* worker-blueberry
* worker-caramel

Open a browser an point to

```
http://dockerhost:18001/bakery
http://dockerhost:18002/report
```

You will see some Web UI for the bakery and reporting application. Place some orders manually and reload the reporting UI
to see that things are working for you.

Now execute some Citrus integration tests

```
mvn -pl integration integration-test
```

To stop the Docker containers run

```
mvn -pl integration docker:stop
```

Now lets run the complete lifecycle with all modules build, shipped to Docker and all Citrus tests executed

```
mvn clean install -Pdocker
```

Now explore the different modules by doing the above steps for each Maven sub module. The steps are beeing the same
docker:start, integration-test, docker:stop.

Citrus test
---------

Once the sample application is deployed and running you can execute the Citrus test cases. Execute all Citrus tests by calling

> mvn integration-test

You can also pick a single test by calling

> mvn integration-test -Pit.test=TestName

You should see Citrus performing several tests with lots of debugging output in your terminals. And of course green tests at 
the very end of the build. Now you can further explore the application by executing Citrus tests form our Java IDE. Also have a 
look at the Maven POM configuration. You can change the versions of used application servers and activemq message broker in POM 
property section. 

Information
---------

For more information on Citrus see [www.citrusframework.org][2], including
a complete [reference manual][3].

 [1]: http://www.citrusframework.org/img/brand-logo.png "Citrus"
 [2]: http://www.citrusframework.org
 [3]: http://www.citrusframework.org/reference/html/
