---
title: 'Lotus Miner: configuration reference（引用配置）'
description: 'This guide covers the Lotus Miner configuration files, detailing the meaning of the options contained in them.'
breadcrumb: 'Configuration reference'
---

# {{ $frontmatter.title }}

{{ $frontmatter.description }}

The Lotus Miner configutation is created after the [initialization step](miner-setup.md) during setup and placed in `~/.lotusminer/config.toml` or `$LOTUS_MINER_PATH/config.toml` when defined.

The _default configuration_ has all the items commented. To customize one of the items, you must remove the leading `#`.

Lotus Miner 配置是在安装过程中的[初始化步骤](miner-setup.md) 之后创建的，并在定义时放置在`~/.lotusminer/config.toml` 或`$LOTUS_MINER_PATH/config.toml` 中。

_default 配置_ 已注释所有项目。 要自定义其中一项，您必须删除前面的 `#`。

::: tip
For any configuration changes to take effect, the miner must be [restarted](miner-lifecycle.md).

任何配置修改之后，必须重启矿工进程。

:::

[[TOC]]

## API section（API部分）

The API section controls the settings of the [miner API](../../reference/lotus-api.md):

API 部分控制 [miner API](../../reference/lotus-api.md) 的设置：

```toml
[API]
  # Binding address for the miner API
  ListenAddress = "/ip4/127.0.0.1/tcp/2345/http"
  # This should be set to the miner API address as seen externally
  RemoteListenAddress = "127.0.0.1:2345"
  # General network timeout value
  Timeout = "30s"
```

As you see, the listen address is bound to the local loopback interface by default. To open access to the miner API for other machines, set this to the IP address of the network interface you want to use. You can also set it to `0.0.0.0` to allow all interfaces. API access is protected by [JWT tokens](../../build/lotus/api-tokens.md), but it should not be open to the internet.

如你所见，监听地址默认绑定到本地环回接口。 要为其他机器打开对矿机 API 的访问，请将其设置为您要使用的网络接口的 IP 地址。 您还可以将其设置为“0.0.0.0”以允许所有接口。 API 访问受 [JWT 令牌](../../build/lotus/api-tokens.md) 保护，但不应向 Internet 开放。

Configure `RemoteListenAddress` to the value that a different node would have to use to reach this API. Usually, it is the miner's IP address and API port, but depending on your setup (proxies, public IPs, etc.), it might be a different IP.

将“RemoteListenAddress”配置为其他节点必须使用才能访问此 API 的值。 通常，它是矿工的 IP 地址和 API 端口，但根据您的设置（代理、公共 IP 等），它可能是不同的 IP。

## Libp2p section（P2P网络部分）

This section configures the miner's embedded Libp2p node. As noted in the [setup instructions](miner-setup.md#connectivity-to-the-miner), it is very important to adjust this section with the miner's public IP and a fixed port:

本节配置矿工的嵌入式 Libp2p 节点。 如[设置说明](miner-setup.md#connectivity-to-the-miner)中所述，用矿工的公网IP和固定端口调整此部分非常重要：

```toml
[Libp2p]
  # Binding address for the libp2p host. 0 means random port.
  # Type: Array of multiaddress strings
  ListenAddresses = ["/ip4/0.0.0.0/tcp/0", "/ip6/::/tcp/0"]
  # Insert any addresses you want to explicitally
  # announce to other peers here. Otherwise, they are
  # guessed.
  # Type: Array of multiaddress strings
  AnnounceAddresses = []
  # Insert any addresses to avoid announcing here.
  # Type: Array of multiaddress strings
  NoAnnounceAddresses = []
  # Connection manager settings, decrease if your
  # machine is overwhelmed by connections.
  ConnMgrLow = 150
  ConnMgrHigh = 180
  ConnMgrGrace = "20s"
```

The connection manager will start to prune the existing connections if the number of established crosses the value set for `ConnMgrHigh` until it hits the value set for `ConnMgrLow`. Connections younger than `ConnMgrGrace` will be kept.

如果已建立的连接数超过为 ConnMgrHigh 设置的值，连接管理器将开始断开现有连接，直到达到为 ConnMgrLow 设置的值。 将保留比 ConnMgrGrace 更年轻的连接。

## Pubsub section

This section controls some Pubsub settings. Pubsub is used to distribute messages in the network:

此部分控制一些 Pubsub 设置。 Pubsub 用于在网络中分发消息：

```toml
[Pubsub]
  # Usually, you will not run a pubsub bootstrapping node, so leave this as false
  Bootstrapper = false
  # FIXME
  RemoteTracer = ""
  # DirectPeers specifies peers with direct peering agreements. These peers are
  # connected outside of the mesh, with all (valid) message unconditionally 
  # forwarded to them. The router will maintain open connections to these peers.
  # Note that the peering agreement should be reciprocal with direct peers
  # symmetrically configured at both ends.
  # Type: Array of multiaddress peerinfo strings, must include peerid (/p2p/12D3K...)
  DirectPeers = []
```

## Dealmaking section

This section controls parameters for making storage and retrieval deals:

此部分控制用于进行存储和检索交易的参数：

```toml
[Dealmaking]
  # When enabled, the miner can accept online deals
  ConsiderOnlineStorageDeals = true
  # When enabled, the miner can accept offline deals
  ConsiderOfflineStorageDeals = true
  # When enabled, the miner can accept retrieval deals
  ConsiderOnlineRetrievalDeals = true
  # When enabled, the miner can accept offline retrieval deals
  ConsiderOfflineRetrievalDeals = true
  # When enabled, the miner can accept verified deals
  ConsiderVerifiedStorageDeals = true
  # When enabled, the miner can accept unverified deals
  ConsiderUnverifiedStorageDeals = true
  # A list made of Data CIDs to reject when making deals
  PieceCidBlocklist = []
  # Maximum expected amount of time getting the deal into a sealed sector will take
  # This includes the time the deal will need to get transferred and published
  # before being assigned to a sector
  # for more info, see below.
  ExpectedSealDuration = "24h0m0s"
  # When a deal is ready to publish, the amount of time to wait for more
  # deals to be ready to publish before publishing them all as a batch
  PublishMsgPeriod = "1h0m0s"
  # The maximum number of deals to include in a single publish deals message
  MaxDealsPerPublishMsg = 8

  # A command used for fine-grained evaluation of storage deals (see below)
  Filter = "/absolute/path/to/storage_filter_program"

  # A command used for fine-grained evaluation of retrieval deals (see below)
  RetrievalFilter = "/absolute/path/to/retrieval_filter_program"
```

`ExpectedSealDuration` is an estimate of how long sealing will take and is used to reject deals whose start epoch might be earlier than the expected completion of sealing. It can be estimated by [benchmarking](benchmarks.md) or by [pledging a sector](sector-pledging.md).

`ExpectedSealDuration` 是对密封需要多长时间的估计，用于拒绝时间不合法的交易（开始时间早于预期密封完成时间）。 可以通过 [benchmarking](benchmarks.md) 或 [抵押一个扇区](sector-pledging.md) 来估计。

:::warning
The final value of `ExpectedSealDuration` should equal `(TIME_TO_SEAL_A_SECTOR + WaitDealsDelay) * 1.5`. This equation ensures that the miner does not commit to having the sector sealed too soon.

`ExpectedSealDuration` 的最终值应该等于 `(TIME_TO_SEAL_A_SECTOR + WaitDealsDelay) * 1.5`。 这个等式确保矿工不会承诺过早密封该扇区。

:::

### Publishing several deals in one message（在一条消息中发布多笔交易）

The `PublishStorageDeals` message can publish many deals in a single message.
When a deal is ready to be published, Lotus will wait up to `PublishMsgPeriod`
for other deals to be ready before sending the `PublishStorageDeals` message.

`PublishStorageDeals` 消息可以在一条消息中发布许多交易。当交易准备好发布时，Lotus 将等待`PublishMsgPeriod`在发送 “PublishStorageDeals” 消息之前准备好其他交易。

However, once `MaxDealsPerPublishMsg` is ready, Lotus will immediately publish all the deals.

然而，一旦 `MaxDealsPerPublishMsg` 准备就绪，Lotus 将立即发布所有交易。

For example, if `PublishMsgPeriod` is 1 hour:

例如，如果 `PublishMsgPeriod` 是 1 小时：

- At 1:00 pm, deal 1 is ready to publish. Lotus will wait until 2:00 pm for other deals to be ready before sending `PublishStorageDeals`.
- At 1:30 pm, Deal 2 is ready to publish
- At 1:45 pm, Deal 3 is ready to publish
- At 2:00pm, lotus publishes Deals 1, 2, and 3 in a single `PublishStorageDeals` message.

----

- 下午 1:00，交易 1 准备发布。在发送“PublishStorageDeals”之前，Lotus 将等到下午 2:00 等待其他交易准备就绪。
- 下午 1:30，交易 2 准备发布
- 下午 1:45，交易 3 准备发布
- 下午 2:00，lotus 在单个 `PublishStorageDeals` 消息中发布交易 1、2 和 3。


If `MaxDealsPerPublishMsg` is 2, then in the above example, when deal 2 is ready to be published at 1:30, Lotus would immediately publish Deals 1 & 2 in a single `PublishStorageDeals` message. Deal 3 would be published in a subsequent `PublishStorageDeals` message.

如果 `MaxDealsPerPublishMsg` 为 2，那么在上面的例子中，当交易 2 准备在 1:30 发布时，Lotus 会立即在单个 `PublishStorageDeals` 消息中发布交易 1 和 2。交易 3 将在后续的“PublishStorageDeals”消息中发布。

> If any of the deals in the `PublishStorageDeals` fails validation upon execution, or if the start epoch has passed, all deals will fail to be published.

> 如果 `PublishStorageDeals` 中的任何交易在执行时未通过验证，或者如果开始时期已经过去，则所有交易都将无法发布。


## Using filters for fine-grained storage and retrieval deal acceptance（使用过滤器进行细粒度的存储和检索交易）

Your use case might demand very precise and dynamic control over a combination of deal parameters.

您的用例可能需要对交易参数组合进行非常精确和动态的控制。

Lotus provides two IPC hooks allowing you to name a command to execute for every deal before the miner accepts it:

Lotus 提供了两个 IPC 钩子，允许您在矿工接受之前为每笔交易命名要执行的命令：

- `Filter` for storage deals.
- `RetrievalFilter` for retrieval deals.

----

- 存储交易的`过滤器`。
- 用于检索交易的`RetrievalFilter`。

The executed command receives a JSON representation of the deal parameters on standard input, and upon completion, its exit code is interpreted as:

执行的命令在标准输入上接收交易参数的 JSON 表示，完成后，其退出代码被解释为：

- `0`: success, proceed with the deal.
- `non-0`: failure, reject the deal.

The most trivial filter rejecting any retrieval deal would be something like:
`RetrievalFilter = "/bin/false"`. `/bin/false` is binary that immediately exits with a code of `1`.

拒绝任何检索交易的最简单的过滤器是这样的：
`RetrievalFilter = "/bin/false"`。 `/bin/false` 是二进制文件，它立即以代码 1 退出。

[This Perl script](https://gist.github.com/ribasushi/53b7383aeb6e6f9b030210f4d64351d5/9bd6e898f94d20b50e7c7586dc8b8f3a45dab07c#file-dealfilter-pl) lets the miner deny specific clients and only accept deals that are set to start relatively soon.

上面的脚本让矿工拒绝制定的客户端，只接受尽快开始的交易。

You can also use a third party content policy framework like `bitscreen` by Murmuration Labs:

您还可以使用第三方内容策略框架，例如 Murmuration Labs 的“bitscreen”：

```sh
# grab filter program
go get -u -v github.com/Murmuration-Labs/bitscreen

# add it to both filters
Filter = "/path/to/go/bin/bitscreen"
RetrievalFilter = "/path/to/go/bin/bitscreen"
```

## Sealing section（密封部分）

This section controls some of the behavior around sector sealing:

这部分配置控制关于扇区密封的一些行为。

```toml
[Sealing]
  # Upper bound on how many sectors can be waiting for more deals to be packed in it before it begins sealing at any given time.
  # If the miner is accepting multiple deals in parallel, up to MaxWaitDealsSectors of new sectors will be created.
  # If more than MaxWaitDealsSectors deals are accepted in parallel, only MaxWaitDealsSectors deals will be processed in parallel
  # Note that setting this number too high in relation to deal ingestion rate may result in poor sector packing efficiency
  MaxWaitDealsSectors = 2
  # Upper bound on how many sectors can be sealing at the same time when creating new CC sectors (0 = unlimited)
  MaxSealingSectors = 0
  # Upper bound on how many sectors can be sealing at the same time when creating new sectors with deals (0 = unlimited)
  MaxSealingSectorsForDeals = 0
  # Period of time that a newly created sector will wait for more deals to be packed in to before it starts to seal.
  # Sectors which are fully filled will start sealing immediately
  WaitDealsDelay = "6h0m0s"
  # Whether to keep unsealed copies of deal data regardless of whether the client requested that. This lets the miner
  # avoid the relatively high cost of unsealing the data later, at the cost of more storage space
  AlwaysKeepUnsealedCopy = true
  # Run sector finalization before submitting sector proof to the chain
  FinalizeEarly = false
  # Whether to use available miner balance for sector collateral instead of sending it with each message
  CollateralFromMinerBalance = false
  # Minimum available balance to keep in the miner actor before sending it with messages
  AvailableBalanceBuffer = 0
  # Don't send collateral with messages even if there is no available balance in the miner actor
  DisableCollateralFallback = false
  # enable / disable precommit batching (takes effect after nv13)
  BatchPreCommits = true
  # maximum precommit batch size up to 256 sectors - batches will be sent immediately above this size
  MaxPreCommitBatch = 256
  # how long to wait before submitting a batch after crossing the minimum batch size
  PreCommitBatchWait = "24h0m0s"
  # time buffer for forceful batch submission before sectors/deal in batch would start expiring
  PreCommitBatchSlack = "3h0m0s"

  # enable / disable commit aggregation (takes effect after nv13)
  AggregateCommits = true
  # minimum batched commit size, no less than 4
  MinCommitBatch = 4
  # maximum batched commit size up to 819 sectors - batches will be sent immediately above this size
  MaxCommitBatch = 819
  # how long to wait before submitting a batch after crossing the minimum batch size
  CommitBatchWait = "24h0m0s"
  # time buffer for forceful batch submission before sectors/deals in batch would start expiring
  CommitBatchSlack = "1h0m0s"

  # network BaseFee below which to stop doing commit aggregation, instead submitting proofs to the chain individually
  AggregateAboveBaseFee = 0.00000000015 #0.15nano
  

  TerminateBatchMax = 100
  TerminateBatchMin = 1
  TerminateBatchWait = "5m0s"

```

### PreCommitSectorsBatch

`PreCommitSectorsBatch` introduced by [FIP-0008 ](https://github.com/filecoin-project/FIPs/blob/master/FIPS/fip-0008.md) supports a miner to pre-commit a number of sectors at once. 

`PreCommitSectorsBatch` 支持一个矿工对一批扇区进行pre-commit。

In lotus v1.10.0 and up, if `BatchPreCommit` is set to false, pre-commitments will be sent to the chain via `PreCommitSector` messages once they are ready. If `BatchPreCommit` is set to true, lotus will batch pre-commitments until any of `MaxPreCommitBatch`, `PreCommitBatchWait` or `PreCommitBatchSlack` is hit:
- `MaxPreCommitBatch` is the maximum amount of sectors' pre-commitments to batch in one `PreCommitSectorsBatch` message. According to FIP-0008, this values is up to 256.
- `PreCommitBatchWait` is how long to wait before submitting the current batch. Note: the ticket of pre-commitment has an expiration of approximately **31.5 hours**, one sector's pre-commit ticket expires **WILL** cause the whole message to fail. Therefore, **we recommend miners to set this value lower than 30 hours.**
- `PreCommitBatchSlack` is the time buffer to forcefully submit the current batch before any of the sector's pre-commit ticket or a deal will expire. For example, if this value is set to 1 hour, which is 120 epochs, then a `PreCommitSectorsBatch` message will be submitted for the existing batch 120 epochs before the earliest epoch among precommits' tickets and deal's start epochs in this batch. **We recommend you to set a longer slack to prevent message failures due to expirations.**

> Note, the current batch will be sent if any of `MaxPreCommitBatch`, `PreCommitBatchWait` or `PreCommitBatchSlack` is hit.

To check the list of the sectors pre-commitments that are in the batching queue, run:

```
./lotus-miner sectors batching precommit
```

the output is the sector ids:

```
$ ./lotus-miner sectors batching precommit
14
15
16
```

To ignore the configuration and force push the current batch, run:

```
./lotus-miner sectors batching precommit --publish-now=true
```

Then in the output, the message CID of the `PreCommitSectorsBatch` message and the sector number of the sectors' pre-commitments that are being submitted is listed:

```
$ ./lotus-miner sectors batching precommit --publish-now=true
Batch 0:
	Message: bafy2bzacecgihnlvbsqu7yksco3vs5tzk3ublbcnkedlofr6nhbq55k5ye3ci
	Sectors:
		14	OK
		15	OK
		16	OK
```

### ProveCommitAggregate

`ProveCommitAggregate` introduced by [FIP-0013](https://github.com/filecoin-project/FIPs/blob/master/FIPS/fip-0013.md) supports a miner to prove-commit a number of sectors at ones. 

In Lotus v1.10.0 and up, if `AggregateCommits` is set to false, prove-commitments will be sent to the chain via `ProveCommitSector` messages once they are ready. If `AggregateCommits` is set to true, lotus will aggregate and batch pre-commitments until any of `MaxCommitBatch`, `CommitBatchWait` or `CommitBatchSlack` is hit:
- `MaxCommitBatch` is the maximum amount of sectors' prove-commitments to batch in one `ProveCommitAggregate` message. According to FIP-0013, this value is up to 819.
- `CommitBatchWait` is how long to wait before submitting the current batch **after** crossing `MinCommitBatch`. Note: a prove-commitment must be submitted **within 30 days** after the pre-commit has landed on-chain. It is recommended to set this value lower than 30 days to prevent collateral loss. 
- `CommitBatchSlack` is the time buffer to forcefully submit the current batch before any of the sector's pre-commitment or a deal will expire. For example, if this value is set to 1 hour, which is 120 epochs, then a `ProveCommitAggregate` message will be submitted for the existing batch 120 epochs before the earliest epoch among precommits' expirations and deals' start epochs in this batch. **We recommend you to set a longer slack to prevent message failures due to deal expirations or loss of collateral**
- `AggregateAboveBaseFee` is the network base fee to start aggregating proofs. When the network base fee is lower than this value, the prove commits will be submitted individually via `ProveCommitSector`. **According to the [Batch Incentive Alignment](https://github.com/filecoin-project/FIPs/blob/master/FIPS/fip-0013.md#batch-incentive-alignment) introduced in FIP-0013, we recommend you to set this value to 0.15 nanoFIL to avoid unexpected aggregation fee in burn.**

`MinCommitBatch` is the minimum amount of sectors' prove-commitment to be batched in one `ProveCommitAggregate` message. According to FIP-0013, this value cannot be less than 4, which is the cross-over point where prove-commit aggregation wins out on single prove-commit gas costs. If any of `MaxCommitBatch`, `CommitBatchWait` or `CommitBatchSlack` is hit by the amount of prove-commit is the batching queue is less than `MinCommitBatch`, then prove-commitments in this batch will be proceeded individually via `ProveCommitSector`. 

> Note: Aggregated proofs will incur a discounted Gas Charge, so overall, it will be less gas usage than the same number of proofs on-chain, but a minimum fee will apply. It is _cheaper per proof_ to aggregate more proofs into a single aggregate message, meaning aggregating 1000 proofs is more beneficial than aggregating 10 sectors. So if a miner wants to onboard more storage, it is recommended to aggregate more proofs into a single message.

To check the list of the sectors prove-commitments that are in the batching queue, run:

```
./lotus-miner sectors batching commit
```

the output is the sector ids:

```
$ ./lotus-miner sectors batching commit
10
11
12
13
14
15
16
17
```

To ignore the configuration and force push the current batch, run:

```
./lotus-miner sectors batching commit --publish-now=true
```

Then in the output, the message CID of the `ProveCommitAggregate` message and the sector number of the sectors' prove-commitments that are being submitted is listed:

```
$ ./lotus-miner sectors batching commit --publish-now=true
Batch 0:
	Message: bafy2bzacedtmykgf5g4evdvapacpmo4l32ewu5l7yxqkzjh3h6fhev7v7qoys
	Sectors:
		15	OK
		17	OK
		12	OK
		10	OK
		11	OK
		13	OK
		16	OK
		14	OK
```

If the sectors in the queue are less than `MinCommitBatch`, then individual `ProveCommitSector` messages will be sent for each sector:

```
Batch 0:
	Message: bafy2bzacedpwysxdsg2ft3hfbwn6ayyaanivfwkx4inav3zm34hwmmwgsljkk
	Sectors:
		18	OK
Batch 1:
	Message: bafy2bzacedrx7l34ckaue7hm2ubousl3djuigyu2xw4xzywgkhttxecsm5ba2
	Sectors:
		19	OK
```

## Storage section

The storage sector controls whether the miner can perform certain sealing actions. Depending on the setup and the use of additional [seal workers](seal-workers.md), you may want to modify some of the options.

```toml
[Storage]
  # Upper bound on how many sectors can be fetched in parallel by the storage system at a time
  ParallelFetchLimit = 10
  # Sealing steps that the miner can perform itself. Sometimes we have a dedicated seal worker to do them and do not want the miner to commit any resources for this.
  AllowAddPiece = true
  AllowPreCommit1 = true
  AllowPreCommit2 = true
  AllowCommit = true
  AllowUnseal = true
```

## Fees section

The fees section allows to set limits to the gas consumption for the different messages that are submitted to the chain by the miner:

```toml
[Fees]
  # Maximum fees to pay
  MaxPreCommitGasFee = "0.025 FIL"
  MaxCommitGasFee = "0.05 FIL"
  MaxTerminateGasFee = "0.5 FIL"
  # This is a high-value operation, so the default fee is higher.
  MaxWindowPoStGasFee = "5 FIL"
  MaxPublishDealsFee = "0.05 FIL"
  MaxMarketBalanceAddFee = "0.007 FIL"
  [Fees.MaxPreCommitBatchGasFee]
      Base = "0 FIL"
      PerSector = "0.02 FIL"
  [Fees.MaxCommitBatchGasFee]
      Base = "0 FIL"
      PerSector = "0.03 FIL" 

```

Depending on the network congestion, the base fee for a message may grow or decrease. Your gas limits will have to be larger than the base fee for the messages to be included. A very large max fee can, however, result in the quick burning of funds when the base fee is very high, as the miner automatically submits messages during normal operation, so be careful about this. It is also necessary to have more funds available than any max fee set, even if the actual fee will be far less than the max fee set.

Set the maximum cost you are willing to pay for onboarding **per** sector in `MaxPreCommitBatchGasFee.PerSector`/`MaxCommitBatchGasFee.PerSector` to avoid unexpected high costs. 

> Note: The current `MaxCommitBatchGasFee.PerSector` is enough to aggregate proofs for 6 sectors. Adjust this respectively according to your operation. **If the value is too low, the message may wait in the mempool for a long while. If you don't have enough funds, the message will not be sent.**

## Addresses section

The addresses section allows users to specify additional addresses to send messages from. This helps mitigate head-of-line blocking for important messages when network fees are high. For more details see the [Miner addresses](miner-addresses.md) section.

```toml
[Addresses]
  # Addresses to send PreCommit messages from
  PreCommitControl = []
  # Addresses to send Commit messages from
  CommitControl = []
  # Addresses to send Terminate Sector messages from
  TerminateControl = []
  # Addresses to send PublishStorageDeals from
  DealPublishControl = []
  # Disable the use of the owner address for messages which are sent automatically.
  # This is useful when the owner address is an offline/hardware key
  DisableOwnerFallback = false
  # Disable the use of the worker address for messages for which it's possible to use other control addresses
  DisableWorkerFallback = false
```
