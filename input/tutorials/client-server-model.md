Description: The Client/Server model
Published: 2018/12/18
Category: Additional Reading
Order: 1
Author: Steven Blom

---

Minecraft's Client/Server Model
=
If you've read through the [Getting Started](./getting-started) tutorial, then you probably would have noticed that scripts are divided into two folders, Client and Server.

If you're unfamiliar with the terms, You can think of it this way

| Client is responsible for                                            | Server is responsible          |
|----------------------------------------------------------------------|--------------------------------|
| The logic for the game                                               |                                |
| user interactions are mapped to intentions (walk, jump, attack, etc) | Acts upon those intentions     |
| Updating each client about each other's actions                      |                                |
| Rendering the game world for you to see                              | Storing and updating the world |
| Rendering and animating entities                                     |                                |
| Playing music and audio                                              |                                |

Regardless of if you are playing together with friends or on your own, your client will be connected to a server somewhere. In the event that you are playing on your own, or you have invited friends to join you in a world you are playing entirely on your own computer, you will be using the Embedded server.

![Embedded Server](/assets/Tutorials/Events_EmbeddedServer.svg)

If you were connecting to Realms, another 3rd party server or running a private server independent of the client, you will be connecting to the Dedicated Server

![Dedicated Server](/assets/Tutorials/Events_DedicatedServer.svg)

Whether you are connecting to the embedded or the dedicated servers, it is worth recognising the division of responsibilities. The client is for showing the game to the player and gathering their input. The server is responsible for acting upon that input, changing the world as appropriate, and then reporting the change back to the client.

It is worth thinking about this when trying to decide whether you need to perform some task on the client-side script, or the server-side script.