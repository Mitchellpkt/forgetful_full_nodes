# Forgetful full nodes

Mitchell / Isthmus - June 2020

Disclaimer: Random shower thoughts, not necessarily a good idea! Somebody else probably thought of this first, please point me towards their prior art. :- )

For much of my life, I witnessed an exhilarating increase in storage capacity of consumer devices from megabytes to low terabytes a decade ago! Then the cloud paradigm swallowed both consumer and business habits, so data and storage capacity increases both ended up in data centers instead of end user devices. 

In 2010 I anticipated ubiquitous 10+ TB hard drives by 2020. Instead, a different approach was embraced in the intervening decade: **the “cloud” tradeoff reduces local device storage requirements at the expense of increased bandwidth.**

This course of technological history has stranded us with high-GB to low-TB scale laptop/desktop hard drives, which is terribly inconvenient for ever-growing distributed ledger databases and their egalitarian aspirations. Alas!

Thus the shower thought: we can make the same cloud tradeoff - increased bandwidth for decreased local storage requirements. Furthemore, we can still “trust but verify” without the security tradeoffs of a remote node or SPV wallet. I can implement this locally today without needing to change the existing p2p protocol.

For a simple example, suppose I am currently running a full node that has verified every transaction from the genesis block to the current height. To reclaim disk space, I discard everything except a skeleton indexing of outputs, transaction hashes, and block height/hashes.

When I receive a new block/transaction, I iterate over the inputs, and completely verify each according to the standard verification protocol. Instead of retrieving the cryptographic proofs related with those outputs from local storage, I fetch them on-the-fly from my peers.

The locally-stored output/txn/block index should be on the scale of megabytes, but would have the same security guarantee as a full node that retains all of the blocks. (*note, this assumes a secure hash function, but then again, doesn’t everything?*)

On a practical note: The fact that spend time distributions (and, where applicable, ring member selection algorithms) skew towards recent transactions allows a convenient optimization! I maintain a rolling buffer that contains the full contents of the most recent blocks. 

To pick the buffer size, we want to consider the historical spend time distribution (and, where applicable, ring member selection algorithms). Based on this [historical data](https://www.researchgate.net/figure/CDFs-of-spend-time-distributions-in-Bitcoin-and-in-Monero-deducible-transaction-inputs_fig7_324863990) a one-month window seems like a good starting point. The buffer would be only megabyte scale, and yet would contain more than 80% of the outputs referenced in real-time transactions. This would greatly reduce the bandwidth requirements with minimal local space requirements!

The downsides are obvious: increased device bandwidth, increased network load, and concerns revolving around “what if nobody stores the full blockchain anymore?”

The upsides are: full-node security with a local database that only takes up megabytes of device storage, and the expanded adoption that this would enable.
