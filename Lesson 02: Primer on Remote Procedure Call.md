# Lesson 2: Primer on Remote Procedure Call

Source: [Lesson 2 — Video](https://www.youtube.com/watch?v=q-XBIen19Pw)

## 1. Introduction

The topic of RPC is covered in other courses in the program, in introduction to operating systems and advanced operating systems. So you can consider this lesson as a primer for those of you who have not taken those courses previously.

In this lesson, we will briefly review the basic elements and mechanisms of client server architectures. We will spend most of the time on remote procedure calls, RPC, and we will describe the idea and the functionality enabled by RPC systems and the underlying mechanisms.

## 2. Client-Server Architecture

### 2.1. Requests and Responses

A common pattern for building distributed applications is that of client server. One or more nodes in a distributed system are clients. They send requests of data or some processing to other designated server nodes. The server node which receives the request, retrieves the data with maybe a file or an object from a database, for example, and responds to the client request with it. Or the server performs the requested processing and returns the result to the client.

To return the requested data or processing result, this means that the server copies this data from the server memory or storage into one or more network packets, which are then transmitted to the client.

For the server to perform the requested operation, typically a client may need to provide some arguments: the name of a file, some parameters for the operation that is requested. The client would also need to copy this data from its memory into network packets and send those out.

### 2.2. Finding and Connecting to a Server

Before this message exchange happens, the client needs to identify the server it is going to contact, and if necessary, to establish a connection with it. I'm saying if necessary, because protocols such as TCP require that two endpoints establish a connection before they can send or receive messages. Other protocols such as UDP don't require an ahead of time connection. However, even in that case, the client still needs to find and decide on the server that it will contact and have the messages properly addressed.

I should note that although this illustration shows two machines connected via network, this would look similar if the client and server application were sitting on the same machine. It's just that in that case, the communication channel doesn't have to be based on a message-based network transport and could be based on shared memory.

## 3. Challenges in Client-Server

### 3.1. Agreeing on Operations and Data Representation

What are some of the things that are difficult when it comes to client server architectures? Well, we have already mentioned some of them. The client needs to find the server it is going to contact, and if necessary, to establish a connection. It needs to know ahead of time how to identify the operation it wants to request, and which parameters is it allowed to pass.

For all the data that will be exchanged, the client and the server need to be in agreement how that data will be represented. What is the byte format of data types such as integers or floats? Are strings going to be terminated by a null character? If there are multiple elements in the data, which of the elements will be transmitted first? Which one second? Otherwise, when they receive a message from the other process, it will be just a sequence of bytes and they may not know what to do with them.

Then, the operation parameters from the client, and later the results from the server, need to be copied to and from network buffers so that they can be transmitted.

### 3.2. Delays and Failures

The client needs to wait for the results, potentially unknown amount of time. If there is no response, the client does not know whether there is a simple network delay, or whether the server is slow in responding, maybe because it's overloaded temporarily, or maybe there is some sort of failure. If the client suspects a failure, it does not know what caused the failure: whether it is a network problem and its request was never received, or the server crashed, or the server has completed the processing but the response got lost or corrupted in the network.

If you have ever implemented a client server program, you probably recognize that you had to decide what to do with all of these points and to implement those design decisions yourself.

## 4. Role of RPC

### 4.1. Making Remote Calls Resemble Local Calls

So what are the goals of an RPC system? One way to address these challenges, these questions that we raised earlier, is to simplify the development of distributed client server applications via the use of an RPC system. As a high level goal, an RPC system, or a system for remote procedure calls, aims to hide the complexity of programming distributed systems. Its goal is to make it possible to program distributed systems more easily, or even in a manner that's as similar as possible to programming single node systems and applications.

At the time when the need for these kinds of systems appeared, a popular way around which applications were structured was through use of procedures and procedure calls. Procedures encapsulate some functionality, maybe some local state. For instance, the functionality may be adding to integers. The procedure call passes arguments to the procedure, and then the result is returned.

The combination of these two observations, that distributed programming was to be made as similar an experience as that of programming local programs, aimed the fact that local programming was centered around procedures and procedure calls, led to the idea of remote procedure calls, or RPC.

### 4.2. Discovery, Connections, and Data Management

More concretely, the goals of an RPC system are to address the specific challenges we discussed. It must provide support for servers to register the services they provide, and for clients to then find out that information. It must provide support for any necessary connections to be established among a client and a server process using appropriate protocols. There must be support that will ensure that the client can find out what are the necessary service parameters, and the results that can be expected. Also, that the client and the server will be able to agree on the data types of the parameters and results.

The runtime system must be responsible for any data management that's necessary to take the data from a process memory and serialize it into byte stream that can be packetized and transmitted. This process is also referred to as serialization or marshalling. Or in the opposite direction, when data is extracted from the packet bitstream and used to populate in-memory data structures, it's called deserialization or unmarshalling. The serialized stream is not going to have the raw data only, but it will also have some additional information, some metadata that identifies the procedure, or maybe further describes the data types.

### 4.3. Handling Failures

Then an RPC system must provide support for dealing with failures. Maybe these are transient failures, so some mechanism that times out and retries the same operation again will be sufficient. Or perhaps there are some more permanent failures, in which case we cannot expect magic. The RPC system will have to pass some error message to the user or to the application level program.

## 5. Architecture of an RPC System

### 5.1. Programming Interface, Stubs, and Runtime

Let's discuss the architecture of an RPC system. The architecture of an RPC system contains several components. At the topmost level is the programming interface that client and server applications use to interact with the system. When clients make a request, they make a call to something that looks similar to a procedure. However, instead of having the program counter jump to a location in the address space that holds the implementation of the actual procedure, the RPC call results in a jump into the stub layer. This layer has knowledge about the remote procedure, its arguments and results, and will perform all steps required for marshaling and our marshaling of the data and parameters.

At the lowest level is the RPC runtime, responsible for tasks such as connection management, sending and receiving data, dealing with failures, etc.

### 5.2. IDL, Compiler, and Registry

There are few other important components of the system. One is the interface definition language, IDL. This is what is used to create an interface specification. The IDL is the agreed upon way in which servers describe the services they provide and their associated data requirements. The idea is that any client that knows the IDL and has the interface specification, it would be able to determine how to interact with the server.

The second important component is the RPC compiler, which takes the interface specification and generates automatically a lot of code that is used by the stubs and the runtime. Without this compilation step, programmers would have to manually write the corresponding code.

Finally, an RPC system typically establishes some rules for how servers would announce their services and would become discoverable, and this is some registry service.

### 5.3. Building Client and Server Programs

The way the RPC system is used then is as follows: the server developer implements a procedure, let's say add, and provides a specification written in the IDL. The specification is compiled with the RPC compiler. The compiler generates the code for the stubs and even the skeleton of the entire server code. The server process is created by adding the implementation of the ad service to the skeleton and then is registered with the registry.

The client developer writes the client application referring to remote operations such as add, using an appropriate interface, and then just compiles the code together with the automatically generated object files produced from the RPC compiler. At runtime, the RPC runtime takes care of everything else.

## 6. Anatomy of an RPC Call

### 6.1. A Calculator Service

Let's illustrate the typical operation of an RPC system by tracing down what happens during a call. We will use the same example where the server provides the operation of adding two numbers as a service. And in this example, the server process, running potentially on a separate machine, is the one that knows how to implement the addition operation, and the client doesn't. To illustrate the structure of the RPC system, I will walk you through this example.

Consider a client and a server. The client wants to perform arithmetic operations, addition, subtraction, but doesn't know how to. The server is the calculator process. It can do these operations. Whenever the client needs some arithmetic operation to be performed, it needs to send a message to the server with the arguments of the operation, and the server has the implementation of that operation, so it will perform the processing and return the results. To simplify all communication related aspects of the programming, creating sockets, allocating and managing buffers, etc, the client uses RPCs.

Let's consider in this example that the client wants to perform an addition, to add inj and obtain the results of this in a variable k. The client doesn't have the implementation of add. Only the server knows how to do the addition. However, with RPC, the client is still allowed to call something that looks just like a regular procedure. They would call k equal add of i and j.

### 6.2. The Client Stub

In a regular program, when a procedure call is made, the execution will jump to a point in the address space which has the implementation of that procedure, meaning that the program counter will be set to some value that corresponds to the first instruction in the procedure. In this example, when this RPC ad is called, the execution of the program will also jump to another location, but this won't be the real implementation of add. Instead, this will be a step implementation. From the rest of the client's processes perspective, it will look as if that's a real ad, but internally, it will do something entirely different.

The responsibility of the client step is to create a buffer, populate that buffer with appropriate information. In this case, that's a descriptor of the function that the client wants the server to execute at, as well as of its arguments, the integers i and j. Here the step is code that is automatically generated via the tools that are part of the RPC package. The programmer doesn't write this code. So when the client makes this add call here, the call takes the execution of the client process into the RPC runtime. That's the system software that implements all of the RPC functionality. And the first step here is the stop implementation.

### 6.3. Sending and Receiving the Request

After the buffer is created, the RPC runtime will send the message to the server process. This may be vsa TCP, but what we're not showing in this illustration is that the information about the server, like its IP address for instance, is available to the client and will be used to establish a connection and to carry out the communication.

On the server side, when the packets are received on this connection, they will be handed off to the server stack. The server step is again a portion of the RPC runtime. This is code that will know how to parse and interpret the received bytes, aim to determine that this is an RPC request from a client for the procedure at and with arguments i and j. The server side code will also understand that i and j are integers, so it will know how to extract the correct number of bytes from the byte stream that arrived in the packet, and it will know how to allocate variables that are of the integer data type that will be initialized to the values of i and j.

### 6.4. Executing the Procedure and Returning the Result

Once all this information is extracted on the server side, the stub will call into the user level server process which has all of the actual implementation of the various operations that this server supports. In this example, this will be the actual implementation of the add procedure.

Once the result of the ad is computed, it takes a reverse path through the server-side stop that will first create a buffer for the result, then send the respond back via the appropriate client connection, then into the client site RPC runtime, where the packets will be received, the result will be extracted from the packets and placed in memory, and ultimately, the procedure will return to the user level client process.

## 7. Invocation Semantics of RPC Operations 1

### 7.1. Synchronous Operations

We can distinguish RPC operations among multiple dimensions. One dimension that is important for us to highlight now is whether they're synchronous or asynchronous. When using a synchronous RPC operation, the client makes the request and waits for the response. If the client has multiple threads, other threads may be doing other things, including making other RPC calls, but each of those threads will wait for the response once an RPC call is made, and only afterward proceed to perform the next steps.

### 7.2. Asynchronous Operations

When using asynchronous RPC operations, the client makes the call, but is free to do other things until the response arrives. A nice property of asynchronous operations is that they hide latency. The client can fire off the RPC operation, and instead of just waiting, and that wait will be longer the longer the network latency is, the client in this case can complete other tasks.

When the client completes all other possible tasks, which do not require the response from the RPC operation, the client checks whether the response has arrived. If yes, then great. It obtains the response and moves on. Otherwise, the client will have to wait. But note how this wait time will likely be much smaller than the wait time in the synchronous case.

### 7.3. Callbacks

If the client wants to be notified as soon as the response is available, the client can specify the operations that are to be performed when the response arrives. We call this registering a callback.

## 8. Invocation Semantics of RPC Operations 2

### 8.1. Local Calls and Remote Uncertainty

Another dimension that can be used to distinguish among different types of RPC systems is based on the guarantees they make regarding the delivery of the RPC call. When we make a local procedure call, it will execute. It will execute exactly one time and produce results which will be used by the calling thread and or process. If there is a problem and the procedure hangs and is not returning, or if the program otherwise crashes, we can always correctly assume that the procedure never executed. And it's okay even if the failure occurred right after the result was about to be returned. All data is in DRAM. It will be lost, and it will have to be executed and new when the process is restarted, as if it never happened before. Or in the case of local procedure calls, we always have a way to know for sure whether or not there was a problem during the execution of the procedure because the color of the procedure is immediately affected by it.

In a distributed system, there are no guarantees that the server will respond. However, not receiving a response doesn't really tell us anything about whether or not the server performed the operation. It is possible that the server completed the operation and just the result got lost. Rerunning this operation twice may corrupt the application datum. Say, if it's an operation to update the same variable, to increment the same variable.

### 8.2. Exactly-Once Execution and Duplicate Requests

Ideally, we would like the RPC system to guarantee the same type of exactly one skull semantics as what we have with local procedures. That means at least that the RPC runtime would perform automatically some form of retransmission when there is no response. In addition, there must be some mechanism for the server to distinguish repeated requests for the same RPC operation, so it does not keep redoing it over and over again.

In some cases, such as when adding a plus beam and both arguments are specified in the RPC call, it's okay to redo the operation. We will still get the same result. In other cases, repeatedly executing the call is a very bad idea. For instance, if the RPC call is used to decrement a counter, maybe someone's account balance, or to similarly update some state in some incremental way. In those scenarios, we have to make sure we can detect and eliminate duplicates.

However, if there is a more permanent issue with the server, or with the connection to the server, the RPC runtime really has no way of guaranteeing that this exactly one semantics can be met. For such scenarios, we will need to do something different.

### 8.3. At-Most-Once and At-Least-Once Semantics

Clearly, what's more practical to expect is that the RPC system supports at most one semantics. The difference between explicitly saying that the call semantics are at most ones, is that the client or the developer of the client application knows that that is the guarantee the RPC system will provide, and it can program the application such that it behaves correctly even in the case if the call is executed once successfully, or in the case if it's not executed at all.

Another option is that the RPC system keeps re-transmitting the request, but if there are no guarantees that it will remove duplicates, then what we'll end up with is at least one semantics.

### 8.4. Calls to Replicated Servers

There are other possible types of semantics that an RPC system may choose to guarantee to the upper level applications that will rely on it. For instance, an RPC system may be built to work with multiple server replicas, and then it may guarantee that the calls will be replicated to all replicas, or that the calls will be replicated at least to one of the replicas, and so forth.

## 9. Examples of RPC Systems

Many RPC systems have been developed over the years. Let's discuss some examples. We mentioned Sun RPC. This was the original RPC system design and implementation from the early 1980s and was done by Sun Microsystems.

Older systems and protocols that have been broadly used in enterprise solutions include SOAP and Cobra. More recent systems built with different internet services in mind include Apache Drift and gRPC. And there are many RPC systems specialized for certain contexts. For instance, for high-end data center systems with ultra high bandwidth, low latency, and very reliable networks, or for embedded environments where there is a lot of optimizations that are necessary in order to minimize the resource footprint.

## 10. Examples of RPC Systems: gRPC

### 10.1. Protocol Buffers and Generated Code

A popular RPC system used today is gRPC. GRPC is an RPC implementation released by Google around 2016, and it's inspired by the original Sun RPC system. It relies on Protocol Buffers as what provides the functionality to describe the interface and the data types and to perform the data serialization. In gRPC, the interface is specified in a dot protofile. It is then compiled with a product compiler to generate the code for the appropriate gRPC routines. And this supports a number of different languages: C plus, Java, Python. You can find the documentation in the full language specific API of gRPC at the gRPC's website gRPC.io. We will look very briefly at the hello world example from the main gRPC website. Our goal will be mainly to highlight the different components and to give you a flavor of the differences among gRPC and some other RPC systems you might have previously looked at.

### 10.2. The Greeter Service Interface

Let's look first at the profile in this example, which is the specification of the service. In this example, the service called Greeter has two RPC procedures called Say Hello and Say Hello Again. Both procedures take one input message of type Hello Request and return a result of type Hello Reply. The message type for Hello Request is defined as having one required field. This field is called name and is of string type. String is one of the data types that are predefined in gRPC, but there is a way how to specify complex data types as well. Each of the elements in the input output data types is identified by its number. Hello Reply here happens to have the exactly same data type as Hello Request.

### 10.3. Compilation and Service Implementation

When this dot protocol is compiled with a C plus language option, it generates the protocol buffer serialization and deserialization routines for messages of type Hello Request or Hello Reply. This will be in files with extension.pp, and any C plus plus code or header files that are needed for the subroutines, are also going to be generated both for the client side and the server side code.

Next, here is the example of the actual service implementation. This is what will be executed in response to each remote procedure call. The illustration shows the implementation of one of the RPC procedures, Say Hello Again here. This procedure combines a string called prefix, which has a value hello again, with the value of the field name in the input message request, which is of type Hello Request. The combined string is set to the value of the output message reply, and this is of type Hello Reply, and this completes the operation successfully.

### 10.4. The Client Program

In the client program, we will create a client context with the gRPC runtime for the Greeter service. Then we can call the RPC operations that this service provides, like Say Hello for instance. The actual call to these operations results in a call to the corresponding method in the stub layer. The full code listing is part of the grpcu tutorial on the main gRPC website.

## 11. Summary

In this lesson, we briefly reviewed remote procedure calls. We said that RPC is a basic mechanism for building client server distributed systems. We briefly reviewed the requirements and the main components of an RPC system. We provided some examples. Many of the upcoming lessons and papers discussed in this course will be built on top of an RPC system with similar features as what we discussed here.
