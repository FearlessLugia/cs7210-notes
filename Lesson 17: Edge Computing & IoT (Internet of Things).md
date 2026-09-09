# Lesson 17: Edge Computing & IoT (Internet of Things)

Source: [Lesson 17 — Video](https://www.youtube.com/watch?v=TRxUTGHvNOU)

## 1. Introduction

In this lesson, we will talk about distributed edge computing and IoT.

We will summarize recent trends around new types of components and infrastructure in the computing landscape. Specifically, we will touch on edge computing and the Internet of Things and give few examples of how these trends change the distributed systems assumptions and designs.

## 2. Tiers in Computing

Let's first see what is edge computing.

### 2.1. Cloud Services and End-User Devices

Traditionally, we have end-user applications and devices interact with services deployed in remote cloud data centers. This is true both for traditional client devices and their applications, such as smartphones, for various types of connected devices coming online as part of the emerging application tiers, such as visual analytics or connected or smart cities and transportation, or for new applications classes that are gaining popularity, such as AR and VR.

### 2.2. Bandwidth and Latency Requirements

These new types of applications are creating huge demand for bandwidth. For instance, video forms a large percentage of the Internet traffic and video-based traffic is on the rise as a combination of both high-definition video and new applications.

They also create demand for latency. Many of these application classes require low response times. For AR or VR, depending on whether this is interactive or multi-party, these requirements are somewhere between 5 and 20 milliseconds. These requirements cannot be met with current service deployments based on remote clouds.

This is a combination of limitations related to physics, as in the speed of light, simply the cloud data centers are not guaranteed to always be close enough, and also the fact that the infrastructure and the energy that's required to move data to and from remote data centers is costly. It requires investments that may be prohibited for many scenarios.

### 2.3. The Edge Tier

In response, a new tier of the infrastructure is emerging outside of data centers in the access regions of the network closer to end-users and devices. This can come in many shapes and forms.

Microdata centers deployed in enterprise locations, such as in Starbucks or Chick-fil-A restaurants, deployed in vehicles, or even in some portable form factors.

### 2.4. Low-Power Devices and IoT

Finally, there is another emerging tier based on very low-end and low-power devices. To encapsulate some basic sensing capabilities and connectivity that will allow them to generate data and send that data, and potentially also some small amount of on-device compute capabilities to perform certain classes of data processing, or to act as actuators so that certain actions can be triggered in the environment.

Many of these do not have a reliable source of energy, and the programming applications around these devices, which are accessible only in some intermittent manner, presents new challenges beyond what we've experienced with traditional embedded computing systems.

### 2.5. A New Computing Landscape

In a recent paper, The Computing Landscape of the 21st Century, presented at Hot Mobile in 2019, Satya and his co-authors identified these four tiers of the modern distributed systems as presenting a new computing landscape.

The differences among these tiers with respect to the device capabilities, the usage models, and the application requirements are so stark that this trend presents a need and opportunities to change the design assumptions around the distributed system designs in these new environments.

## 3. Why Edge Computing?

Let's look a little bit more at the edge computing tier. You may be hearing the buzzword, but what really is the motivation for this trend?

There is a continuous increase in traffic volume, in number of devices, and in wireless bandwidth.

Cisco, for instance, publishes frequently a report called the Virtual Network Index, which tracks these trends. If we take a look at one of these reports that presents this historical data and projections for a five-year period, we observe this increase both in the number of connected devices, in the amount of raw data, and also in the demand for wireless connectivity.

This demand, in large part, is created by the emergence of new types of workloads, which demand more bandwidth, which demand better connectivity with better latency guarantees, or a combination of both.

### 3.1. Changes in Connectivity Demand

Also, providing connectivity requires capacity forecasting and planning to determine where and how much infrastructure should be deployed. The pandemic has shifted this almost overnight. This heat map of the Bay Area illustrates the change in connectivity demand in the Bay Area in March 2020, with the red area seeing decrease and the green area seeing increase in the demand for connectivity. You can notice that the increase is not in the traditional hot spots of the technology companies. Instead, it's more in areas where people live in residential areas.

The graph below shows a typical diurnal traffic pattern on a typical university campus in the U.S., with peaks corresponding to working hour times and the purple area here corresponding to a weekend. As the U.S. went into lockdown on March 13, 2020, the traffic on that following Monday was just a blip compared to what was expected.

And some form of these new trends will likely stay beyond the pandemic, given that many companies have adapted remote work on a more permanent basis.

In these scenarios, connectivity is not just about games and videos and infotainment. We rely on proper connectivity and good connectivity for healthcare, for jobs, for education, for basic livelihood services.

## 4. Closing the Latency/Bandwidth Gap

How do we close this gap?

### 4.1. New Network Technologies

One way to do this is to rely on new technologies such as 5G and beyond. However, while technologies such as 5G offer promise, their deployment is costly. It will take years before they have meaningful penetration.

If we take a look at these figures here, they point to the fact that today, even older technologies such as 2G and 3G have significant global presence. And even in developed countries such as the U.S., there is a major difference among the level of connectivity in terms of data speeds across different regions.

So basically, we cannot just assume that we will roll out the new technology generation and address all the bandwidth and latency limitations that are arising today.

### 4.2. Open-Source Infrastructure and Cost

Part of the reason why the rollout of new technologies is slow is related to cost. With respect to particular the mobile network infrastructure, one of the things, one of the trends that address the cost factor is a movement toward open-source software stacks and commodity hardware.

There are a number of open-source efforts that provide hardware specifications, that provide open-source implementations of mobile network stacks, including the radio stacks and the stack for the core of the network, as well as prototypes of mobile network systems that sort of allow some sort of federated model of different participants to engage in the network.

Some of these efforts have roots in academic projects, such as a project presented at NSDI about an open-source network to address some connectivity gaps in certain regions in Southeast Asia, but many of them are backed by real commercial entities, big companies, both from the telecom space and also, in general, companies such as Facebook.

### 4.3. Moving Computation to the Edge

These efforts show promise, but there is another way that as technologies that we're trained to solve resource bottlenecks.

The commoditization of the communication infrastructure is creating opportunities to tap into the compute and storage resources at the edges of the network and then to use them to migrate some workloads from a remote cloud data center closer to the devices and the end-users' applications. We refer to this trend as edge computing.

The industry standard term started as mobile edge computing, where mobile referred to the edge infrastructure in the mobile networks, but then the MEC acronyms were subsequently extended to refer more generally to the access tiers of the network and now stands for multi-access edge computing.

What this approach really does is it creates one type of resource for another. So instead of solving this gap by finding ways how to provide more connectivity with respect to higher bandwidth, slower latency, we use these computational resources that are available closer to the network to essentially satisfy the demand for that connectivity.

As a concept, edge computing can be pretty broad and it can refer to infrastructure in different form factors, integrated in the end-to-end communication paths, both in cellular towers or purposely deployed at specific locations, can be based on traditional server systems or certain types of hardened compute devices and so forth.

## 5. Is Edge Computing New?

Now, is edge computing really new? We have had different types of content delivery networks for a long time. The same report from Cisco that summarizes certain trends regarding the use of the network infrastructure in the world today summarizes that over half of the Internet traffic, even back in 2017, has been served through CDNs and that number is just growing at a very substantial rate.

Now CDNs, as we know, are formed by servers deployed at different locations, many different locations, closer to the end-users with the goal of offering to end-users better connectivity with lower latency and in that manner also reducing the backhaul bandwidth demand on the Internet infrastructure. In that sense, this has a very similar goal to what we said edge computing is trying to solve.

### 5.1. Comparing CDN and Mobile Infrastructure

However, if we look in more detail at the CDN solutions, we'll see that we're talking about deployments of infrastructure. It's something that's on the order of a few thousands of locations globally. In addition, this infrastructure is largely owned and operated by the CDN providers themselves.

If we look at the mobile infrastructure alone, we observe that there are one to two order of magnitude more infrastructure points. These maps are based on data gathered from the FCC a few years ago in 2017. The FCC requires that any antennas, any points of presence are registered with the FCC and so this is where the data is coming from. And we observe that there's something on the order of several hundreds of thousands of cellular tower locations in the U.S. alone and tens of thousands of locations of central offices that are used by the mobile network companies.

This is a substantially larger distribution of these edge compute endpoints compared to the few thousands of global locations that we reported regarding the CDN.

To leverage this infrastructure, mobile network operators are also partnering with traditional cloud providers with the goal of transforming this new compute tier into the next cloud frontier.

### 5.2. Different Types of Edge Locations

That doesn't mean that the mobile networks are the only solution for the edge tier. A report called State of the Edge provides a taxonomy of the different types of edge locations and that includes the wireless or the cellular access points, aggregation points deeper in the network, but also an edge tier that's coupled with the devices themselves. For instance, a drone or a car or a camera can both be viewed as a device generating data but also they have sufficient compute resources to offer edge compute capabilities for certain classes of applications.

These various edge tiers will require proper software stacks in order to be effectively used, managed, programmed, and there are a number of solutions that are emerging broadly in the technology landscape.

## 6. Edge Computing Drivers

Let's look again at the drivers behind edge computing. We said speed of light is one of the drivers. We have to have the ability to deploy a service close enough.

The increase in data traffic creates a demand for bandwidth that on one side could be addressed with more investment in just bandwidth capacity in wires and in fiber links, but the flip side of that is it generates energy demand and this presents some fundamental challenges and limitations as to how much data we can drive to a data center.

There are also regulatory reasons why edge computing is necessary. So-called data sovereignty laws, privacy laws, such as GDPR in Europe, pose some restrictions on where data can be transferred to processed access and this demands localized data processing.

A number of killer apps are emerging as potential drivers for MEC and they drive their own requirements on the edge computing infrastructure and software stack.

Interestingly, while 5G is both an enabler for MEC in many of these use cases, it is also a driver for MEC. Some of the processing and signaling requirements of 5G precisely rely on the presence of distributed compute further out at the edges of the network.

### 6.1. Latency Requirements

In terms of latency, some common numbers around MEC and edge computing in general include mentions of latency guarantees sub 10 milliseconds or sub 20 milliseconds.

This is an interesting illustration that was presented a few years ago at a keynote given by Pablo Rodriguez from Telefonica and it classifies a number of different use cases in these different latency bands.

And the latency requirements for the use cases are derived based on different things. It may be that there are some regulatory requirements and it may be because of how humans operate, because of biology or because of physics.

The point is that there are a number of important use cases which we see as driving some of the next years of the applications out there that really cannot be met with today's CDNs and cloud data centers.

If we think about today's cloud and CDN infrastructure, there are ways to reach these numbers over a wired infrastructure, but the combination of adding also a wireless hop in the end-to-end times necessitates support from a new infrastructure tier.

### 6.2. Bandwidth Requirements

With respect to bandwidth as a driver, in a recent paper, The Emerging Landscape of Edge Computing, a group of researchers from Microsoft included a survey of the available data rates for different types of technologies in different countries. We observed that there is a large range of differences across both countries, across technologies, and with some exceptions, most of these exhibit an order of magnitude difference in the download versus upload capabilities.

And this is a problem since many of the use cases we mentioned have similar or even higher demand for uplink bandwidth.

### 6.3. Examples of Edge Adoption

The same paper also surveys the current adoption of this trend and a number of different use cases across different industries.

Chick-fil-A, for instance, the Atlanta-based fast food chain, has an in-house technology group focused on edge computing, very sophisticated services ranging from support for long-term planning, customer experience, to actually controlling the preparation of the perfect chicken nugget.

A project from Microsoft Research called FarmBeats developed services for low-cost monitoring of large agricultural areas using a combination of mobile drones, low-cost base stations, and also the unused portions of the TV spectrum called white bases.

## 7. Distributed Edge Computing

So what is so different about edge computing compared to what we have been doing so far when it comes to distributed computing? Can we not just take the same set of technologies used in data centers and just use them across the distributed resources at the edge?

A number of characteristics make the considerations of the edge environment different from the assumptions used when designing for clouds.

### 7.1. Scale and Geodistribution

One difference are the scale and the geodistribution. As we pointed out, the scale and the geodistribution in the two environments are completely different. While data centers may have extremely large numbers of components, those are more tightly coupled.

So if we take the same protocols that we use in data centers and deploy them at the edge, we may find out that they're very chatty. There are a lot of timeout messages, heartbeat messages, and these kinds of messages when they have to travel over long distances of our wide area networks, they may end up introducing overheads that will really obviate any of the benefits that we expect from the edge.

### 7.2. Limited Elasticity

Second, the edge is not elastic. Much of the data center systems are designed with assumptions that there are more nearby resources. In fact, there is almost an assumption that there are limitless amount of resources, at least when it comes to the compute and storage.

But the edge is not elastic. To meet the requirements we talked about, a service may need very specific edge location. And if there aren't sufficient resources there, it is as if the service is completely unavailable. We cannot simply just run it on another CPU at another edge location.

Because of this, solutions which trade resources for some potentially reduced service level will become much more important.

### 7.3. Mobility, Churn, and Reliability

Properties such as mobility, device churn, decreased reliability, those also differ in the two environments. Data center solutions are designed with fault tolerance in mind, and there is an assumption that nodes will fail or will be added dynamically. But the networks, the data center networks specifically today, are largely designed with fairly reliable and high-performance technologies, and that certainly is not the case for the edge.

As a result, the degree of churn that exists when considering the types of devices at the edge and their mobility can be order of magnitude more significant than in data centers. And because of that, any of the data center fault tolerance solutions will end up exhibiting much more significant levels of overheads and may even end up getting rendered completely ineffective.

### 7.4. Heterogeneity and Localized State

There is also a much greater degree of heterogeneity in the types of devices that we see at the edge in their compute and communication capabilities. Many of the designs we discussed in this class make some assumptions about some symmetry existing among nodes when either server node can perform some functionality, but this cannot be applied in the edge settings.

When it comes to edge computing, we will see that there is a greater degree of localization of certain types of properties, certain type of state at specific locations, whereas in a data center server infrastructure, you may find that the kinds of state and the kinds of processing actions that take place across different servers are much more homogeneous in a sense.

### 7.5. Ownership and Security

Also, when it comes to the edge infrastructure, it's much more common to expect that not all of the resources will be operated by a single provider, whereas in a data center that's largely not the case.

And finally, there is a significant difference in the security assumptions at the edge, which are largely ignored when it comes to the cloud.

Techniques that are built in with proper assumptions will be important to deliver a robust and scalable edge infrastructure.

## 8. IoT and Distributed Transactions

So let's look now at an example of how distributed computing concepts may need to be extended or modified as a result of these new infrastructure tiers and applications.

For instance, consider edge services supporting IoT-based applications such as those in a home. Backend services deployed in the cloud interact with one or more gateways in the home, and these in turn interact with multiple smart devices, which provide updates from the environment that they sense, and they also present some interface to actuators that accept commands or messages to trigger some actions in the home.

We'll look at some examples from the paper Transactuations, where transactions meet the physical world. It was presented at the USENIX annual technical conference in 2019.

### 8.1. Intrusion Detection and Physical State

Here's an example that's shown in the paper that involves an intrusion detection application, a couple of types of devices, motion detection device, and an alarm.

If we program an application for this kind of environment, it may look like something like what's illustrated here. The motion detector may be a camera with some local processing logic. A gateway can simply coordinate the execution of this application and may perform some additional processing.

Once the motion is detected, the alarm is triggered, and it makes sense to set the alarm state as triggered to avoid redundant actions.

However, these two operations, alarm strobe that triggers the alarm and state alarm active, which sets the state of the alarm as activated, are two separate operations, and they involve separate variables or separate pieces of state. One writes some application level state regarding the state of the alarm, and the other one writes to some control register that will cause the alarm to rank.

### 8.2. Limitations of Conventional Transactions

For situations such as these in classical distributed systems, we would use transactions and redo or undo logs to ensure that we have consistent execution of these operations.

Using redo or undo log doesn't make sense when we're talking about activations of the physical environment.

As a result, it's possible that the application level state is updated by the second operation, but the first operation strobing the alarm somehow gets lost. The physical state of the application in that case will be inconsistent with the state of the application that's reflected by the application level state in the system.

And although we can conceive a way to program this particular intrusion detection application in a better way to avoid this particular situation, the paper gives a number of other examples which exhibit similar types of problems for which distributed transaction mechanisms as described in the earlier lessons just cannot help.

### 8.3. Three Types of Dependencies

The problems are fundamentally attributed to three cases of dependencies.

The first dependency concerns situations where an actuation action is dependent on the sensing variable. So here the actuation of the fans depends on the CO2 value. This means that the actuation should not be triggered if the sensed value or the read value does not satisfy this predicate.

The second dependency concerns dependencies among updates to the application state and the sensed value or value that is read.

And the third one concerns dependencies among the updates to the application level state and the actuation actions. This is the dependency we observed in the intrusion detection case where we needed to make sure that both the application level state and the actuation are either both of these performed or neither one of them takes place.

## 9. Transactuations

Since distributed transactions cannot help, the authors in this paper propose a new concept which is natively designed for the IoT Edge and it's called transactuations.

Transactuations are a high-level abstraction and a programming model. The transaction is specified by some application logic, sensing policy, and an actuation policy.

### 9.1. Sensing and Actuation Policies

The sensing policy can be expressed in terms of the hard values that are required for some of the sensors that are specified in a sensor list. It also can include a time window that specifies the time when these values of these sensors should be read and when the action should take place.

And it can also specify a policy that describes whether all the requirements should be met or just some of them or any one of them.

The actuation policy expresses the dependencies among the updates to the application state and the actuation of the physical devices. And this policy will specify whether they're allowed when any or some or none of the device actuations have succeeded.

### 9.2. Success, Failure, and Runtime Guarantees

The programming model also assumes that programmers specify the desired behavior of transaction success or failure. So this is essentially what has to happen on commit or abort.

The result is that it becomes possible with a programming model like this to describe the operations in the system and then to make some guarantees regarding the atomic durability of the actuations that take place in the environment.

Also, this type of system provides enough information so that one can schedule the different updates or can control actually when the different updates are performed and therefore avoid certain concurrency bugs.

### 9.3. Sensing and Actuation Invariants

The two concepts key in expressing transactuations and then building a runtime that will enforce them are the sensing and actuation invariants.

The sensing invariant specifies when transactuations can execute with respect to some property of the sensed values. So transactuations can execute only when the staleness of the sensors that are read is bounded and how it's bounded it will depend on the specified sensing policy.

In that sense, the sensing policy will describe how much staleness is acceptable. So what is the time window that's acceptable between the moment when the sensor was read and when an actuation or an update is to be performed. This policy can describe how many failed sensors are there allowed to be in the environment and so forth.

For instance, an example of this is a sensing policy that specifies that at least one of the CO2 sensors must have been read within the last five minutes and if so, then the transactuation can proceed and the update to the application level state or the actuations, those can take place.

It specifies that when a transactuation commits its application state updates, then it must be guaranteed that a sufficient percentage of the actuations have already succeeded as per the specified actuation policy. The actuation policy will specify what is that sufficient percentage, whether it's all or some or some other policy.

For instance, one example of this is that the actuation policy can specify that at least one alarm should be successfully turned on before the internal alarm state can be set to reflect that the alarm has been raised.

### 9.4. Execution and Commit

The resulting runtime then has sufficient information to insert checks at appropriate places for the different invariants and for the policies that it needs to enforce and to determine when to start or when to commit a transaction and how to ensure that a serializable ordering among concurrent transactions is enforced.

Specifically, a transaction execution will start when the sensing policy is satisfied, the ordering of the device actuations will be determined so as to avoid any rollbacks which are not going to be possible, and then the final commit will be performed based on the actuation policy and this is when any internal state that's dependent on the actuations will actually be updated.

## 10. Evaluation of Transactuations

The paper also evaluates whether transactuations are useful. It performs the evaluation with several different applications. They look to answer several concrete questions regarding the utility of transactuations.

What is the impact on the ability to program these kinds of use cases when using transactuations in a transactuation runtime? For this, they use lines of code as a measure which is a pretty common metric that's used when trying to evaluate some aspect of programmability.

Then they ask what is the impact on performance of a failure-free execution? So are all those checks that we are inserting at runtime introducing some overheads that are going to slow down the execution of these kinds of applications?

And then, of course, they want to verify that when there are failures with transactuations, the system can achieve correct behavior.

### 10.1. Generality and Application Coverage

Also, by picking these diverse types of IoT applications, the system in a way demonstrates that it can achieve generality that is sufficiently general.

The applications that I've chosen cover a number of different use cases that fall into different categories, convenience, energy efficiency, safety, security.

And so the claim is that because they're applicable to different types of applications, this transactuation concept is sufficiently general.

### 10.2. Implementation Comparisons

For each application, they start with the original implementation of the application, and then they modify it to ensure that consistency is added, but this is done in more of an ad hoc manner manually. And then they actually re-implement the same application using the transactuation programming system and runtime that they built.

### 10.3. Code Size Results

The results show that transactuations provide for more compact implementation of the desired consistency policies.

In fact, in some cases, two to three times fewer lines of codes are required than the corresponding implementations of the application plus all of the consistency requirements.

The code increase that's going to be observed when comparing the transactuation implementation to the original application is going to be very dependent on the kind of sensing policy and actuation policy that need to be enforced in order to meet the consistency guarantees.

### 10.4. Runtime Overheads and Correctness

Clearly supporting this transactuation concept is going to introduce some runtime overheads when compared to the original implementation of these applications. Remember, the original implementation of these applications did not necessarily include any of the necessary consistency checks. So in that sense, it can lead to some faulty behaviors.

In many of these cases, the overheads are actually quite modest. In some of the scenarios, clearly they're more significant. Again, this depends on the invariant checks that must be enforced.

But the 50% average overhead should be acceptable for the fact that the transactuation provide us with correctness guarantees.

The paper has much more results if you're interested.

## 11. Summary

In this lesson, we discuss new trends in distributed computing in terms of the emergence of new infrastructure tiers beyond just those of the mobile client devices and the remote cloud data centers.

These new trends sufficiently change some of the assumptions around which current distributed systems and concepts have been designed. This raises a need to rethink the current designs and abstractions.

In particular, we discuss some of the differences presented with the emergence of edge computing. And we also spend some time on a concrete example of transactuation as an example of how a familiar and established concept, distributed transactions, needs to be extended and modified in order to make it applicable to these new contexts, to these new infrastructure tiers.
