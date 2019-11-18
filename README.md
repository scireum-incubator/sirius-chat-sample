# SIRIUS Chat Sample

This is an illustration project used to showcase how to setup and use the
[SIRIUS](https://github.com/scireum/sirius-kernel) libraries.

The task at hands is creating a small web server which provides a chat room
for client which are connected via web sockets. The task itself is split up
into challenges - some build up onto each other, others are more like side
quests which provide some functionality but can also be skipped.

## SIRIUS

SIRIUS is split up into distinct modules to provide a broad range of use-cases.
The modules are:
* https://github.com/scireum/sirius-kernel - Contains common code and especially the dependency injector
* https://github.com/scireum/sirius-web - Contains the the web server along with **Tagliatelle** which is our template engine
* https://github.com/scireum/sirius-db - Contains a persistence framework which helps to manage JDBC datasources, MongoDB, Elasticsearch or Redis
* https://github.com/scireum/sirius-biz - Contains a lot of high level frameworks which help to built cloud native business applications

## Setup

To save some time, the project is already setup and also contains some scaffolding for the
challenges. This project can be built using **Apache Maven** and should be developed using
**Jetbrains IntelliJ IDEA**.

To understand the inner workings first have a look at the [pom.xml](pom.xml) which tells
maven how to build this project. Then consult the [src/resources/application.conf](src/resources/application.conf)
and [develop.conf](develop.conf) to understand the configuration of the application itself
as well as the developer config which is used when starting the application locally.

(The system configuration is accessible via either [Sirius.getSettings()](https://github.com/scireum/sirius-kernel/blob/master/src/main/java/sirius/kernel/Sirius.java#L607)
or by using the [ConfigValue](https://github.com/scireum/sirius-kernel/blob/master/src/main/java/sirius/kernel/di/std/ConfigValue.java) annotation.
An example is the [WebServer](https://github.com/scireum/sirius-web/blob/master/src/main/java/sirius/web/http/WebServer.java#L82)
provided by **sirius-web**, which uses the configuration to determine which port to 
open etc.)

Speaking of which, SIRIUS applications can be started using the built-in main class
[Setup](https://github.com/scireum/sirius-kernel/blob/master/src/main/java/sirius/kernel/Setup.java).
A run-configuration for IntelliJ is present as **SERVER**.

Note one more very important file (**src/resources/component.marker**). Although this file
is empty, is it essential, as it is used by the [Classpath](https://github.com/scireum/sirius-kernel/blob/master/src/main/java/sirius/kernel/Classpath.java)
scanner of the [Dependency Injector](https://github.com/scireum/sirius-kernel/blob/master/src/main/java/sirius/kernel/di/Injector.java)
to discover all classpath roots which participate in the SIRIUS system.

## Challenges

Ok, we're almost good to go. Before diving into the first challenge, start a Debugger using the
**SERVER** run configuration to make sure that the base system is operational. This will start
a **Docker** container for **Redis** and one for **Elasticsearch** which are required for later
challenges (the stack is configured in [docker-compose.yml](docker-compose.yml)). Once a message
like `System is UP and RUNNING` appears in the console, you should be able to view the chat UI: http://localhost:9000
Note that you can also have a look at the system state via http://localhost:9000/system/state and
even monitor some details via http://localhost:9000/system/console (e.g. by executing the `http` command). 

Note that each challenge has a unique ID (like CHALLENGE-0). You can search in all files ("Find in Path")
using this ID to spot all relevant code positions. You will also find the appropriate .solutions file
which will assist i case you're in trouble :-P

### Hello World (CHALLENGE-0)

The first step is to make the server respond to incoming chat messages. These messages will
simply be sent back to the client. But first, let's find out where the client comes from. Head
over to the [ChatClientController](src/main/java/client/ChatClientController.java) and read the
JavaDoc - then come back here. 

Now that we know how the client is delivered, we can start the challenge. The client connects via
a "web socket" which is more or less a persistent HTTP connection which exchanges messages (called)
frames. (You can review the client code required to open a web socket in JavaScript - but that's
not required for our challenge - [client.js](src/main/resources/assets/client/client.js)). For each
web socket being connected, the [WebSocketDispatcher](src/main/java/server/WebSocketDispatcher.java)
will detect this and initialize a [ChatSession](src/main/java/server/ChatSession.java). Head over
to the session class to read the provided docs and finally to start some coding :)

One the challenge is completed, you should be able to chat with yourself +1

### Hello Everyone (CHALLENGE-1)

For the second challenge, head to [ChatSessionRegistry](src/main/java/server/ChatSessionRegistry.java) and
implement the missing parts. Then switch to [ChatSession](src/main/java/server/ChatSession.java) to call
the appropriate methods.

Once this is all done, restart the Debugger and open two or more tabs. You should now be able to chat
with each other.

### Hello HA (CHALLENGE-2)

The third challenge starts by completing the [ChatClusterUplink](src/main/java/server/ChatClusterUplink.java).
Once this is done, change the [ChatSessionRegistry](src/main/java/server/ChatSessionRegistry.java) as noted
in the JavaDoc.

After restarting the server, the chat should continue to work. Now if you specify a shared Redis server in
[develop.conf](develop.conf) you should be able to share your chat with other machines.

### Ahoi World (CHALLENGE-3)


### Side-Quest: HA-Setup (CHALLENGE-4)

### Side-Quest: Chat-Bots (CHALLENGE-5)

### Side-Quest: Rate-Limiting (CHALLENGE-6)

### Side-Quest: Search (CHALLENGE-7) 
