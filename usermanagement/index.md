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

Make shure that the ActiveMQ container is running and remove all other running services from previous tutorials.

## Login Process Overview

The Login Process that MicroNet defines is aimed to be as simple as possible and is meant to be adjusted by the develop according to the needs of the a game application. The image below shows the login process along with all actors that take place in it.

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
10. The player consumes the login response from a temporary queue. Upon success he gains access to the game services (example sequence: A, B, C, D 5 , E).

## Session Store

MicroNet uses [Couchbase](https://www.couchbase.com/) as a NoSQL database to provide Session Store capabilities. Think of the session store as a shared data storage used by your Microservices to persist data for a set amount of time or infiniely. The distibuted nature of Couchbase makes it possible to make often used session data highly available using eventual consistency concepts.

> Since usually multiple instance of a Microservices are deployed to achieve reliablilty Microservices are not allowed to store session data directly in memory. Instead the session store can be used persist data between request.

Add the **mn-archetype-couchbase** archetype to your game workspace using the MicroNet Service Catalog. Perform a *Full Service Build* of the Couchbase Service Project from the Service Explorer and afterwards start Couchbase via *Start Serivce Container*.

> MicroNet uses a shell script called *wait-for-it.sh* to enshure that required services like activemq are avaible before starting a service. This however does not work for couchbase because the ports offered by couchbase are already open during initialization of couchbase and therefore starting couchbase simultaneously with any dependent container leads inevitabley to errors. It es therefore recommended to start couchbase prior to any other services to enshure availibility. If anyone finds a solution for this, please contact me.

## Gateway Service

The Gateway offers the API that is provided by the individual Microservices. Obviously this service introduces additional requirements to security because it has to be accessible from the outside, the Internet. Up until now all communications have been taking place in a private network not accessible by the outside. All services you introduce are automatically participants of this internal network and therefore all inter service communications are considered secure. To allow access to services that reside in the internal network, the API Gateway Service comes into play. For this porpose MicroNet provides a second public message broker which is considered unsecure. Generally everyone is allowed to connect to this public broker. The API Gateway then interconnects the two networks as a reverse proxy.

> This two message broker concept is not yet implemented in MicroNet. Also Encryption is a feature that is not yet provided by MicroNet at this stage. Also all passwords are stored in plain text. All these security aspects will be covered as soon as possible.

Add the **mn-archetype-gatewayservice** to the game workspace and launch it. Observe how the Gateway Service connects to the Session Store to access User Information later.

## Account Service

The Account archetype is a Multi-Module Maven archetype and consists of two projects, the AccountService and the AccountDB projects. Add the **mn-archetype-account** archetype to your game workspace. 

> Notice that choosing the acrifactId "Account" results in the Service projects: Account, AccountService, and AccountDB. The Account project it self is not used for anything but is required for the Maven project Hierarchy.

Build and Run the AccountDB as a *Service Container*. Also start the AccountService project either *Native* or as a *Service Container*.

## Test Client

If all the services are running you are finally ready to connect to the MicroNet application using a client. Add the **mn-archetype-testclient** archetype from the *MicroNet EXAMPLE Service Catalog* to wour game workspace. The test client is a native Java application providing a little console style UI. To start the TestClient right click the MyGame.TestClient.TestClient.java in the *src/main/java* directory and click *Run/Debug As -> Java Application*.

If everything went well you can register and login at this point... You are online!

## Whats Next

Now that we have basic communication and user management out of the way we can finally move on an implement some gameplay functionality. Continue with the [Simple Example Game](../tutorial/index.md) section.

