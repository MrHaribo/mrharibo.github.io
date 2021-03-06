# MicroNet Quick Reference

- [Getting Started Tutorial](../index.md)
- [Quick Reference](index.md)
  - [Shared Model](#shared-model)
  - [Parameter Codes](#parameter-codes)
  - [Shared API](#shared-api)
  - [Launch Utility](#launch-utility)
  - [Game Engine Integration](#game-engine-integration)
  - [Evaluation Survey](#evaluation-survey)

Besides the core Functionality MicroNet provides additional functionality to ease the process of online game development. These concepts are mainly aimed to deal with the complex Microservice topics composition and deployment.

## Shared Model

The main concept behind the composition of a MicroNet application is to use platform independent Shared Model to define datatypes for message transfers and persistence. This concept allows Microservices to use domain objects in a type-safe way neglecting shared game agnostic libraries between services. The developer can define template types which represent the game objects of the game and MicroNet automatically generates POJOS out of the model. This follows the define once, use anywhere approach.

The shared model also features model contribution. Archetypes from the service catalog can define template types which they depend on. These required templates are then automatically contributed to the shared model when the service archetype is added. If you followed the getting started tutorial this process already happened multiple times since several model templates have been added to the shared model already like `UserValues` for example. The image below shows the Model View for the Simple Example Game. With the Template Tree the Model View provides a way to edit the Shared Model in a convenient way. It is possible to add, remove, and edit templates. 

![model-view](ModelView.PNG "Model View of the Simple Example Game")

### Prefab Tree

Prefabs are instances of template objects. Prefabs can be created from model templates and can be organized to a hierarchy of entities. The idea of prefabs is that they can be edited by the designer and then directly stored in a database to be used at runtime. Prefabs are also planned to implement an observer pattern on object level. This approach will allow the use of reactive programming on a basis of object change notification either for complete objects or for very fine grained access on individual variables of a prefab.

> At this point prefabs are only experimental and are not used for anything. 

## Parameter Codes

A MicroNet message transfer can be augmented by adding parameters to a message. Each parameter of a message is identified by a ParameterCode. The ParameterCodes are global for the whole game application. The following snipplet shows how to add and get parameters from and to a message.

```java
		Request request = new Request();
		request.getParameters().set(ParameterCode.USER_ID, 42);
		int userID = request.getParameters().getInt(ParameterCode.USER_ID);
```

The Parameter Codes of the game application can be edited via the *ParameterCode View*. It is possible to add and remove ParameterCodes. It is also shown in the ParameterCode View which codes are used by which service. Just like Model Templates, required ParameterCodes are contributed to the game workspace when a service is added. The ParameterCode View always contains the three built in codes: USER_ID, USER_REQUEST, and EVENT. These three codes are used by the framework and cannot be removed.

The image below shows the ParameterCode View for the Simple Example Game. No additional Parameters are used in this example.

![parameter-code-view](ParameterCodeView.PNG "Parameter Code View of the Simple Example Game")

## Shared API

MicroNet achieves loose coupling of services by sharing the API in a platform independent way. The so called Service API is a concept that lets the developer document the API of a service directly in the service code using MicroNet annotations and access the API of other services with the Eclipse Code Completion Feature. 

### Documenting the Shared API with Annotations

In addition to the mandatory `@MessageService` and `@MessageListener` annotation, MicroNet provides additional optional annotations to document the API for quick access in other services.

The `@RequestPayload` and `@ResponsePayload` annotations document the type of the message payload. This can be a primitive type or a model type from the Shared Model. Types which are not part of the shared model cannot be used as message payload.

The `@RequestParameters` and `@ResponseParameters` annotations allow to define an array of `@MessageParameter` annotations. Each message parameter must be defined by a ParameterCode along with the class of the payload type.

Generally all MicroNet annotations support a description field to document the functionality of the Shared API in a human readable form.  The Listing below shows an example of a fully annotated message listener function.

```java
@MessageListener(uri="/score/add", desc="Increments the score of the specified player")
@RequestParameters(@MessageParameter(code=ParameterCode.USER_ID, type=Integer.class, desc="UserID"))
@RequestPayload(value = Integer.class, desc = "Score Increment")
public void addScore(Context context, Request request) {
	int userID = request.getParameters().getInt(ParameterCode.USER_ID);
	Player player = players.get(userID);
	int newScore = player.getScore() + Integer.parseInt(request.getData());
	player.setScore(newScore);
	players.update(userID, player);
}
```
### Accessing the Shared API with Code Assist

Once API annotations exist, MicroNet uses this information to present the API to the developer. The complete API is presented in a hierarchical fashion starting with the available services. To test this feature type "mn://" somewhere in a source code and press Ctrl+Space to open the Eclipse Content Assist. You will notice that the full service proposal that is documented is available. The image below shows MicroNet Code Assist in action.

![content-assist](CodeAssist.png "MicroNet Content Assist")

## Launch Utility

MicroNet provides a series of Launch Configurations to ease the life of DevOps engineers. These utility automate major parts of the composition and containerization of MicroNet applications. The MicroNet Launch Utilities are mainly accessible via the Service Explorer.

Probably the mostly used feature of the Launch Utilities is the **Build All Services** ![build-all](build.png) shortcut. Since MicroNet performs a lot of background activities and specifically code generation, there sometimes might occur issues. To be sure that code generation was performed properly, press the *Build All Services* shortcut manually to invoke the code generation process. This often resolves reference issues. If errors remain the developer has to track them manually. Usually its just imports or package miss-matches. In future versions of MicroNet this process will be revisited to make it simpler.

### Service Launch Utilities

The Service Launch Utilities provide shortcuts to containerize individual services.

![launch-utility-service](LaunchUtilityService.png "MicroNet Service Launch Utility")

1. **Full Build Service:** This includes all steps to containerize a service, namely: perform a Maven build followed by a Docker build which packages the compiled service.
2. **Build Maven:** With this individual build step only the Maven build is executed. It is possible to containerize the compiled service later on by using the individual build step *Build Docker*.
3. **Build Docker:** Containerized a service previously compiled with the *Build Maven* command.
4. **Debug/Run Service Native:** Runs or Debugs a service as a native Eclipse Java application. This is equivalent to running the *ServiceImpl.java* class which is contained in every regular service in the *target/generated-sources/annotations* directory.
5. **Run Service Container:** Runs the previously built service container. If the container was not built this action will fail.
6. **Set Network:** Allows to define the network a service uses individually. If this is left untouched the service will connect to the network you specified globally via the preferences. Setting individual networks allows it to separate services from each other for example to reuse ports.
7. **Add Ports:** Allows to define which ports are published by the container. These ports are available on the host system. Notice that for Docker Toolbox these ports are not available via *localhost* but via the *docker-machine ip*. The format is "hostPort:containerPort".
8. **Add Native Env Args:** This utility is used to inject environment variables into native launch configurations. The goal is to achieve the same functionality as with **ENV** entries in *Dockerfiles* so the service code can be left unchanged and it is possible to inject different configurations (for examble network addresses) at startup.
9. **Edit Service Config:** This action simply the settings file of the service in the editor. This can be used to edit all three options 6, 7, and 8 at once. There are also some hidden settings that should be rarely used which are only accessible via the config file.

#### Service Config Options Overview
  - *ports*: Exactly as 7. 
  - *network*: Exactly as 8.
  - *alias*: Network alias of the service in the Docker network
  - *container.name*: A custom name for the service image
  - *enabled*: Persists is a service is enabled
  - *contribute.shared.dir*: Allows to specify Shared Model Templates which are contributed that the Service is added from the catalog.

### Game Launch Utilities

![launch-utility-game](LaunchUtilityGame.PNG "MicroNet Game Launch Utility")

1. **Run/Debug Enabled Services Native:** Runs or Debugs all services which have the **Enabled** checkbox checked. The services are started as an Eclipse Launch Group which was promoted to the Eclipse Standard Package with the Latest **Oxygen** Release. Be sure to have this version installed when using Native Game Launch Configurations.
2. **Generate Game Pom:** Updates the Game Application pom.xml file and adds all **Enabled** services as child projects. This is helpful to build the hole game at once using Maven. The Game Application pom.xml is used to execute the *Build Game Pom* Launch Action 5.
3. **Generate Game Compose:** Updates the Game Application docker-compose file which can be used to containerize the whole Game Application at once. All enabled services are added to the Game Compose File. The Game Compose file is used to execute the *Build Game Compose* Launch Action 6.
4. **Build Full Game:** Performs a complete build of the game which includes a Maven build of all *Enabled* services followed by a docker-compose build.
5. **Build Game Pom:** Builds all services at once using the Game Application pom.xml which has to be generated prior. 
6. **Build Game Compose:** Containerized all services at once using the Game Compose file which has to be generated prior. 
7. **Run Game Local Compose:** Runs the complete Game Application as a docker-compose application. The Game Application has to be built prior before this Launch Action can be used.
8. **Run Game Local Swarm:** Docker Swarm mode is not yet supported by MicroNet. But you can easily use it using the Docker CLI. Refer to the [Docker Swarm Documentation](https://docs.docker.com/engine/swarm/).
9. **MicroNet Console:** The MicroNet Console helps the developer to notice usage errors but also shows errors of the framework itself. Use it to track miss-behavior of MicroNet.
10. **MicroNet Preferences:** Shortcut to the MicroNet preference page.
11. **Enabled:** Allows to mark a service for either Native Launches and pom.xml respectively docker-compose generation. If the checkbox is checked, the state will be persisted to project preferences which can be slow, the first time.

## Game Engine Integration

A Game Engine integration component for Unity3D is in development. But this component is only experimental at this point. It will be released in the near future.

## Evaluation Survey

If you have not done it already, please be sure to fill out the [Evaluation Survey](https://docs.google.com/forms/d/e/1FAIpQLSc2mOMse_nsa6kJSQsRMN-ph_sUb4EDws9loc530sVPcMOOsg/viewform?usp=sf_link).
