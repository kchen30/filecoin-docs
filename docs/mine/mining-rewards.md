---
title: 'Mining rewards（挖矿奖励）'
description: 'In Filecoin, miners earn different types of rewards by contributing to the network.'
breadcrumb: 'Mining rewards'
---

# {{ $frontmatter.title }}

{{ $frontmatter.description }}

There are two main types of rewards for their efforts: storage fees and block rewards.

目前有两种主要的获得奖励的方式：存储费用和区块奖励

## Storage fees（存储费用）

_PoSt (Proof-of-Spacetime)_ window checks are performed on 24 hour intervals across the network to ensure that miners are continuing to host their required sectors as normal. Correspondingly, each storage miner’s set of pledged sectors is partitioned into subsets, one subset for each window. Within a given window, each storage miner must submit a PoSt for each sector in their respective subset. For each day a miner is inactive it will receive a [fault fee](slashing.md).

_PoSt（时空证明）_ 时间窗口检查每隔 24 小时在整个网络中执行，以确保矿工继续照常托管所需的扇区。 相应地，每个存储矿工的质押扇区集被划分为子集，每个窗口一个子集。 在给定的时间窗口内，每个存储矿工必须为其各自子集中的每个扇区提交一个 PoSt。 对于矿工不活跃的每一天，它都会收到[故障费](slashing.md)。

***注：目前每24个小时分为48个时间窗口，每30分钟一个时间窗口，矿工承诺扇区会被分成若干组，每组对应一个时间窗口。***

**Storage fees** are the fees paid regularly by clients after a deal has been reached, in exchange for storing data. These fees are automatically deposited into a miner's associated withdrawal wallet as they continue to perform their duties over time, and are briefly locked upon being received.

**存储费用**是客户在达成交易后定期支付的费用，以换取存储数据。 这些费用会自动存入矿工的相关提款钱包，因为他们会随着时间的推移继续履行职责，并在收到后短暂锁定。

## Block rewards（区块奖励）

**Block rewards** are large sums that are given to the miner credited for a new block. Unlike storage fees, these rewards do not come from an associated client; rather, the network "prints" new FIL as both an inflationary measure and an incentive to miners advancing the chain. All active miners on the network have a chance at receiving a block reward, their chance at such being directly proportional to the amount of storage space currently being contributed to the network.

**区块奖励**是给予被记入新区块的矿工的大笔金额。与仓储费不同，这些奖励不是来自相关客户；相反，该网络“发行”新的 FIL 作为通货膨胀措施和对矿工推进链的激励。网络上所有活跃的矿工都有机会获得区块奖励，他们获得的机会与当前贡献给网络的存储空间量成正比。

The mechanism to earn the right to mine a new block is called _WinningPoSt_. In the Filecoin network, time is discretized into a series of epochs – the blockchain's height corresponds to the number of elapsed epochs. At the beginning of each epoch, a small number of storage miners are elected to mine new blocks. Additionally to the block reward, each miner can collect the fees associated to each message included in the block.

获得新区块开采权的机制称为_WinningPoSt_。在 Filecoin 网络中，时间被划分为一系列时间段——区块链的高度对应于经过的时间段数。在每个 时间段 开始时，会选出少量的存储矿工来挖掘新区块。除了区块奖励之外，每个矿工还可以收取与区块中包含的每条消息相关的费用。

The number of blocks on every tipset is based on a Poisson distribution of a random variable with λ = 5. Miner implementations may use several strategies to choose which messages to include in every block to minimize overlap. Only the "first execution" of each message will collect the associated fees, with executions ordered per the hash of the VRF (Verifiable Random Function) ticket associated to the block.

每个tipset 上的块数基于λ = 5 的随机变量的泊松分布。矿工实现可能使用多种策略来选择每个块中包含哪些消息以最小化重叠。只有每条消息的“第一次执行”才会收取相关费用，执行根据与块相关联的 VRF（可验证随机函数）票证的哈希排序。

## Verified clients（已验证客户）

To further incentivize the storage of "useful" data over simple capacity commitments, storage miners have the additional opportunity to compete for special deals offered by _verified clients_. Such clients are certified with respect to their intent to offer deals involving the storage of meaningful data, and the power a storage miner earns for these deals is augmented by a multiplier. The total amount of power a given storage miner has, after accounting for this multiplier, is known as **quality-adjusted power**.

为了通过简单的容量承诺进一步激励“有用”数据的存储，存储矿工有额外的机会来竞争_已验证客户_提供的特殊交易。 这些客户在提供涉及存储有意义数据的交易的意图方面得到了认证，并且存储矿工通过这些交易获得的权力会因乘数而增加。 考虑到这个乘数后，给定存储矿工的总功率称为**质量调整功率**。

## Retrieval fees（检索费用）

Retrieval fees are paid incrementally using _payment channels_ as the retrieval deals are fulfilled (by sending portions of the data to the client. This happens off-chain.

随着检索交易的完成（通过向客户端发送部分数据。这发生在链外），检索费用使用_支付渠道_递增支付。
