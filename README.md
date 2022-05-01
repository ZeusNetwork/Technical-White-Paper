# Technical-White-Paper
Zeus network technology principle and underlying architecture guide

#𝗪𝗵𝗮𝘁 𝗶𝘀 𝘁𝗵𝗲 𝗭𝗲𝘂𝘀 𝗡𝗲𝘁𝘄𝗼𝗿𝗸❓
A blockchain is an implementation of a fault-tolerant replicated state machine. Currently publicly available blockchains do not rely on time, or make assumptions about the ability of participants to maintain time. At the same time each node in the blockchain network usually relies on their own local clock without knowing the clocks of other participants in any network. The lack of a reliable source of time means that when message timestamps are used to accept or reject messages,
![image](https://user-images.githubusercontent.com/104710488/166135854-4a3304c1-90d4-47a4-b457-e07983f48ee4.png)

There is no guarantee that every other participant in the network will choose exactly the same. The PoH presented here aims to create a ledger with a verifiable elapse of time, i.e. the duration between events and messages
order. It is expected that every node in the network will be able to rely on the elapse of time recorded in the ledger without trust.

𝟭 𝗜𝗻𝘁𝗿𝗼𝗱𝘂𝗰𝘁𝗶𝗼𝗻
A blockchain is an implementation of a fault-tolerant replicated state machine. Currently publicly available blockchains do not rely on time, or make assumptions about the ability of participants to maintain time. At the same time each node in the blockchain network usually relies on their own local clock without knowing the clocks of other participants in any network. The lack of a reliable source of time means that when message timestamps are used to accept or reject messages,

There is no guarantee that every other participant in the network will choose exactly the same. The PoH presented here aims to create a ledger with a verifiable elapse of time, i.e. the duration between events and messages
order. It is expected that every node in the network will be able to rely on the elapse of time recorded in the ledger without trust.

𝟮 𝗢𝘂𝘁𝗹𝗶𝗻𝗲
The rest of the Zeus network is organized as follows.
The overall system is designed as
Described in Section 3..........................Describes an in-depth description of historical proofs
Described in Section 4 ................................. An in-depth description of the proposed Proof-of-Stake consensus
Described in Section 5..........................​​ In-depth description of the assumptions made by the algorithm
Described in Section 6.................................................​​ System Architecture and Fast Proof of Replication
Described in Section 7................................Performance constraints on high-performance GPUs

𝟯 𝗡𝗲𝘁𝘄𝗼𝗿𝗸 𝗗𝗲𝘀𝗶𝗴𝗻
As shown in Figure 1, at any given time, system nodes are designated as
The leader generates the historical proof sequence, providing the network global
Read consistency and verifiable elapse of time. leader sequence user
messages and sort them so that others can efficiently process Zeus Networks
Nodes in the system to maximize throughput. Zeus Network executes transactions
Store in RAM and publish the current state of the transaction
And a signature on the final state of the replicating nodes called validators.
Validators execute the same transaction on their state copy and publish their computed state signature as confirmation. Published
Confirm the vote as a consensus algorithm.
![image](https://user-images.githubusercontent.com/104710488/166135937-ab3b62aa-25d1-4e43-bd6d-444a7415025b.png)

In a non-partitioned state, at any given time, there will be a leader
The internet. Each Verifier node has the same hardware capabilities as the Leader
And can be elected as a leader, which is done through PoS-based elections.


According to the CAP theorem, consistency almost always takes precedence over availability when a partition occurs. In the case of large partitions, the Zeus network proposes a mechanism to restore control of the network from one partition
any size. This is covered in detail in Section 5.12.

historical proof
A historical proof is a series of calculations that can provide a way
Cryptographically verify the elapse of time between two events. Zeus Network uses a cryptographically secure function written so the output cannot be predicted
Extracted from input and must be fully executed to generate output.
The function runs sequentially on a single core, and its previous output is a periodic record. The output can then be recomputed and verified externally, and the Zeus network parallelizes the computer by examining each sequence segment on a separate core.
The state of the function can be entered by appending data (or a hash of some data).
State records: indexes and data appended to the sequence provide timestamps
This guarantees that the data was created sometime before the next hash, in the sequence. This design of the Zeus network also supports horizontal scaling, as multiple generators can synchronize their states into each other's sequence by mixing the Zeus networks.
The Zeus network is designed to work as follows. With a cryptographic hash function, whose output cannot be predicted without running the function (eg sha256, ripemd, etc.), run the function from some random starting value and take its output and pass it as input to the same function again.
Zeus Network will record the number of times the function is called and output each call. The starting random value chosen can be any string, for example: the top stories of the day in the New York Times.
For example: PoH sequence
index operation output hash
1 sha256 ("any random starting value") hash1
2 sha256(hash1) hash2
3 sha256(hash2) hash3
where hashN represents the actual hash output.
E.g:
PoH sequence
index operation output hash
1 sha256 ("any random starting value") hash1
200 sha256(hash199) hash200
300 sha256(hash299) hash300
As long as the chosen hash function is collision-resistant, the set of hashes can only be computed sequentially by a single computer thread. This is as follows
From the fact that there is no way to predict the hash value at the index
300 will not actually run the algorithm from the start. So we can infer from the data structure the real
Elapsed time between index 0 and index 300.
In the example in Figure 2, the hash 62f51643c1 is produced when counting
510144806912 and hash c43d862d88 are produced on count 510146904064.
According to the properties of the PoH algorithm discussed earlier, we can
Believe that real time passes between count 510144806912 and count 510146904064
![image](https://user-images.githubusercontent.com/104710488/166135961-3b29d503-b843-49e9-a828-c3a1b986e3dd.png)
event timestamp
This hash sequence can also be used to record some data
Created before generating a specific hash index. Use "combine"
The exponent of the function that combines the data block with the current hash value. The data can simply be arbitrary cryptographically unique hash event data. The combine function can be a simple data append, or any anti-collision operation. The next generated hash represents the timestamp of a piece of data, since Zeus Network can only generate that particular piece of data after that.
E.g:
PoH sequence
index operation output hash
1 sha256 ("any random starting value") hash1
200 sha256(hash199) hash200
300 sha256(hash299) hash300
Some external event happened, or any arbitrary creation of numeric data:
PoH sequence with data
index operation output hash
1 sha256 ("any random starting value") hash1
200 sha256(hash199) hash200
300 sha256(hash299) hash300
Recorded as part of the sequence output. So anyone can verify this sequence and can then recreate changes to the sequence. Validation can still be done because the initial process is still sequential, so we can know things, the input sequence must have happened sometime before in the future.
Calculate the hash value.
POH sequence
index operation output hash
1 sha256 ("any random starting value") hash1
200 sha256(hash199) hash200
300 sha256(hash299) hash300
400 sha256(hash399) hash400
500 sha256(hash499) hash500
600 sha256 (append(hash599, photo2 sha256)) hash600
700 sha256(hash699) hash700
Table 1: PoH sequence with 2 events
In the sequence represented in Table 1, photo2 was created before
hash600, photo1 was created before hash336. insert data
All subsequent values ​​in the hash sequence change
order. as long as the hash function used is collision resistant, and
With data attached, it should be computationally impossible to precompute
Any future sequence based on prior knowledge of which data to integrate into the sequence.
The data mixed into the sequence can be the original data itself, or
Just a hash of the data with metadata.
In the example in Figure 3, the input cfd40df8... is inserted into the historical proof sequence. The count of inserts is 510145855488
The state inserted into the Zeus network is 3d039eef3. All future generated hashes are modified by a change in this sequence, which is represented by a color change in the graph.
Observing each node of this sequence can determine the order of all nodes
Events have been inserted and estimated real time between insertions.
![image](https://user-images.githubusercontent.com/104710488/166135978-8c377a68-cc73-42b6-a97c-f128077f3d03.png)

verify
This sequence can be significantly verified to be correct by a multi-core computer
It takes less time than generating the Zeus network.
E.g:
core 1
index data output hash
200 sha256(hash199) hash200
300 sha256(hash299) hash300
core 2
index data output hash
300 sha256(hash299) hash300
400 sha256(hash399) hash400
Given a certain number of cores, such as a modern GPU with 4000 cores,
Validators can divide the hash sequence and its index into 4000 slices,
and at the same time make sure that each slice hashes from the starting to
The last hash in the slice. If the expected time to produce the sequence is
Will:
8
Figure 4: Using multi-core authentication
![image](https://user-images.githubusercontent.com/104710488/166135998-b857e0ad-cb58-4202-ac63-e52404105a3c.png)

total number of hashes
The expected time for 1 core per second to verify that the sequence is correct would be:
total number of hashes
(Hashes per second per core * verifiable cores)
In the example in Figure 4, each core is able to verify parallel order. Since all input strings are recorded to the output, the counters and states that Zeus networks are attached to, validators can replicate each slice in parallel. The red hash indicates that the sequence was modified by data insertion.
 Horizontal zoom
Zeus Network can synchronize multiple proof-of-history generators by mixing
sequential state from each generator to other generators, thus achieving
Horizontal scaling of historical proof generators. This zoom is done
There are no shards. The outputs of both generators are required for reconstruction
The complete sequence of events in the system.
PoH Generator A
index hash data
1 hash 1a
2 Hash 2a Hash 1b
3 Hash 3a
4 Hash 4a
PoH Generator B
index hash data
1 hash 1b
2 Hash 2b Hash 1a
3 Hash 3b
4 Hash 4b
Given generators A and B, A receives packets from B (hash1b),
which contains the last state from generator B, and the last state generator
B is observed from generator A. The next state hash in generator A depends on the state of generator B, so we can deduce that hash1b happened sometime before hash3a. This property is transitive, so if three generators are synchronized via a single common generator A ↔ B ↔ C, we can track the dependencies between A and C, even if the Zeus network is not directly synchronized. By periodically synchronizing the generators, each generator can handle a portion of the external traffic, so the entire system can handle tracking a large number of events at the expense of real-time accuracy without network delays between generators. A global order can still be achieved by choosing some deterministic function to order any event synchronization window within it, such as the hash itself.
In Figure 5, two generators interpolate each other's output states and record operations. A color change indicates that data from the peer has modified the sequence. Generated hashes mixed into each stream, sync is transitive. A ↔ B ↔ C has a provable order
A and events between C and B.
Scaling in this way comes at the cost of availability. A 10 × 1 gbps connection with an availability of 0.999 will have an availability of 0.99910 = 0.99.
 consistency
The user is expected to be able to enforce the consistency of the generated sequence and make it resistant to attacks by interpolating the last observed output
A sequence that they think is valid for the input.
![image](https://user-images.githubusercontent.com/104710488/166136011-7fb4b86e-4014-446a-b444-fe7b67bf9831.png)

Figure 5: Synchronization of two generators
PoH Sequence A
index data output hash
10 Hash 10a
20 events 1 hash20a
30 event 2 hash30a
40 event 3 hash 40a
PoH hidden sequence B
index data output hash
10 hash 10b
20 event 3 hash20b
30 event 2 hash 30b
40 event 1 hash 40b
A malicious PoH generator can generate a second hidden sequence
Events are in reverse order, if the Zeus network can access all events at once, or be able to generate faster hidden sequences.
To prevent this kind of attack, every client-generated event should contain in itself the latest hash that the client has observed from what the Zeus network thinks it is
So when clients create "Event1" data, they should
Append the last hash they observed.

PoH Sequence A
index data output hash
10 Hash 10a
20 event 1 = append(event 1 data, hash 10a) hash 20a
30 event 2 = append(event 2 data, hash20a) hash30a
40 event 3 = append(event 3 data, hash30a) hash40a
When the sequence is published, Event3 will refer to hash30a,
If the Zeus network is not in the sequence prior to this event, then
The sequence knows that the Zeus network is an invalid sequence. then partial reordering will attack
will then limit the number of hashes produced while the client has it
An event is observed and when it is entered. then the client should be able to write correct software that does not assume ordering pairs are short
The hash between the last observed hash and the inserted hash.
To prevent malicious PoH generators from rewriting the client event hash, the client can submit the signature and last
The observed hash, not just the data.
PoH Sequence A
index data output hash
10 Hash 10a
20 event 1 = symbol(append(event 1 data, hash 10a),
Client private key) hash20a
30 event 2 = symbol(append(event 2 data, hash20a),
Client private key) hash30a
40 event3 = symbol(append(event3data, hash30a),
Client private key) hash40a
Verifying this data requires signature verification and lookup
The hash in the sequence of hashes preceding this one.
Verify:
(signature, PublicKey, hash30a, event3 data) = Event3
Verify (signature, public key, event 3)
find(hash30a, PoHSequence)
In Figure 6, the user-supplied input relies on the hash 0xdeadbeef...
was present in the generated sequence sometime before insertion. blue part
![image](https://user-images.githubusercontent.com/104710488/166136021-622e9afd-22f2-4ac0-a99c-b90e048183e2.png)

Figure 6: Input with backreferences.
The upper left arrow indicates that the client is referencing a previously generated hash. Client messages are only valid in sequences containing hashes
0xdeadbeef.... red in the sequence indicates that sequence
Modified by customer data.
data
4000 hashes per second yields an additional 160 KB of data,
and requires access to a GPU with 4000 cores and around 0.25-0.75
milliseconds to verify.
 reverse
Generating the reverse order would require an attacker to initiate malicious
The sequence after the second event. This delay should allow any non-malicious
Peer-to-peer nodes communicate about the original order.

 speed
Having multiple generators may make the deployment more resistant to attacks.
One generator can be high bandwidth and receive many events to be mixed
Going into its sequence, another generator could be a high-speed low-bandwidth, periodically mixed with a high-bandwidth generator.
The high-speed sequence will create a secondary data sequence that the attacker will have to reverse.

ranged attack
Remote attacks involve obtaining old discarded client private keys, as well as generating fake ledgers [10]. Historically proven to provide some protection against long-range attacks. A malicious user gaining access to the old private key would have to recreate a history that took the same amount of time as the original they were trying to forge. This will require access to a faster
The processor is larger than what the network is currently using, otherwise the attacker will never catch up with the history length.
Additionally, a single time source allows for simpler construction of
Copy proof. Due to the design of the network, all participants in the network will rely on a single historical event record.
So PoRep and PoH should provide space and time defense together

For counterfeit ledgers.
5 Proof of Stake Consensus
5.1 Description
This particular proof-of-stake instance is designed for quick confirmation
The current sequence generated by the proof-of-history generator for voting
And choose the next proof-of-history generator, and punish any misbehaving validators. The algorithm depends on the message that finally arrives
Sent to all participating nodes within a certain timeout period.

5.2 Terminology
Bonds: Bonds are equivalent to capital expenditures in proof of work. a miner
Buy hardware and electricity and submit it to a branch
Proof-of-work blockchain. Bonds are coins submitted by validators
as collateral when they validate transactions.
Suggested solutions to reduce risk-free problems in Proof
Stake System [7].
When a proof of vote for a different branch is published, that branch can destroy the validator bond. This is a fork of economic incentives designed to prevent validators from confirming.





5.3 Bonding
A bound transaction takes a certain amount of coins and moves them to an account under the bound user identity. The coins in the bound account cannot be
Spend and must remain in the account until the user deletes it. This allows users to only delete stale coins that have timed out. The overwhelming majority of current stakeholders have confirmed this sequence.
5.4 Voting
It is expected that the proof-of-history generator will be able to publish
The signature of the state at the predetermined period. Each bound identity must confirm that signature by publishing its own country signature.
Voting is a simple yes vote, no negative votes.
If the overwhelming majority of bound identities vote within the timeout,
then this branch will be accepted as valid.

5.5 Degumming
Missing N votes marks the coin as stale and no longer eligible for voting.
Users can issue an unbinding transaction to remove the Zeus network.
N is a dynamic value based on the ratio of stale votes to active votes. n
Increases with the number of outdated votes. In the event of a large network partition, this allows larger branches to recover faster than
smaller branches.
5.6 Elections
When a PoH generator fails, a new PoH generator is chosen
The validator with the highest voting power or highest public key
If there is a tie, the address is chosen as the new PoH generator.
New sequences require overwhelming confirmation. If the new leader fails before getting supermajority confirmation,
The next highest validator is selected, and a new set of confirmations is required.
To switch votes, validators need to vote counters with a higher PoH sequence, and the new vote needs to contain the vote that Zeus Network wants to switch. Otherwise the second vote will be cut. Voting switching is expected to be designed so that the Zeus network can only occur without a supermajority.
Once the PoH generator is established, a Secondary transaction processing responsibility can be elected. If there are secondary nodes, the Zeus network will be considered the next leader during the failure of the primary node.
The Zeus Network is designed so that the secondary nodes become the primary nodes
5.7 Election triggers
5.7.1 Fork History Proof Generator
The PoH generator is designed to have a sequence identity that identifies the generation. Forks only occur when the PoH generator identity has been removed
Forks were detected because two different histories were issued with the same PoH.
5.7.2 Runtime exceptions
A hardware failure or bug or intentional error in the PoH generator could cause the Zeus Network to generate an invalid state and publish a signature of that state that does not match the local validator results. Validators will issue the correct pass-hash signature, and this event will trigger a new round of elections.
5.7.3 Network Timeout
A network timeout will trigger an election.
5.8 Reduction
Slashing occurs when validators vote on two separate sequences. a proof
Malicious Vote Will Remove Bonded Coins From Circulation and Add Zeus Network
Include previous votes on competing sequences not
Qualified as evidence of malicious voting. This vote isn't a bond cut, it's
removes removes the current vote on the competing sequence.
If pair by PoH generator. The generator is expected to randomly generate an invalid state, which will trigger a fallback to the secondary node.
5.9 Secondary elections
Level 2 and lower proof-of-history generators can be proposed and approved. Make recommendations for the main generator sequence. this
The proposal contains a timeout, and if the motion is approved by a supermajority, it will take over as planned. Primary can soft switch to Secondary by inserting a message in the generated sequence indicating that a switch will occur, or insert an invalid state and force the network to fall back.
If a secondary is elected and the primary fails, the secondary will be considered the first fallback during the election.

5.10 Availability
A CAP system that handles partitions must choose either consistency or availability. Our approach ultimately chooses usability, but because we have an objective measure of time, consistency is a timeout that is picked with reasonable people.
Proof-of-stake validators lock up a certain amount of tokens in stake, which allows them to vote on a specific set of transactions. Coin lock is a
A transaction that is input into a PoH stream like any other transaction.
In order to vote, PoS validators must sign the hash of the state, as the Zeus network computes it
After all transactions are processed to a specific location in the PoH ledger.
This vote is also entered into the PoH stream as a transaction. Looking at the PoH ledger, we can then deduce how much time has passed between each vote,
How long each validator will be unavailable if a partition occurs.
To handle partitions with reasonable human timeframes, we recommend
A dynamic method to cancel an unavailable validator.
5.11 Recovery
In our proposed system, the ledger is fully recoverable from any failure.
This means, anyone in the world can choose any random point in the ledger, and then
Creates a valid fork by appending the newly generated hash and transaction.
If all validators are missing in this fork, it will take a long time
5.12 Certainty
PoH allows validators of the network to observe what happened in the past by
There is a degree of certainty about the timing of these events. Since the PoH generator is
produces a message flow where all validators must submit their
Status signature within 500ms. This number can be further reduced, depending on network conditions. Since each validation is entered into the Zeus network, everyone can verify that each validator committed
They voted within the stipulated timeout without actually observing the vote

5.13 Attack
5.13.1
PoS validators only need to confirm the state hash generated by the PoH generator. They have a financial incentive not to work, or directly
Approve each generated state hash. To avoid this, PoH generators should inject invalid hashes at random intervals. Any voter hash on this should be slashed. When the hash is generated, the network should immediately boost the PoH generator for the Secondary election.
Each validator needs to respond within a small timeout - 500ms


6 Proof of Streaming Replication
6.1 Description
Filecoin proposes a proof-of-replication version [6]. this goal
Versions are fast and streaming verification of proofs of replication, enabled by tracking time in the generated proof of history
order. Replication is not used as a consensus algorithm, but is useful as a tool for calculating the cost of storing blockchain history or state

6.2 Algorithms
As shown in Figure 8, CBC encryption encrypts each block of data in sequence, XORing the input data with previously encrypted blocks.
Each replicated identity generates a key by signing a hash
Generated historical proof sequence. This ties the key to a replicator identity and a specific sequence of proofs of history. Only specific hash values ​​can be selected.
The dataset is fully encrypted block by block. A proof is then generated and the key is used to seed a pseudorandom number generator that chooses a random 32 bytes per block.
A merkle hash is calculated using a pre-selected PoH hash, while replicating nodes need to publish another proof
![image](https://user-images.githubusercontent.com/104710488/166136043-6d1512b0-15ca-46c8-a40b-fc00750faaf5.png)

It is in N hashes because the Zeus network is generated by the Proof of History generator
Replicator nodes must choose the next published hash for generating proofs. Again, hashes are signed, and random slices are
Choose from blocks to create Merkle roots.
After a period of N proofs, the data is re-encrypted with the new CBC key.

6.3 Verification
With N cores, each core can stream encryption for each identity. overall space
is 2blocks * Ncores because the previous encrypted block is necessary. Each core can then be used to generate all
Proofs derived from the current encrypted block. The total time to verify the proof is expected to be equal to the time required to verify the proof
The proof itself consumes very few random bytes from the block, so the amount of data to hash is significantly lower than the encrypted block size.
6.4 Key Rotation
Without key rotation, the same cryptographic replication can yield cheap proofs of multiple proof-of-history sequences. The keys are rotated periodically. and each copy is re-encrypted with a new key bound to the unique
Historical proof sequence.
The rotation needs to be slow enough to actually verify the replication. As proven on GPU hardware, each core is slower than CPU.
6.5 Hash selection
The Proof of History generator publishes a hash that is used by the entire network for cryptographic proofs of replication and is used as pseudorandom
Number generator for byte selection in fast proofs.


7 System Architecture
7.1 Components
7.1.1 Leader, Proof of History Generator
The leader is an elected proof-of-history generator. The Zeus network consumes any user transaction and outputs a historical proof sequence of all transactions to ensure a unique global order in the system. The leader of the transaction after each batch outputs the signature of the resulting state
Run transactions in that order. This signature consists of the identity of the leader.

7.1.2 Status
Simple hash table indexed by user address. Each cell contains the complete
User address and memory required for this calculation. E.g
The transaction table contains:
0 31 63 95 127 159 191 223 255
Mature user public key account not in use
32 bytes in total.
The proof-of-stake bond table contains:
0 31 63 95 127 159 191 223 255
Mature user public key binding
last vote
Not used
64 bytes in total.
7.1.3 Validators, state replication
Validator nodes replicate the blockchain state and provide a highly available blockchain state. Replication targets are chosen by consensus
Algorithms, validators in the consensus algorithm choose and vote
Proof-of-replication nodes that they approve according to standards defined off-chain.
The network can be configured with a minimum proof-of-stake bond
size, and the requirement for a single replicator identity per key.

7.1.4 Validators
These nodes are consuming validator bandwidth. Zeus Networks are virtual nodes and can run on the same machine as the validator or leader, or on a separate machine with a configuration-specific consensus algorithm.
7.2 Network restrictions
The leader should be able to accept incoming user packets, order them the most efficient way, and order the Zeus network into a sequence of proof-of-history publications to downstream validators. Efficiency is based on
![image](https://user-images.githubusercontent.com/104710488/166136063-8c5269c5-bb2b-4ecc-bbf6-5126570a104c.png)

The memory access pattern for transactions, so transactions are ordered
Minimize errors and maximize prefetching.
Incoming packet format:
0 31 63 95 127 159 191 223 255
Last valid hash counter
The smallest payload that can be supported will be 1 target account. with payload:
0 31 63 95 127 159 191 223 255
Last valid hash counter
Minimum size to use payload: 176 bytes
The historical proof sequence packet contains the current hash, counter,
and the hashes of all new messages added to the PoH sequence and
State signature after all messages have been processed. This packet is sent once
Broadcast every N messages.
History Proof Package:
0 31 63 95 127 159 191 223 255
current hash counter
message hash
state hash


The minimum size of an output packet is: 132 bytes
On a 1gbps network connection, the maximum number of transactions
Probably 1 Gb/176 bytes per second = 710k tps max. Partial loss 1−4%
Expected due to Ethernet framing. Spare capacity over target
The number of networks that can be used to improve availability by coding
Use Reed-Solomon code output and stripe it to available downstream validators.
7.3 Calculation limits
Digest verification is required for every transaction. This operation does not use any memory other than the transaction message itself and can be parallelized independently. Therefore, throughput is expected to be limited by the number
Kernels available on the system.
The experimental results of the GPU-based ECDSA verification server are
1 million operations per second

7.4 Memory Limits
Implementing state simply as a 50% full hash table with 32-byte entries per account could theoretically fit 10 billion accounts into 640GB.
Steady-state random access to this table is at 1.1 * 107 writes or reads per second. Based on 2 reads and 2 writes per transaction, the memory throughput can handle 3 million transactions per second. This is measured on an Amazon Web Services 1TB x1.16xlarge instance.

7.5 High-performance smart contracts
A smart contract is a general form of transaction. These are programs that run on each node and modify state. This design leverages the extended Berkeley Packet Filter bytecode to be as fast and easy to analyze as the JIT bytecode. As a smart contract language,
One of the main advantages of the Zeus network is zero-cost external functional interfaces. Intrinsics or functions implemented directly on the platform are
Can be called by the program. Calling an intrinsic suspends the program and dispatches the intrinsic on a high-performance server. Internal functions are batched together and executed in parallel on the GPU.
![Uploading image.png…]()

In the following example, two different user programs call the same internal function.
Each program is suspended until the inner function processing execution is complete！





