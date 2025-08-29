---
layout: post
title: Learnings from MIT 6.824
date: 2025-08-27 20:54 -0700
description: MIT 6.824 lab review
---

Over the past few months, I’ve been intermittently working on the lab assignments for MIT 6.824. I had anticipated a lengthy debugging process, but to my surprise, the reality was quite different. I spent a total of 10-20 hours debugging all the labs, a remarkably smooth experience. The bulk of my time was dedicated to contemplating the myriad edge cases that arise during implementation, each demanding meticulous handling of event sequences. I found myself poring over the Raft paper on multiple occasions. Its dense, precise language is laden with profound insights that required my full attention to implement correctly. Personally, I consider 6.824 one of the best coding projects for diving deep into distributed systems (particularly the Lab 5 challenges). In this post, I’d like to share some of the heuristics I developed along the way.

## About Debugging

My debugging habit might be a bit unconventional, but I find it more efficient than conventional methods. I don't use print statements during my initial implementation; instead, I just run the tests. Usually, some of them will fail, and then I use the VS Code Go debugger to rerun the specific test. If a test gets stuck, it's typically due to a deadlock. I then pause the debugger and examine each goroutine, which usually allows me to quickly locate the blocked routine in my code. If there's no deadlock, I can start reasoning from that point and add logging if needed. This approach usually saves me time and helps me avoid distracting and redundant logging.

Rather than logging, I prefer adding assertions to test invariants during execution, which helps detect logical flaws promptly.

Sometimes, deadlocks are difficult to reproduce. In extreme cases, I've encountered situations where a single test run consistently passes, yet running `go test -race` 10 times in parallel triggers a deadlock with a 10% probability (where my computer was strained). To debug this, I typically wait for the test to time out and then inspect the stack trace to identify which lines of code have been blocked the longest.

---

## Lab 1: MapReduce

In this first lab, we implement the MapReduce framework from the ground up. It is challenging yet manageable and provides a precious opportunity to dive deep into how hundreds of thousands of machines can be connected and coordinated for a distributed workload.

This lab is so self-contained that the implemented MapReduce framework could even be leveraged in side projects, such as web crawling.

## Lab 2: Key/Value Server

Lab 2 is quite simple, so I won't elaborate on it further. The core lesson from this lab is achieving request idempotency.

## Lab 3: Raft

This brings us to the Raft implementation—the heart of the course. While I had tackled the Raft algorithm once before in a graduate course, my memory of it has faded. I discovered that this project was more demanding than my previous encounter with it, as it includes additional requirements such as log compaction.

### Handling Staleness

From my perspective, one of the most critical mindsets to adopt during implementation is handling stale responses across all three states: Follower, Candidate, and Leader. Stale requests can arise from out-of-order network delivery, server state changes, or other field modifications. To stimulate further thought, I've outlined a few scenarios below:

* **Follower**
  * **AppendEntries RPC:**
    * What if an `AppendEntries` request is delivered out of order and is therefore stale? Will it truncate the correct logs?
* **Candidate**
  * **RequestVote RPC:**
    * Could a `RequestVote` response be stale? For instance, what if a candidate is elected twice due to a timeout, but the response is from the first election attempt?
* **Leader**
  * **AppendEntries RPC:**
    * If the RPC fails, should it be resent immediately?
    * If the response indicates a failure, should a new `AppendEntries` request be sent immediately?
    * How can you determine if an `AppendEntries` response is stale? This is crucial, as the `nextIndex` update depends on it.
  * **Heartbeat RPC:** This is typically a periodic goroutine, but could it also become stale? For example, it's possible that during the interval, a leader could be converted to a follower, then to a candidate, and then back to a leader. In such a case, you must avoid mistakenly spawning two periodic heartbeat goroutines.

### Fast Backup Algorithm

As the lab documentation suggests, we are required to implement a fast backup mechanism. However, in practice, we don't need the three additional fields in the RPC response that the documentation suggests; two are sufficient.

### Commit Index Update

The commit index update process is quite intriguing. Theoretically, the most efficient approach would be to maintain an indexed min-heap and an indexed max-heap, which would allow for finding the median match index in near-constant time. However, this is likely overkill. Given that the server cluster size is typically small and constant, such a complex implementation is unnecessary. A plain sort is probably the best approach, as it strikes a good balance between simplicity and efficiency. As a side note, I implemented an [indexed heap](https://github.com/yutao-li/libheap) in my leisure time.

### Improving Read Consistency with No-Op Entries

While the project requirements build a robust distributed system, a subtle enhancement can further improve read consistency. When a server becomes a leader, it can immediately append a "no-op" entry to its log. By replicating and committing this entry from its current term, the leader can also safely commit any preceding entries from its log that might have been left uncommitted from previous terms. This ensures that the system can immediately serve the most up-to-date data for read requests.

### Log Compaction

I completed the 2024 version of the lab, and in my opinion, the documentation was somewhat opaque. It was necessary to examine the test code to understand how the `Snapshot` function is called, how snapshots are generated, and to support the specific parsing logic required; otherwise, some test cases would likely fail. Essentially, it's not necessary to re-compress snapshot data during persistence.

## Lab 4: Fault-Tolerant Key/Value Service

Lab 4 is a combination of Lab 2 and Lab 3, designed to attain both idempotency and fault tolerance. This lab provides an opportunity to build a real-world service on top of Raft. The logic in this lab is less complex than in the previous one, but the tests have stricter performance requirements. To achieve a full score, it's crucial to focus on optimizing the logic, particularly for handling stale and duplicate requests.

Another point of initial misunderstanding for me was that the server ID in Raft does not map to the index in the client's server list. Consequently, tracking the leader ID within the Raft layer is futile and can be detrimental if misused. I initially attempted to propagate the leader ID from the Raft layer to the client, only to find that the client consistently contacted the wrong server. After delving into the test code, I discovered that each client's server list is shuffled, which explained why my approach was ineffective.

## Lab 5: Sharded Key/Value Service

Finally, our distributed system lab journey culminates in Lab 5! In this lab, we get the chance to coordinate multiple RAFT systems and build a sharded system, which is the typical case for a large-scale key/value service distributed system. Personally, I consider this lab the most challenging of all, but also the most rewarding.

In Part A, the documentation states that the difficulty is easy, which sharply contrasts with my personal experience. The shard rebalancing part can be made highly sophisticated using complex data structures, but since this is not an algorithms and data structures course, I chose not to dive too deep into it. In the end, I used a for-loop to achieve the goal.

In the non-credit challenges, as shown from the lab documentation:

> Client requests during configuration changes  
> The simplest way to handle configuration changes is to disallow all client operations until the transition has completed. While conceptually simple, this approach is not feasible in production-level systems; it results in long pauses for all clients whenever machines are brought in or taken out. It would be better to continue serving shards that are not affected by the ongoing configuration change.
>
> Modify your solution so that client operations for keys in unaffected shards continue to execute during a configuration change. You have completed this challenge when you pass TestChallenge2Unaffected.
>
> While the optimization above is good, we can still do better. Say that some replica group G3, when transitioning to C, needs shard S1 from G1, and shard S2 from G2. We really want G3 to immediately start serving a shard once it has received the necessary state, even if it is still waiting for some other shards. For example, if G1 is down, G3 should still start serving requests for S2 once it receives the appropriate data from G2, despite the transition to C not yet having completed.
>
> Modify your solution so that replica groups start serving shards the moment they are able to, even if a configuration is still ongoing. You have completed this challenge when you pass TestChallenge2Partial.

We get directly entangled with the intricacies of lock granularity design. It requires us to think thoroughly about the configuration update procedure and carefully divide it into multiple atomic (async) steps. A simple and clear division of each step's responsibility is crucial for reasoning about linearizability. There are multiple design tradeoffs to consider in depth, as different decisions can lead to drastically different levels of complexity.
To avoid giving too much away, I'll just offer a few hints here:

1. Consider pull vs. push on shard transfer; one way is much harder than the other.
2. Does the config version update really have to be blocked by stale shard/keyValueMap deletion?
3. Does the config version update really have to be blocked by shard transfer? Consider the complexity of different options when handling stale requests.
4. Idempotency consideration of config updates and shard moves is important.
5. On config version polling, should we poll the next config version or the latest version? If we poll the next version, what happens if a (new) replica group joins/rejoins, where the current version is 10000?
6. It's OK to allow a short interval where no servers handle a shard that is being moved.

Lastly, try not to hold any lock while waiting for RPC reponse, since it's huge waste of time.

## Side Note

To get the most out of this well-crafted course project, I highly recommend taking a look at how `labrpc.go` is implemented. It's the core infrastructure that all of our tests are based on. In particular, focus on the `processReq` and `Call` functions. There you will see how arbitrary RPC calls are supported by a sophisticated usage of Go reflections. If you understand these two functions, you will understand how a network RPC library is implemented.
