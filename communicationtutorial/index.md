# Simple MicroNet Communication

In this tutorial you tryout basic connectivity between Microservices using the default MessageBroker ActiveMQ.

## Adding ActiveMQ

In this first step you will add ActiveMQ to your game workspace. Open the *New Project* dialog via the *Project Explorer* or the *File* menu. In the dialog select *Maven -> Maven Project* and press next until the archetype selection page. Select the MicroNet Archetype Catalog and choose the *mn-archetype-activemq* archetype. Press next and edit the metadata of the ActiveMQ project. The artifactId can be chosen arbitrary but the groupId should match the groupId from the game workspace metadata. Press finish to create the project.

To test ActiveMQ find it in the service explorer and right click it to open the context menu. Execute the **Full Service Build** action and observe the build output in the console. If the build was successful you can start ActiveMQ via the context menu with the **Run Service Container** button.

> To verify that ActiveMQ is running you can use the Docker Explorer provided by the *Docker Tools for Eclipse* which can be found in the Eclipse marketplace. Once installed open the *Docker Explorer* view over *Window -> Show View -> Other -> Docker -> Docker Explorer*. In the Docker Explorer locate the ActiveMQ container and verify that it is running. The desired state is shown in the image below.

![docker-explorer](DockerExplorer.PNG "Docker Explorer from the Docker Tools for Eclipse")

> You can also access the ActiveMQ *Web Console* with your browser to observe the state of ActiveMQ. The Web Console is avaibale under http://localhost:8161/admin/ and the deault username/password are "admin"/"admin". For production change the admin credentials in any case.

## Creating a Loopback Service

Services can communicate with each other or can send messages to themself. The first service that you will create will do exatly that, sending itself a message.

> Be aware that if multiple instances of one service are deployd it not guaranteed that the same service the sent the message will receive it again.

Over the MicroNet Service Catalog add the *mn-archetype-simpleservice* to your game workspace in the same fashion you added ActiveMQ. Name the artifactId **FooService**. This will create a Maven Java *Service Project* in the workspace. In the Service Project expand the *src/main/java* folder and locate the *MyGame.FooService.FooService.java* class. This class can be though of as the *Main Class* of the service and is used as an entrypoint to inject domain logic into the service. The Service created by the *mn-archetype-simpleservice* archetype is already prepared to be tested right away. Make shure ActiveMQ is running and start the container as a local Java application using the context menu action *Debug/RunServiceNative*. Observe the console as the service starts up and send itself a message. You can also perform a *Full Service Build* and start the service in a container but be shure to remove it afterwards via the Docker Explorer or you wont be able to start again due to a naming collision.

> Note that at the moment it is not possible to locally run the same container twice for testing. This feature is planned to be added in the future. Meanwhile you can use Docker Swarm to deploy multiple instances of the same container using the *docker service* CLI.







In the Main Class edit the `@MessageService` and enter a valid URI like for example 
