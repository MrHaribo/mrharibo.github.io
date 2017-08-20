# Simple Example Game

In this last step of MicroNet Getting Statred Tutorials you will add game functionality to build a complete game. The example game is very simple example game application but gives a good overview how to develop games with MicroNet. The example game is played in rounds of 10 seconds where each player can submit a guess and gets points based on the proximity to a random number guessed by the game application. A score ranking of all players is presented to the player in the TestClient.

* [MicroNet Documentation](../index.md)
  * [Getting Started](../gettingstarted/index.md)
  * [Example Game Tutorial](./index.md)
  * [Quick Reference](../quickreference/index.md)
  
## Player Session Management

The design of the Example Game foresees that a player session is created for every player that connects to the game. A Player Service is responsible to provide an interface to access player session. Add the *mn-archetype-playerservice* to to the game workspace. Just like the GatewayService the PlayerService needs access to the session store. So make shure that couchbase is runnion before starting the PlayerService.

One thing that is left to do for the developer is to decide at what moment the player session is addes. The simplest possibility is to add the session right after a successful login. A place to do that is in the Login Service before sending the loging response. In the `onLogin` method add the required code to send a *mn://player/add* to the Player Service. The following code snipplet shows how the `onLogin` method looks like after the addition. 

```java
@MessageListener(uri = "/login", desc="Attempt to log in a User.")
@RequestPayload(CredentialValues.class)
@ResponsePayload(value=Integer.class, desc="UserID of the User that logged in")
public Response onLogin(Context context, Request request) {
	CredentialValues credentials = Serialization.deserialize(request.getData(), CredentialValues.class);
	UserValues user = database.getUser(credentials.getUsername());

	if (user == null)
		return new Response(StatusCode.NOT_FOUND);
	if (!credentials.getPassword().equals(user.getCredentials().getPassword()))
		return new Response(StatusCode.UNAUTHORIZED);

	Request addPlayerRequest = new Request(credentials.getUsername());
	addPlayerRequest.getParameters().set(ParameterCode.USER_ID, user.getId());
	context.sendRequest("mn://player/add", addPlayerRequest);

	return new Response(StatusCode.OK, Integer.toString(user.getId()));
}
```
