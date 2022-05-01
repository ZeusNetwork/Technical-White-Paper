# Technical-White-Paper
Zeus network technology principle and underlying architecture guide

#𝗪𝗵𝗮𝘁 𝗶𝘀 𝘁𝗵𝗲 𝗭𝗲𝘂𝘀 𝗡𝗲𝘁𝘄𝗼𝗿𝗸❓
A blockchain is an implementation of a fault-tolerant replicated state machine. Currently publicly available blockchains do not rely on time, or make assumptions about the ability of participants to maintain time. At the same time each node in the blockchain network usually relies on their own local clock without knowing the clocks of other participants in any network. The lack of a reliable source of time means that when message timestamps are used to accept or reject messages,
![image](https://user-images.githubusercontent.com/104710488/166135854-4a3304c1-90d4-47a4-b457-e07983f48ee4.png)

There is no guarantee that every other participant in the network will choose exactly the same. The PoH presented here aims to create a ledger with a verifiable elapse of time, i.e. the duration between events and messages
order. It is expected that every node in the network will be able to rely on the elapse of time recorded in the ledger without trust.

