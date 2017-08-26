# User Management

- [Getting Started Tutorial](../index.md)
  - [Installation](../gettingstarted/index.md)
  - [Basic Communication](../communicationtutorial/index.md)
  - [User Management](../usermanagement/index.md)
    - [Login Process Overview](#login-process-overview)
    - [Session Store](#session-store)
    - [Gateway Service](#gateway-service)
    - [Account Service](#account-service)
    - [Test Client](#test-client)
    - [Whats Next](#whats-next)
  - [Simple Example Game](../tutorial/index.md)
- [Quick Reference](../quickreference/index.md)

For Users to be able to connect to a MicroNet application the MicroNet *User Management* Infrastructure needs to be available. This infrastructure consists of an API Gateway Service, an Account Service including a relational Account Database, and a Session Store using a NoSQL Database and of course ActiveMQ for networking. This sounds like a lot of stuff but MicroNet makes all these services and technologies available to you in a simple way using containers (Just like you installed ActiveMQ on your system using a container).

Make sure that the ActiveMQ container is running and remove all other running services from previous tutorials.

## Login Process Overview

The Login Process that MicroNet defines is aimed to be as simple as possible and is meant to be adjusted by the developer according to the needs of the game application. The image below shows the login process along with all actors that take place in it.

![login-process](PlayerSessions.png "MicroNet Login Process")

1. The user sends a login request to the public broker.
2. One API Gateway polls the message in a competing consumer fashion.
3. The gateway forwards the login message to the internal broker using the mn://account/login queue.
4. One Account Service polls the message in a competing consumer fashion.
5. The Account Service authenticates the user using the Account Database.
6. The account service returns the login response to a temporary queue held open by the responsible gateway.
7. The gateway service consumes the login response from the temporary queue.
8. Upon login success the gateway adds a new player session to the session store, identified by the player connection.
9. The gateway returns the login response to a temporary queue held open by the requesting player.
10. The player consumes the login response from a temporary queue. Upon success he gains access to the game services (example sequence: A, B, C, D, E).

## Session Store

MicroNet uses [Couchbase](https://www.couchbase.com/) as a NoSQL database to provide Session Store capabilities. Think of the session store as a shared data storage used by your Microservices to persist data for a set amount of time or indefinitly. The distibuted nature of Couchbase makes it possible to make often used session data highly available among services using eventual consistency concepts.

> Because multiple instances of several Microservices are deployed to make these Microservice applications reliable, services are not allowed to store session data directly in memory ???(ganzer Satz). Instead the session store must be used to persist data between requests. The result are stateless message transfers.

Add the **mn-archetype-couchbase** archetype to your game workspace using the MicroNet *Service Catalog*. Perform a *Full Service Build* of the Couchbase Service Project from the Service Explorer and afterwards start Couchbase via *Start Serivce Container* in the Service Explorer.

> Important: MicroNet uses a shell script called *wait-for-it.sh* to ensure that required services like activemq are avaible before starting dependent services. This however does not work for couchbase because the ports offered by couchbase are already open during initialization of couchbase and therefore starting couchbase simultaneously with any dependent container leads inevitably to errors. It is therefore recommended to start couchbase prior to any other services to ensure availibility. If anyone finds a solution for this timing issue, please contact me.

Couchbase needs a lot of memory itself and together with MicroNet at least 2048MB of Ram need to be available for the Couchbase container. You can adjust the avaiable RAM for container using the Docker Desktop Settings Window or for Docker Toolbox using the *Oracle VM VirtualBox Manager*.

> You can also access Couchbase with the Web Interface via http://localhost:8091/ui or http://your_docker_machine_ip:8091/ui respectively. The default username/password are Administrator/password. Change the credentials in the *couchbase_init.sh* script which is contained in the Root folder of the Couchbase project.

## Gateway Service

The Gateway offers the API that is provided by the individual Microservices to the Users of the application. Obviously this service introduces additional security requirements since it has to be accessible from the outside world, the Internet. Up until now all communications have been taking place in a private network not accessible by the outside. All services you introduce are automatically participants of this internal network and therefore all inter service communication is considered secure. To allow external access to services that reside in the internal network, the API Gateway Service comes into play. To keep the internal network secure MicroNet introduces a second public message broker which is generally considered unsecure. Everyone is allowed to connect to this public broker but messages are only forwarded to the internal network on behalf of the API Gateway instances. The API Gateway is implemented as a reverse proxy.

> The "two message broker" concept is not yet implemented in MicroNet. Momentarily all connections go directly to the internal network which is unsecure. This is also the main reason why it is not advised to use MicroNet for production at the current stage. Also, encryption is a feature that is not yet supported by MicroNet and all passwords are stored in plain text. All these security aspects will be covered as soon as possible.

Add the **mn-archetype-gatewayservice** to the game workspace and launch it. Observe how the Gateway Service connects to the Session Store to access the *User Conections* Bucket.

## Account Service

The Account archetype is a Multi-Module Maven archetype and consists of two projects, the AccountService and the AccountDB projects. Add the **mn-archetype-account** archetype to your game workspace. 

> Notice that choosing the acrifactId "Account" results in the Service projects: Account, AccountService, and AccountDB. The Account project itself is not used for anything but is required for the Maven project Hierarchy.

Build and Run the AccountDB as a *Service Container* and start the AccountService project either *Native* or as a *Service Container*. In this case it is necessary to at least start the AccountDB simultaneously with the AccountService to prevent any timimng issues.

> If you are using Docker Toolbox the AccountService wont find the AccountDB network address due to the *docker-machine* issue. Be shure to add: **database_address=*your_docker_machine_ip:5432* to the AccountService Native Environment Args. 

## Test Client

If all the services are running you are finally ready to connect to the MicroNet application using a client. If you did not add the MicroNet Catalogs during Workspace Setup, press *Configure...* in the top right and add the MicroNet Example Service Catalog via *Add Remote Catalog...*:

- **MicroNet Examples:** https://mrharibo.github.io/catalog/example-archetype-catalog.xml

Add the **mn-archetype-testclient** archetype from the *MicroNet Examples Service Catalog* to your game workspace.

![example-service-catalog](ExampleServiceCatalog.PNG "Example Service Catalog")

The test client is a native Java application providing a small console style UI. To start the TestClient right click the MyGame.TestClient.TestClient.java in the *src/main/java* directory and click *Run/Debug As -> Java Application*.

> If you are using Docker Toolbox you run into the same connectivity problems as with services. But since the Client is not a service you have to add the **message_broker_address=your_docker_machine_ip:61616** to the Eclipse Run Configuration of TestClient. Add the message broker address on the *Environment* tab of the *TestClient* Launch configuration.

![test-client](TestClient.PNG "Test Client")

If everything went well you can register and login at this point... You are online!

## Whats Next

Now that we have basic communication and user management out of the way we can finally move on an implement some gameplay functionality. Continue with the [Simple Example Game](../tutorial/index.md) section.

