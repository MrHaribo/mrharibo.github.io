# User Management

For Users to be able to connect to a MicroNet application the User Management Infrastructure needs to be available. This infrastructure consists of an API Gateway Service, an Account Service including a relational Account Database, and a Session Store using a NoSQL Database and of course ActiveMQ for networking. This sounds like a lot of stuff but MicroNet makes all these services and technologies available to you in a simple way using containers (Just like you installed ActiveMQ on your system in the last tutorial without even noticing).

Make shure that the ActiveMQ container is running and remove all other running services from previous tutorials. 

## Session Store

MicroNet uses [Couchbase](https://www.couchbase.com/) as a NoSQL database to provide Session Store capabilities. Think of the session store as a shared data storage used by your Microservices to persist data for a set amount of time or infiniely. The distibuted nature of Couchbase makes it possible to make often used session data highly available using eventual consistency concepts.

> Since usually multiple instance of a Microservices are deployed to achieve reliablilty Microservices are not allowed to store session data directly in memory. Instead the session store can be used persist data between request.

Add the *mn-archetype-couchbase* archetype to your game workspace using the MicroNet Service Catalog. Perform a *Full Service Build* of the Couchbase Service Project from the Service Explorer and afterwards start Couchbase via *Start Serivce Container*.

> MicroNet uses a shell script called *wait-for-it.sh* to enshure that required services like activemq are avaible before starting a service. This however does not work for couchbase because the ports offered by couchbase are already open during initialization of couchbase and therefore starting couchbase simultaneously with any dependent container leads inevitabley to errors. It es therefore recommended to start couchbase prior to any other services to enshure availibility. If anyone finds a solution for this, please contact me.  
