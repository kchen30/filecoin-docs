---
title: 'How mining works（挖矿工作原理）'
description: 'Filecoin mining involves accepting deals and honoring them by inserting proofs in the chain. This provides an overview of the mining process.'
breadcrumb: 'How mining works'
---

:::tip ProtoSchool tutorial available
Take a look at the [Filecoin Tutorial over at ProtoSchool](https://proto.school/verifying-storage-on-filecoin) to get a better understanding of how mining works.
:::

# {{ $frontmatter.title }}

In most blockchain protocols, "miners" are the participants on the network that do the work necessary to advance the blockchain and maintain its validity. For providing these services, miners are compensated in the native cryptocurrency. The term "miner" emerged in the initial Proof-of-Work era, comparing the work done by hardware miners using computational power to secure blockchains with that of gold miners who expended vast physical resources for a chance at a large payout.

在大多数区块链协议中，“矿工”是网络上的参与者，他们保障和推进区块链正确有效的工作。为了提供这些服务，矿工获得加密货币作为报酬。 “矿工”一词出现在最初的工作量证明时代，将硬件矿工使用计算能力来保护区块链的工作与黄金矿工的工作进行比较，后者花费大量物理资源以获得大笔支出。

Mining in Filecoin works quite differently however -- instead of contributing computational power, miners contribute storage capacity to be used in deals with clients looking to store data.

然而，Filecoin 中的挖掘工作完全不同——矿工不是贡献计算能力，而是贡献存储容量，用于与希望存储数据的客户进行交易。


## Types of miners（矿工类型）

The Filecoin network will have multiple types of miners:

- _Storage miners_, responsible for storing files and data on the network.
- _Retrieval miners_, responsible for providing quick pipes to retrieve files.
- _Repair miners_, to be implemented.

**Storage miners** are the heart of the network. They earn Filecoin by storing data for clients, and computing cryptographic proofs to verify storage across time. The probability of earning the block reward and transaction fees is proportional to the amount of storage the miner contributes to the Filecoin network, and not hashing power.

**Retrieval miners** are the veins of the network. They earn Filecoin by winning bids and mining fees for a particular file, which are determined by the market value of said file's size. A retrieval miner’s bandwidth and bid/initial response time for deals (i.e., latency and proximity to clients) will determine its ability to close retrieval deals on the network. The maximum bandwidth of a retrieval miner will set the total quantity of deals it can make.

Filecoin 网络将有多种类型的矿工：

- _存储矿工_，负责在网络上存储文件和数据。
- _检索矿工_，负责提供快速检索文件的能力。
- _修复矿工_，待实施。

**存储矿工**是网络的核心。他们通过为客户存储数据并计算加密证明以及跨时间验证存储来赚取 Filecoin。获得区块奖励和交易费用的概率与矿工为 Filecoin 网络贡献的存储量成正比，而不是hash能力。

**检索矿工**是网络的脉络。他们通过赢得特定文件的投标和挖掘费用来赚取 Filecoin，这些费用由该文件大小的市场价值决定。检索矿工的带宽和交易的出价/初始响应时间（即延迟和与客户的接近程度）将决定其在网络上完成检索交易的能力。检索矿工的最大带宽将设置它可以进行的交易总量。

## Deals（交易）

Deals are the core function of the Filecoin network, and represent an agreement made between a client and a miner for a storage 'contract'.

交易是 Filecoin 网络的核心功能，代表客户和矿工之间就存储“合同”达成的协议。

Once a client has decided on a miner to store with based on their available capacity, duration, and desired price, they lock up sufficient funds in an associated wallet to cover the total cost of the deal. The deal is then published once the miner has accepted the storage agreement.

一旦客户根据他们的可用容量、持续时间和期望的价格决定了要存储的矿工，他们就会在相关的钱包中锁定足够的资金来支付交易的总成本。一旦矿工接受存储协议，交易就会发布。

After the deal is published, the client then prepares the data for storage and then transfers it to the miner. Upon receiving all of the data, the miner packs the data into a sector, seals it, and begins submitting proofs to the chain. Once the first confirmation has been hit, the client can be certain the data is being stored properly, and the deal has officially begun.

交易发布后，客户端准备存储数据，然后将其传输给矿工。收到所有数据后，矿工将数据打包到一个扇区中，密封它，并开始向链提交证明。一旦第一次确认被击中，客户就可以确定数据被正确存储，交易正式开始。

Throughout the lifetime of the deal, the miner submits ongoing proofs to the chain. Clients pay incrementally using the funds they previously locked up. If a proof is missing or delayed, the miner is penalized. More information on this can be found in the [slashing](slashing.md) section.

在交易的整个生命周期中，矿工向链提交持续的证明。客户使用他们之前锁定的资金逐步支付。如果证据丢失或延迟，矿工将受到惩罚。更多信息可以在 [故障费](slashing.md) 部分找到。

## Power and rewards

Each Filecoin miner has an associated _power_ value in the network that is proportional to the amount of space contributed and determines the chances to win the right to mine a block in every epoch. By mining blocks, miners obtain _block rewards_ and collect fees for the messages included in that block.

每个 Filecoin 矿工在网络中都有一个关联的 _power_ 值，该值与贡献的空间量成正比，并决定了在每个时期赢得区块开采权的机会。 通过挖掘区块，矿工获得_区块奖励_并为包含在该区块中的消息收取费用。

Additionally, miners submit storage proofs regularly to the chain. This enables them to obtain _storage fees_ from the clients that have made deals with them.

此外，矿工定期向链提交存储证明。 这使他们能够从与他们进行交易的客户那里获得_存储费用_。

Additional details about this can be found in the [mining rewards](mining-rewards.md) section.

有关这方面的其他详细信息，请参见 [挖矿奖励](mining-rewards.md) 部分。