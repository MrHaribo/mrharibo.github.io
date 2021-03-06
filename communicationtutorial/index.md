# Basic MicroNet Communication

- [Getting Started Tutorial](../index.md)
  - [Installation](../gettingstarted/index.md)
  - [Basic Communication](../communicationtutorial/index.md)
    - [Adding ActiveMQ](#adding-activemq)
    - [Creating a Loopback Service](#creating-a-loopback-service)
    - [Adding a Communication Partner](#adding-a-communication-partner)
    - [Whats Next](#whats-next)
  - [User Management](../usermanagement/index.md)
  - [Simple Example Game](../tutorial/index.md)
- [Quick Reference](../quickreference/index.md)

This tutorial shows you how the basic connectivity between Microservices using the default MessageBroker ActiveMQ works. It is assumed that you have the *Docker Engine* and *Eclipse IDE* installed and have performed all necessary workspace modifications as explained on the [Installation](../gettingstarted/index.md) page.

## Adding ActiveMQ

In the first step you will add ActiveMQ to your game workspace as a *Service Project*. Open the *New Project* dialog via the *Project Explorer* or the *File* menu. In the dialog select *Maven -> Maven Project* and press next twice to display the archetype selection page and select the MicroNet Archetype Catalog. If you did not add the MicroNet Catalogs during Workspace Setup, press *Configure...* in the top right and add the MicroNet Service Catalog via *Add Remote Catalog...*:

- **MicroNet:** https://mrharibo.github.io/catalog/archetype-catalog.xml

With the MicroNet Catalog selected choose the *mn-archetype-activemq* archetype as shown in the image below and press next. On the next page edit the project metadata of the ActiveMQ project. The artifactId of the ActiveMQ project can be chosen arbitrarily but the groupId should match the groupId which you specified during Game Workspace setup earlier. Press finish to create the project.

![#service-catalog](ServiceCatalog.PNG "MicroNet Service Catalog")

To test ActiveMQ find it in the Service Explorer view and right-click it to open the context menu. Execute the **Full Service Build** action and check the build output in the console. If the build was successful you can start ActiveMQ via the context menu with the **Run Service Container** button.

> To verify that ActiveMQ is running you can use the **Docker Explorer** provided by the *Docker Tools for Eclipse* which can be found in the Eclipse marketplace. Once installed open the *Docker Explorer* view through *Window -> Show View -> Other -> Docker -> Docker Explorer*. In the Docker Explorer locate the ActiveMQ container and verify that it is running. The desired state is shown in the image below.

![#docker-explorer](DockerExplorer.PNG "Docker Explorer from the Docker Tools for Eclipse")

> You can also access the ActiveMQ *Web Console* with your browser to observe the state of ActiveMQ. The Web Console is avaibale under http://localhost:8161/admin/ and the default username/password credential is "admin"/"admin". For production use change the administrative credentials as explained on the [ActiveMQ Dockerhub Page](https://hub.docker.com/r/webcenter/activemq/).

> If you are using Docker Toolbox you can't access the web console via *localhost* since the Docker Daemon runs on a completely isolated virtual host provided by Virtual Box called a *docker-machine*. In this case replace *localhost* with your docker-machine ip. It is shown when you started Docker via the *Docker Quickstart Terminal* as shown in the image below. If you missed it use the *docker-machine ip* CLI command.

> ![#docker-guickstart-terminal](DockerMachineTerminal.PNG "Docker Machine Startup")

## Creating a Loopback Service

Services can communicate with each other or can send messages to themself. The first service that you will create will do the latter, sending a message to itself.

> Be aware that if multiple instances of one service are deployed it is not guaranteed that the service sending the message will also receive the message again.

Using the MicroNet Service Catalog add the *mn-archetype-simpleservice* to your game workspace the same way you added ActiveMQ. Name the artifactId **FooService**. Upon confirmation a Maven Java *Service Project* will be created in the workspace. In the Service Project expand the *src/main/java* folder and locate the *MyGame.FooService.FooService.java* class as shown in the picture below.

![#project-explorer](ProjectExplorer.PNG "Project Explorer showing a Service Project")

This class can be thought of as the *Main Class* of the service and is used as an entry-point to inject domain logic into the service. The Service created by the *mn-archetype-simpleservice* archetype is already prepared to be tested right away. Make sure ActiveMQ is running and start the container as a local Java application using the context menu action *Debug/RunServiceNative* in the Service Explorer as shown in the image below. Check the console as the service starts up and sends itself a message. If you are using Docker Toolbox you most likely get a connection error. See [below](#connecting-with-docker-toolbox) how to deal with this.

You can also perform a *Full Service Build* and start the service in a container but be sure to remove it afterwards via the Docker Explorer or you will not be able to start it again due to a naming collision.

![service-debugging](ServiceDebugging.png "Debugging a MicroNet Service")

> Note that at the moment it is not possible to locally run the same container twice for testing. This feature is planned to be added in the future. Meanwhile you can use Docker Swarm to deploy multiple instances of the same container using the *docker service* CLI.

The desired log output should look similar to the one shown in the image below.

![service-start-log](StartingLog.PNG "Starting Log of a MicroNet Service")

> Tip: If you think a Service Project shows way to many folders in the Project Explorer as add a new *Filter* via the *Drop Down* menu of the Project Explorer. Add "src, target, shared_contribution" as Name filter patterns, as shwon in the image below. You can also check the *Libraries from external* checkbox but cheking on the library folders is sometimes useful.

> ![name-filter-pattern](NameFilterPatterns.PNG "Useful Name filter patterns")

### Connecting with Docker Toolbox

If you are using a Native Launch Configuration with Docker Toolbox you might run into a connection error like the one shown in the image below.

![#connection-error](ConnectionError.PNG "Connection Error")

This is because the *docker-machine* is not accessible via *localhost*. MicroNet provides a mechanism to set environment variables for Native Launch Configurations which allow to inject the *docker-machine* ip as Message Broker Address. In the *Service Explorer* rightclick the service that could not connect and select the *Add Env Vars...* action. Add the following argument:

- message_broker_address=*your_docker_machine_ip*:61616

How to retrieve the *docker-machine-ip* is explained in the section above. You have to do this for all Java services which need access to ActiveMQ. This will be automated in future.

## Adding a Communication Partner

Of course one service does not make up a very interesting distributed application so we spice things up a little by adding a communication partner for our lonely *FooService*. But before we do that, we need to ensure that we give FooService a proper address so it can be found by other services or by the users of the application later on. In the *Main Class* of **FooService** edit the `@MessageService` annotation and enter a valid URI e.g. "mn://foo". The protocol portion "mn://" of the URI is required for a service to be recognized by a MicroNet application. Additionally, in the ServiceFoo class remove the 

```java
context.sendRequest("mn://fooservice/hello/world/handler", new Request("Hello"));
``` 
call from the start method since the service "mn://fooservice" no longer exists. Finally the FooService should look like this:

```java
@MessageService(uri = "mn://foo")
public class FooService {
	
	@OnStart
	public void onStart(Context context) {
		System.out.println("FooService Start Routine...");
	}
	
	@MessageListener(uri="/hello/world/handler")
	public void helloHandler(Context context, Request request) {
		System.out.println(request.getData() + " World MicroNet...");
	}
}
```

Now add another *mn-archetype-simpleservice* archetype project to your game workspace and name the artifactId **BarService**. In the Main Class of the BarService change the `@MessageService` and enter a valid URI for the service for example "mn://bar". Change the call in the BarService Main Class from 

```java
context.sendRequest("mn://barservice/hello/world/handler", new Request("Hello"));
```

to

```java
context.sendRequest("mn://foo/hello/world/handler", new Request("Hello from Bar"));
```

You can also remove the `helloHandler` method from the BarService to keep it clean (Microservice style). The resulting BarService should look like this:

```java
@MessageService(uri = "mn://bar")
public class BarService {
	
	@OnStart
	public void onStart(Context context) {
		System.out.println("BarService Start Routine...");
		context.sendRequest("mn://foo/hello/world/handler", new Request("Hello from Bar"));
	}
}
```

To test the communication between the two services you must first ensure that no old service instances are running which may interfere with our new services. Check this on the *Debug View* in the Java *Debug Perspective* and in the container section in the *Docker Explorer*. You can leave ActiveMQ running because we still need it. After this clean-up start both services using your preferred method. The order does not matter since the message that is sent by BarService is buffered by ActiveMQ and will be delivered as soon as FooService is available. Check the Console of both services to see if the communication was successful.

## Whats Next

Now that you can establish a simple service communication you can add more complex features to you Game Workspace. Move on to the [User Management Tutorial](../usermanagement/index.md) Tutorial.
