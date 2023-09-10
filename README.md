# ADAPT Messenger Demo

This repository contains a demo project built using ADAPT: a simple browser-based no-backend messenger with end-to-end encryption. This example demonstrates how messages can be broadcast securely, with each recipient in a group chat receiving a uniquely encrypted message, all achieved with just a few lines of MUFL code. 

Using the messenger functionality as an example, this repository shows the robust capabilities of the ADAPT framework and MUFL, the simplicity and efficiency of building decentralized applications with ADAPT.

Navigate through the sections for an in-depth look:

0. [About ADAPT Framework](#about-adapt-framework)
1. [Messenger Architecture](#messenger-architecture)
2. [Code Structure](#code-structure)
3. [How to Build](#how-to-build)
4. [ADAPT Architecture](#adapt-architecture)

You can try this demo in [interactive codesandbox](https://codesandbox.io/p/github/adapt-toolkit/adapt-hello-world-example/release-0.1?file=/.codesandbox/README.md:1,1)

### **About ADAPT Framework**

The ADAPT Framework is used to build a mesh of interconnected data nodes that underlies an internet application. The framework eliminates the traditional REST-based web back end and introduces the capability of distributing the data, and building front-end-only and non-custodial applications. The approach ADAPT takes to building internet applications is especially suitable for handling sensitive, critical, or proprietary data, where the traditional web architecture is unfit for the purpose. 

Our mission is to offer a robust solution where existing development tools fall short in establishing customer trust towards the way their sensitive data and logic is handled. ADAPT forsters a modern approach to building both consumer and enterprise internet applications.

The ADAPT-based data mesh consists of nodes that can run identically on the client side and server side of the application. The nodes self-host the back-end business logic of the application and are connected into a data mesh. Each node is a request-response server that manages a set of data belonging to the node, termed a "packet". Every request to the node results in a state transition of its data packet. 

MUFL Programming Language is the cornerstone of ADAPT. It is a functional special-purpose programming language designed for the efficient implementation of ADAPT packet state transitions. It empowers programmers to implement the business logic of a distributed application painlessly and efficiently.

You can find more information in our documentation:
- **General:** [ADAPT Framework Documentation](https://docs.adaptframework.solutions/)
- **MUFL Overview:** [Detailed MUFL Documentation](https://docs.adaptframework.solutions/basic-syntax.html)
- **Beginner's Guide:** [ADAPT Tutorial](https://docs.adaptframework.solutions/detailed-build-example.html)

You can also find the ADAPT whitepaper, information about our team and other information on [our main site](https://www.adaptframework.solutions/).

Feel free to join our [discord server](https://discord.gg/VjKSBS2u7H) and ask all your questions! With love, ADAPT team <3

### **Messenger Architecture**

The ADAPT messenger demo implements a serverless architecture for a simple messaging application. Messages are exchanged directly between the front-end nodes that belong to individual users. In group chats, messages are broadcast, that is they are simultaneously dispatched to every other member of the group. Each message is encrypted using a distinct encryption key.

> **Example:** If a chat consists of 10 members and one of them sends a message, that single message is encrypted using 9 unique keys for the 9 other members. These encrypted messages are then sent out individually to each member.

Joining a chat requires an invite code from an existing member who serves as the group's admin. This code carries a transient encryption key used only once during the initial key exchange. After the key exchange, members can exchange encrypted messages. The inviter/admin also ensures that all members of the chat are introduced to each newcomer, facilitating an exchange of encryption and signing keys.

The extensive encryption, key exchange, and signing mechanisms discussed above are trivially implemented using features of ADAPT. These features simplify the development process substantially.

For a more exhaustive understanding of ADAPT, MUFL, and this demo, consult our [detailed tutorial](link-here), which walks you through the demo's development and deep-dives into MUFL code.

### **Code Structure**

```
|-- compile-mufl-code-in-docker.sh # this script allows you 
|                                    to compile your local
|                                    MUFL code using ADAPT 
|                                    docker development kit
|
|-- docker-compose.yml             # docker compose configuration of the demo.
|                                    it contains 2 docker containers -- broker and web
|
|-- Dockerfile                     # Dockerfile containing instructions how 
|                                    how to create a docker image          
|
|-- LICENSE                        # ADAPT license
|
|-- README.md                      # You are reading this file now!
|
|-- mufl_code                      # Directory containing all the MUFL code
|   |
|   |-- actor.mu                   # Main MUFL application implementing 
|   |                               the business logic of the messenger 
|   |
|   |-- config.mufl                # MUFL config file specifiying a path
|   |                                to the MUFL standard library
|   |
|   \-- <...>.muflo                # Compiled MUFL code
|
\-- web                            # Directory containing frontend code.
                                     For details, please refer to the source code
```


### **How to build**

To get the ADAPT messenger demo up and running, first, construct a Docker image. Then, use the docker-compose utility to initiate the containers.

```bash
docker build . -t messenger-demo # build docker image

docker-compose up -d # run docker containers

open http://localhost:8080 # open a browser tab
```

To shut down the demo, execute:

```
docker-compose down
```

If there's any modification to the MUFL code, recompilation is necessary. This can be achieved effortlessly using the compile-mufl-code-in-docker.sh script.

```bash 
./compile-mufl-code-in-docker.sh
```

Once recompiled, the frontend application will utilize this updated version automatically.

### **ADAPT Architecture**

In the context of this messenger demo, the architecture of ADAPT consists of two main components: the browser user's node and the message broker.

1. **Browser User's Node**: This node managers the user's ADAPT packet, which hosts the chat's entire business logic. The browser node supervises the user's packet, processes requests (transactions) within the packet, and stores the packet's new state post-transaction. All of these operations leverage the ADAPT JS API —- a set of TypeScript functions that enable the JS/TS web front-end to drive the execution of the MUFL code over the data embedded within the node. In browser environments, the ADAPT JS API runs on top of a WebAssembly (WASM) module, whereas in native environments, it drives a native NodeJS addon.

   > For a comprehensive understanding of the ADAPT JS API, visit our [documentation](https://docs.adaptframework.solutions/release/0.1/api-reference.html).

2. **Message Broker**: Within the MUFL code, inter-node communication is guided by packet IDs. The message broker's role is to associate these packet IDs with the actual IP addresses of the nodes. Notably, in this demo, every message that traverses the message broker is encrypted, although this is contingent on the MUFL-based application logic. Consequently, even in scenarios where the message broker's integrity is compromised, the attacker cannot glean any sensitive information.

Outlined below is the three-step communication process between any two nodes:

1. Packet A submits a registration request to the broker. In response, the broker correlates the requesting packet's ID with its actual IP address.
2. Packet B replicates the aforementioned registration process.
3. Packet A dispatches a message intended for Packet B via the message broker. The broker retrieves Packet B's IP address and routes the message accordingly.

A standout feature of ADAPT is its abstraction of the underlying communication mechanisms, allowing MUFL developers to focus exclusively on building the application's business logic and facilitating node communication. 
