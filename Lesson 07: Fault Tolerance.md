# Lesson 7: Fault Tolerance

Source: [Lesson 7 — Video](https://www.youtube.com/watch?v=zr2cjdxJXy4)

## 1. Introduction

In this lesson, we will talk about different techniques for fault tolerance and recovery. We will present the discussion of the different techniques in the context of the failure models they assume, meaning the types of faults they can tolerate. And we will also describe and compare several basic recovery techniques.

## 2. Some Taxonomy

First, let's define some concepts. We will talk about failures in recovery, but a failure first starts with a fault. A fault may be related to some faulty hardware component, circuit, or memory location, or it may be a software bug. The system may have a fault, but function correctly as long as the fault isn't activated, accessed, or executed. Once it's activated, this fault leads to some error: incorrect behavior, incorrect information being produced, or something similar. This error propagates through the system as it executes, and it ultimately causes some sort of failure.

We already mentioned briefly in the introductory lesson that there are different types of failures. Let's look at what these are one more time.

Considering the faults in the system, they may be transient, meaning they manifest themselves only once and then they disappear. They may be intermittent, meaning they manifest themselves occasionally, or they may be permanent. A permanent fault, once activated, it will have its effect persist until the fault is removed, meaning the node or the software bug are fixed.

A fold can manifest itself in a field stop failure. In fail stop, one or more components of the distributed system stop working. They stop responding, and this is like a crash. A fault may lead to timing failures, which means that the affected system components behave outside of some timing expectations. This can lead to problems, for instance, if the implementation of the system relies on timeouts to decide whether it needs to re-transmit a message or to trigger some reconfiguration operation, for instance.

Omission faults are the ones where some actions are missing. For instance, a note can fail to send all messages as expected, or fail to receive all messages which were sent. And finally, the failures may be arbitrary. A it may continue to process and generate messages, but its behavior may be incorrect. And this may be either for malicious reasons or for just some arbitrary reasons.

### 2.1. Avoidance, Detection, and Recovery

Failures are clearly undesirable, so what is it that we can do about that? Ideally, we would like to avoid all failures. This means we need to detect them ahead of time, predict that they're about to occur. Avoidance tends to be too expensive. In a way, it relies on some ability to foresee all possible aspects of an execution, and particularly in a distributed system with many components, asynchronous messages, etc, this is not practical. So we have to accept that the occurrence of failures is inevitable.

Detection means that we have an ability to detect that a failure has occurred. Some common techniques include using some heartbeat mechanism to periodically check whether nodes are responsive. This can tell us whether a node is running or not, meaning it can allow us to detect a field stop failure, but not necessarily whether it's behaving correctly. For the later, we can rely on some form of error correction codes, checksums, or cryptographic methods.

If a failure is detected, ideally, we would like to remove its root cause and its effect. One way to do this is via rollback. This would take the system to the point in execution before the effects of the faults started manifesting themselves. If the failure was due to a transient fault, we may be lucky, and this can cause the system to continue execution correctly.

Rollbacks are not always possible. If the system had some external effects, for instance, caused a robot to move and perform some action in the physical world, in the external world, we cannot simply roll back and undo that.

Most generally, what we would like to achieve from a system is that even in the presence of failures, the system should be able to recover from them, by detecting that any failure has occurred, removing its effects, and resuming correct execution. Such systems are fault tolerant systems.

## 3. Rollback-Recovery Idea

The basic idea of rollback recovery-based fault tolerance is as follows: in the event some failure is detected, the system rolls back to a previous state which we know is correct, and then continues from that point to re-execute the operation with default removed correctly. By rollback here, we mean that the system will be in a state where any effects of the messages that had been exchanged from that point on are now removed. And similarly, if any of the notes are represented by their internal state, then that state has been restored to correspond to the state of the nodes at the point of time chosen during the rollback.

So the first question here is: which previous states does the system need to roll back to? Now, recall consistent cuts. The system needs to roll back to some previous consistent, which corresponds to the state of the system, ideally before the fault that caused the failure has occurred. As a consistent cut, this state corresponds to some legal state of the system. It cannot represent a scenario where, like in the right-hand side figure, node p2 thinks that it has received message m2 from p1, while at the same time, p1 has no record of ever sending such a message.

Two things are worth highlighting here. First, the state that the system rolls back to may not be an actual state that the system has ever been in during its previous execution. If you recall our discussion of consistent cuts, consistent cuts correspond to system state that the system may pass through during some execution, but not necessarily a state that the system has passed through during the execution that's being analyzed.

Second, you may wonder: how do we know how far back should this consistent cut be taken from? You can imagine how rollback recovery process can keep trying until it finds a cut far enough back, but can we have some better method to determine this? So the question is: how do we capture this state? We can try to find it by progressively rolling back the execution to earlier points, potentially all the way to the beginning. This would mean that potentially a lot of work can be lost. So instead, we rely on two basic mechanisms to do this. One is checkpoint based, and the second one is log-based. We've actually, in a way, already looked at log-based methods during our discussion of Raft and paxas. Here, we had to log all the messages, all the updates that were happening in the system, but we will come back briefly to this to explain how these are used during rollback and recovery.

### 3.1. Granularity and Application Interfaces

Before we continue, we should also mention that the granularity at which all of these systems for fault tolerance operate may differ. They may be completely transparent, meaning the solutions are implemented at the system level and do not require any modifications to the applications or any use of some special APIs.

In these transparent solutions, the rollback and recovery system needs to be concerned with each individual receive and send message, their success, their ordering, with each read access or update to a process state, and similarly, their success and ordering. This clearly can be an overkill for many settings. So often, to provide fault tolerance, systems explicitly expect that applications will be modified to use some special APIs. The standard way to do this is through use of transactional APIs, which group sets of related operations, as shown in this snippet. The system will then ensure that the transactions are performed atomically, meaning they either are successful or will be aborted and fully rolled back. If they're successful, meaning if they're committed, in that case, their effects will remain durable in the system. Note here that I'm oversimplifying this at this point, because the transaction itself may be distributed and it may correspond to a group of operations which are performed at more than one node.

Finally, there may be other application specific interfaces to rollback recovery. In the high performance computing domain, in the HPC domain, where we have huge applications executing across many thousands of nodes with massive amounts of state, using something like a transparent system level checkpoint will be a major overkill for performance. Fortunately, these applications typically know very well when and what they need to checkpoint in order to be able to recover, and they can pass this information to the underlying checkpointing recovery system, and so the checkpointing recovery system will precisely handle only that particular state.

In the rest of this lesson, we will talk about transparent methods which operate on individual read write, or message sent receive operations. But you can easily substitute whenever we refer to a term operation, and think about the term transaction, or think about an application initiated checkpoint, and then the rest of the discussion will apply to these other contexts as well.

## 4. Basic Mechanisms

Let's talk now about the basic mechanisms. A checkpoint corresponds to saving the state of the process or of the entire node to some persistent storage. When a checkpoint is performed, the state of the system or the application process should be captured from memory registers etc, and written out to disk or some other persistent media. If there is a failure, the checkpoint can be used to rebuild the state of the system at the corresponding point in the execution, and then to restart. In fact, the restart can be instantaneous as soon as the state is reloaded in memory and registers. Of course, if there is a hardware failure, the assumption here is that either this node would be restarted, repaired and restarted rather, or that another node would start, and that this other node has access to the same persistent media so as to access that checkpointed state.

The downside is that there is potentially a lot of IO that needs to be performed during each checkpoint in order to save the full system state. This is why application specific approaches are used in some context such as HPC, so as to include in the checkpoint only the state that's really necessary for recovery. Another way to reduce the amount of checkpoint IO is to keep track of the deltas across checkpoint intervals, and then to write out only the changed portions of the state.

### 4.1. Logging and Combining Mechanisms

An opposite approach from taking checkpoints of the entire system state is to just log information about the operations that have been performed that resulted in state changes. At a most basic level, this information should be just about the changes of the variables or memory locations from, say, x to x prime. More generally, the log can include information for some other higher level operations.

The information in the log may include the original values of the affected variable or variables. We call this the undo log, since we can use this information if we need to undo the changes during crawlback. Or the information can include the new values, in which case, to rollback means to go back to the original application state at the beginning, and then the log is used during recovery to replay the changes, to redo the changes.

Clearly, now the log has to be persisted and made durable, needs to be written out somewhere so it can be recovered in case the note fails. The benefit of this is that the log will be typically much smaller than the total system or application state, and therefore the amount of IO that needs to be performed during checkpoint is far less. Since this IO time is time that is taken in the application critical path, the application kind of has to write out this information while it's executing, it is important to keep this time to the minimum.

The downside is that now recovery takes longer. We need to look at this log, find out what are the updates that we need to undo or redo, replay the log, and so forth. In addition, with redo log in particular, even regular application operations become more expensive, because now they have to look through the log to find out the most recent value of any dependent parameters and input parameters they need for execution.

The two mechanisms can be combined so that each node in the system periodically performs a checkpoint, and then between checkpoints, it logs its updates. Sending a message is also considered an update to the system, changes the state of the channel of the system, so this is also an operation that needs to be locked. By combining the two mechanisms, if there is a failure, the system doesn't necessarily have to go back to the beginning. Instead, it needs to return to the most recent checkpoint that corresponds to a consistent cut.

This speeds up recovery, and also it's no longer necessary to keep very long locks. All data prior to some of these consistent checkpoints can be discarded. The downside is that the rollback recovery technique must incorporate a mechanism to detect a consistent cut from all of the individual node checkpoints.

## 5. Checkpointing Approaches

What are some different approaches to checkpointing? The explanation so far ignored one important question. In order to explain how the consistent cut can be determined, we need to first explain that there can be several ways to decide when a process takes a checkpoint: uncoordinated, coordinated, or communication induced. We will explain a few methods for rollback recovery based on these approaches, but first, let's briefly summarize the system model.

We will consider a model with a fixed number of processors. Communication among these processors is only going to happen through messages, and processes may interact with the outside world. We will assume that the network is non-partitionable, but the other assumptions will vary per protocol. For instance, some will require that the network is FIFO, or that reliable communication channel exists among the processes. In general, this can be achieved in a manner that's orthogonal to the rollback recovery protocol, for instance, by relying on an ordered reliable protocol such as TCP. In addition, the protocols that we will discuss will also vary based on the number of failures that they can tolerate.

## 6. Uncoordinated Checkpointing

The first approach is the uncoordinated approach. In this approach, processes take checkpoints independently. If there is a failure, it is important to recover the system in a consistent state. And for this execution here, it means p2, the note which has failed, will have to roll back to some consistent state. The nearest checkpoint is c. If it rolls back to the checkpoint seam, that means that the message m6, which is sent from p2 to p1 after the checkpoint, is lost. So we have to remove any of its effects from the system. That means that on p1, we now have to roll back to checkpoint b.

Fortunately, there are no other effects that need to be rolled back. P1 has not sent anything after b, so we don't have to eliminate the effects of any messages that may have been sent after the checkpoint b. And if we take a look at the most recent checkpoint in p0, that's checkpoint a, there are no messages that it has sent that need to be undone. All of the messages that were sent prior to any of the per node checkpoints a b and c, are reflected as received in the corresponding checkpoints. So in this scenario, the recovery line will correspond to these states a b and c.

Clearly, in order to be able to do this, the solution will have to rely on maintaining some information about the dependencies that exist, about the messages that have been sent and received in the system.

### 6.1. The Domino Effect

Uncoordinated checkpoints suffers from a very serious downside, and that's what's called domino effect. Consider this slightly modified execution. And now, suppose process p2 fails and rolls back to checkpoint c. Now, the rollback invalidates the sending of the message m6, and so p1 must now roll back to checkpoint b. Now, when we roll back p1 to checkpoint beam, we eliminate the effects of message m6. However, we now have to also eliminate the effects of message m7. Now, we need to roll back process p0 to checkpoint a. We see that process 0 has sent message m5 after the checkpoint, so we have to eliminate any effects in the system that correspond to the receipt of message m5. So we look now at process p2. Checkpoint c is not adequate. We have to roll back to the previous checkpoint. Well, now we have to eliminate any messages that p2 has sent after that previous checkpoint exist in the system. In this case, that's message m4. Clearly, have to roll back p1 to a checkpoint before the checkpoint beam.

And so you can see how this will have to continue, and one by one, the rollback will go back to find as a recovery line, a point in the execution that corresponds really to the very initial state of the system. All of the work that has been done by any of the processes in the system will be completely wasted.

Other than the domino effect, as a result of which the application could lose work, there are other issues with uncoordinated checkpointing. For instance, you could see in the previous illustration how checkpoint after checkpoint were discarded, were not used. It is quite possible, in fact, to have many useless checkpoints that will never become part of a globally consistent state, and that's also just extra work that that was wasteful.

Also, since we don't know how far back we'll have to roll back to reach a consistent recovery line, each process must maintain multiple checkpoints, but not just the most recent snapshot. This is going to increase the per process storage requirements for each node, and this is particularly bad since not all of these checkpoints are really useful.

In order to deal with these potentially excessive storage requirements, we'll have to run garbage collection algorithms periodically to identify obsolete checkpoints. And these garbage collection algorithms are not going to be simple, and they're going to add to the overhead of the system.

## 7. Coordinated Checkpointing

Another approach is to do what's called coordinated checkpoint. In a coordinated checkpoint, the processes coordinate when they take a checkpoint so as to ensure that the checkpoints they take are part of a consistent state. For instance, here, p0 initiates a checkpoint and coordinates with the two other processes to make sure that they take their appropriate checkpoint. In such a scenario, a recovery no longer requires maintaining a dependency graph to calculate a recovery line. The latest checkpoint that each of the processes can simply be used.

The benefit of this is that in coordinated checkpoint, then there is no danger of having a domino effect where we have to roll back the entire computation all the way to the beginning as what we described for uncoordinated checkpoint. Basically, the coordination that we perform when taking the checkpoint ensures that when a checkpoint is taken, it is a relevant checkpoint that's part of a consistent cut. It's obvious, therefore, to see that there really isn't a need at this point to keep multiple checkpoints per process. We can just keep one checkpoint per process, the most recent one, and the earlier ones can be discarded. And this makes garbage collection trivial.

### 7.1. Coordination Challenges

But there are challenges with coordinated checkpoint, and the biggest one is how to ensure this coordination among processes that they need to perform in order to agree when to take the checkpoint.

For instance, the initiator message to p2 may have been delayed and to receive after it has received this message mi. In this case, if we take a look at the state that's described with the cut abc, we see that this state corresponds to a situation where the system knows that a message has been received on p2, this is message mi, but it does not know that that message has been sent, because in process p1, the checkpoint b corresponds to state before the message mi has been sent. So then clearly, this checkpoint abc does not correspond to a consistent cut in the system, and we have to prevent this situation.

If we had a guarantee of synchronous clocks, it would be possible to do this. We can just have the nodes agreed that they will take a snapshot after every t units of time. However, time is not guaranteed to be uniform across all nodes, and there is no guarantee that there isn't a drift among the cloaks. So this presents a problem. If we had reliable and bounded message delivery, then there are ways to come up with a coordination strategy, but in general, this is not the case.

Also, by having the initiator send a message that says take a checkpoint now to all the notes in the system, this type of coordination may lead to many processes having to create a checkpoint even when there are no relevant changes in their state. So the problem of having the system take unnecessary checkpoints is not completely eliminated in this scenario.

## 8. Communication-Induced Checkpoints

To deal with this, we rely on so-called communication-induced checkpoints. One way to ensure the nodes properly coordinate is to use a coordination protocol such as two-phase commit, or even any other consensus protocol. Here, the consensus the nodes are trying to reach is that they are taking a snapshot at that particular point of time. The key here is that from the moment the coordination is initiated until it completes, no other messages should be processed. And in that sense, this approach is blocking.

Instead, we already looked at a non-blocking alternative that would be a good match for capturing a consistent. That was the global snapshot algorithm. You recall that the global snapshot algorithm relied on the special marker messages. This worked, but imposed this requirement that the network had to be fifa. To remove the question of how the marker is ordered with respect to any of the application level messages, one way to do this is you can simply piggyback the marker message in a message.

Also, to make sure that we have recent checkpoints of nodes that may not be presently communicating with anyone, periodic independent checkpoints are encouraged. In between such independent checkpoints, each note monitors the incoming messages, and if they contain a marker, then a checkpoint is performed. And in fact, the snapshot of the process date is captured just before processing the message that carried the piggybacked information about taking a checkpoint. By doing that, this scenario where we had a problem in the earlier example can be solved. The checkpoint of p1 is taken before the message is sent. The checkpoint c of p2 is also taken before the message is formally received by the process. And if there is a failure in the system, the two checkpoints will represent a consistent cut, and will form a valid recovery line.

## 9. Logging

The other basic mechanism we rely upon in order to implement recovery mechanisms is logging. Unlike checkpoint reset, logging offers opportunity to save on the amount of ion that needs to be performed, but requires more complex recovery, because to rebuild the state of the system, the log needs to be used to first roll back and then to re-execute the execution.

When considering distributed systems, the logs that are created by each of the nodes in the system must be such so that they specify deterministically a valid execution of the system, and they don't lead to orphaned events. For instance, in an example that's equivalent to the inconsistent cut illustration we used earlier, we cannot have a situation where upon failure we have a note p2 information that the message has been received from p1, but the log on p2 shows no record of a message being sent.

There are several approaches to achieve this. In pessimistic logging, the idea is that each process should log everything to persistent storage before allowing events to propagate and to get committed in the system. If pessimistic logging is used in the scenario illustrated in this illustration, it would be impossible to execute the sent message event at p1 and not to log it. Therefore, the problem would be solved.

The issue with pessimistic logging is that it incurs very high overhead. Writing to persistent storage is slow. It introduces a slowdown, and the slowdown is in the critical path of executing operations and finalizing and committing them. There are ways to improve this. Newly available persistent memories offer a way to write to persistent storage much much faster, and they can certainly help. And there are many examples out there where persistent memories are used precisely for maintaining logs that are later used for building fault tolerance solutions.

### 9.1. Optimistic Logging and Causality

Another approach is optimistic logging. In optimistic logging, there is an assumption that the log will be persisted before a failure occurs, and it also makes it possible to remove the effects of an operation in case it needs to be aborted.

Now, we recognize that it may be hard to generally claim that this assumption that the log can be persisted always before a failure occurs, that this assumption can generally be true. For this reason, these solutions that rely on optimistic logging must introduce some additional mechanism to track dependencies, so in the event of a crash, they can restore the system to correct state. What this requires is that they need to correctly identify any incompleted operations, and then remove their effects.

For any operations that lead to some externally visible events that cannot simply be undone, the output of such operations has to be delayed until the system is sure that the operation has, information about the operation has been persisted, and that these operations will not need to be imported.

In principle, what we need is some causality tracking mechanism. And approaches that are based on causality tracking can operate optimistically whenever there are no dependence related problems, but guarantee the similar properties of not having orphaned events, which is possible to guarantee with pessimistic law.

There is still an issue here that any externally visible output would need to be delayed, potentially unknown amount of time, particularly when the system operates with slow and unreliable networks. The reason for this is that causality tracking relies on exchange of some messages, and so we have to ultimately guarantee that there aren't going to be any messages carrying causality related information that are still in place.

## 10. Which Method to Use?

To summarize, there are a number of options that one can consider when implementing fault tolerance solutions for distributed systems that rely on rollback recovery. At a basic level, these solutions incorporate checkpointing and logging mechanism. But even in the context of checkpoint and logging, a number of variations exist.

The right choice would depend on a number of factors. These can be related to the workload: how often is the data updated? How big are the updates? How many nodes are involved in a single application level transaction? Is achieving fast recovery important? When thinking about this, clearly, we have to think also about the failure characteristics: what kinds of failures are occurring in the system, and what are their probabilities? Then we have to think about the characteristics of the system as well, particularly with respect to the cost and overhead of communication versus storage.

These last questions about the cost and overheads of communication versus storage tend to change over time. Today, in data centers, we can have ultra fast and very reliable networks, but also the overheads associated with writing to persistent storage are changing given the availability of new classes of persistent memory.

The paper includes a nice table showing a comparison of the different techniques. For instance, pessimistic logging shows up favorably here with respect to the different features compared in the table. We said that it may be undesirable because it requires immediate update to persistent storage. But if the cost of writing to storage becomes very cheap, then pessimistic logging is a viable candidate given the simplicity of all of the different aspects.

Coordinated checkpointing, which has been the default strategy in HPC systems, is favorable with respect to most of these features, but it delays committing the operation until it can ensure that it has been safely checkpointed. This requires global system-wide coordination, and for many HPC applications, this is given, given that the application itself has iterative nature with a lot of global communication among the processes. But when these systems become larger and larger, this type of option will not be practical anymore. In fact, the cost of performing the checkpoint and waiting for this global coordination may end up dominating a huge percentage of the execution of the application.

## 11. Summary

And now, to summarize the lesson, in this lesson, we discuss the problems related to dealing with failures in distributed systems, and we said that for distributed computations to be able to recover from failures, it is necessary for them to maintain information about their state and any changes that have been performed on that state in a consistent manner. We said that there are two basic mechanisms that underpin general rollback recovery techniques: checkpointing and logging. We briefly described several design approaches to implementing a checkpoint or a logging based recovery system. Note that we use the terms operations and update interchangeably to refer to some unit of work in the system. In principle, this can correspond to an individual update to a variable, or it can correspond to an entire distributed transaction.
