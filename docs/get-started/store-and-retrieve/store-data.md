---
title: Store data（存储数据）
description: Start storing your data on the Filecoin network. This section covers packaging your data, importing it into your local Lotus lite-node, finding a storage provider through the Filecoin Plus miner registry, creating a storage deal, and then waiting for the deal to complete! 
---

# Store data（存储数据）

Start storing your data on the Filecoin network. This section covers packaging your data, importing it into your local Lotus lite-node, finding a storage provider through the Filecoin Plus miner registry, creating a storage deal, and then waiting for the deal to complete. There's a lot to do, so let's dive in!

开始在 Filecoin 网络上存储您的数据。 本节介绍打包数据、将其导入本地 Lotus lite 节点、通过 Filecoin Plus 矿工注册表查找存储提供商、创建存储交易，然后等待交易完成。 有很多事情要做，所以让我们开始吧！

:::danger
Filecoin is optimized for public data and doesn't yet support access controls. If storing private data, ensure you encrypt it before storage to ensure it remains unreadable by anyone without the ability to decrypt it. Keep in mind that if a vulnerability is found in your encryption process at any point in the future, then your data may be compromised.

Filecoin 针对公共数据进行了优化，尚不支持访问权限控制。 如果存储私人数据，请确保在存储之前对其进行加密，以确保任何无法解密的人都无法读取它。 请记住，如果在未来的任何时候在您的加密过程中发现漏洞，那么您的数据可能会受到损害。

:::

## Things to note（记录）

As you're going through this section, make a note of the following variables: 

| Variable | Description | Example |
| --- | --- | --- |
| Data CID | The content identifier (CID) of the data that you want to store using Filecoin. | `bafk2bzaceajz56zudni2hli7id6jvvpo5n4wj5eoxm5xwj2ipthwc2pkgowwu` |
| Miner ID #1 | The unique identifier for each storage provider. You need to have two storage provider IDs for this tutorial. | `f01000`
| Miner ID #2 | The unique identifier for each storage provider. You need to have two storage provider IDs for this tutorial. | `f01000`
| Deal CID | The content identifier (CID) for a deal made with a storage provider. | `bafyreict2zhkbwy2arri3jgthk2jyznck47umvpqis3hc5oclvskwpteau` | 

| 字段 | 描述 | 示例 |
| --- | --- | --- |
| Data CID | 您要使用 Filecoin 存储的数据的内容标识符 (CID)。 | `bafk2bzaceajz56zudni2hli7id6jvvpo5n4wj5eoxm5xwj2ipthwc2pkgowwu` |
| Miner ID #1 | 每个矿工的唯一标识符。 本教程需要两个矿工 ID。 | `f01000`|
| Miner ID #2 | T每个矿工的唯一标识符。 本教程需要两个矿工 ID。 | `f01000`|
| Deal CID | 与矿工达成的交易的内容标识符 (CID)。 | `bafyreict2zhkbwy2arri3jgthk2jyznck47umvpqis3hc5oclvskwpteau` |

## Prepare your data（准备数据）

For this tutorial, we're going to create a dummy 5GB file full of random data and store it on the Filecoin network.

在本教程中，我们将创建一个包含随机数据的 5GB 虚拟文件并将其存储在 Filecoin 网络上。

1. Move into your home folder:

    ```shell
    cd ~
    ```

1. Create a 5GB block of random data to serve as our payload:（使用dd命令创建一个5GB大小的随机文件，/dev/urandom是unix系统中的一个特殊设备文件，用作随机数生成器或伪随机数生成器，生成原理源于收集设备驱动程序或其他来源的背景噪声）

    **MacOS** users must run:

    ```shell
    dd if=/dev/urandom of=5gb-filecoin-payload.bin bs=1m count=5200
    ```

    **Linux** users should run: 

    ```shell
    dd if=/dev/urandom of=5gb-filecoin-payload.bin bs=1M count=5200
    ```

    This process will take about 60 seconds to create a dummy file.

We now have our payload file ready to be stored using the Filecoin network.

## Add data to Lotus（添加数据到Lotus）

We need to tell our Lotus lite-node which file we want to store using Filecoin.

1. Import the payload into the `lotus daemon` using the `import` command: （通过import命令添加文件）

    ```shell
    lotus client import 5gb-filecoin-payload.bin 
    ```

    Lotus creates a directed acyclic graph (DAG) based off the payload. This process takes a few minutes. Once it's complete, Lotus will output the payload CID.

    Lotus基于要加载的内容创建了一个DAG（DAG：Directed Acyclic Graph，中文意为「有向无环图」。 DAG原本是计算机领域一种常用数据结构，因为独特的拓扑结构所带来的优异特性，经常被用于处理动态规划、导航中寻求最短路径、数据压缩等多种算法场景。），这个过程需要几分钟。完成后，Lotus会输出数据 CID。

    ```text output
    Import 3, Root bafykb...
    ```

    This process takes about 60 seconds.

1. Make a note of the CID `bafykb...`. This is your **Data CID**. We'll use it in an upcoming section.

Now that Lotus knows which file we want to use, we can create a deal with a Filecoin storage provider to store our data!

## Find a storage provider （找到一个存储提供商）

We need to find suitable storage providers before we can store our data. The Filecoin network allows storage providers to compete by offering different terms for pricing, acceptable data sizes, and other important deal parameters. It's also important to consider the storage provider's location; the closer the storage provider is to you, the faster the storage and retrieval process will be. 

我们需要先找到合适的存储提供商，然后才能存储我们的数据。 Filecoin 网络允许存储提供商通过提供不同的定价、可接受的数据大小和其他重要的交易参数来进行竞争。 考虑存储提供商的位置也很重要； 存储提供商离您越近，存储和检索过程就会越快。

We're going to use the Filecoin Plus miner registry to find a couple of storage providers and then cross-check their information with a third-party storage provider reputation system.

我们将使用 Filecoin Plus 矿工注册表来查找几个存储提供商，然后与第三方存储提供商信誉系统交叉检查他们的信息。

:::tip
Increasing the number of storage providers you use increases your data redundancy, and decreases the chances of your data being lost.

更多的数据冗余、更多的数据提供商会降低您数据丢失的几率。

:::

### Filecoin Plus miner registry （Filecoin+ 矿工注册表）

The Filecoin Plus miner registry is a collection of geographically diverse storage providers that are willing to accept low-cost or free storage deals from users. The more storage providers that offer storage in different parts of the world, the faster we can work toward Filecoin’s underlying mission to store humanity’s most important information. Checkout the [Filecoin Plus page](../../store/filecoin-plus.md) to find out more about the program.

Filecoin Plus 矿工注册表是地理上不同的存储提供商的集合，这些提供商愿意接受用户的低成本或免费存储交易。 在世界不同地区提供存储的存储提供商越多，我们就越能更快地实现 Filecoin 存储人类最重要信息的使命。 查看 [Filecoin Plus 页面](../../store/filecoin-plus.md) 以了解有关该程序的更多信息。

Let's find a couple of storage providers to store our data.

让我们找到几个存储提供商来存储您的数据。

1. Go to [plus.fil.org/miners](https://plus.fil.org/miners/).
1. Using the table, find a couple of storage providers that suit your needs. Try to find storage providers that are geographically close to you.
1. Once you have found a couple of suitable storage providers, make a note of their _miner IDs_ from the **Miner ID** column:

    ![A collection of storage providers listed in the Filecoin Plus miner registry.](./images/miner-x-listings.png)

    Some storage providers list multiple miner IDs. For these storage providers, just copy one of the IDs:

    ![A list of storage providers, highlighting one storage provider with multiple IDs.](./images/miner-with-multiple-miner-ids.png)

1. Make sure to write down the IDs of the storage providers you want to use. We'll be referring to these IDs in the next section.

### Miner reputation systems （矿工声誉系统）

The Filecoin Plus miner registry is a great resource, but it represents a small portion of the entire Filecoin mining community. Filecoin reputation systems like [FilRep](https://filrep.io) can help you compare storage providers based on their past performance and provide useful information about the deal parameters that a storage provider will accept. Using FilRep, you can compare storage provider metrics like location, storage power in the network, pricing, and overall success rate.

Filecoin+ 矿工注册表是一个很好的资源，但是它只是代表了整个Filecoin挖矿社区的一小部分，像[FilRep](https://filrep.io)这样的 Filecoin 信誉系统可以帮助您根据过去的表现比较存储提供商，并提供有关存储提供商将接受的交易参数的有用信息。 使用 FilRep，您可以比较存储提供商指标，例如位置、存储能力、定价和总体成功率等。

We're going to use FilRep to check that the minimum deal size of the storage providers we selected fits the size of our file.

1. Go to [filrep.io](https://filrep.io).
1. Click the **Settings** toggle to display a list of all available storage provider details.
1. Make sure that the **Min File Size** column is selected:

    ![](./images/filrep-select-columns.png)

1. Now you can search for the storage providers you found before, using the miner ID. 

    ![](./images/filrep-search-min-file-size.png)

1. Check that the minimum file size is lower than 5 GiB, and that they charge 0 FIL for verified deals.
1. If the minimum file size shown for any of your storage providers is larger than 5 GiB, or they charge more thann 0 FIL for verified deals, go back to [the previous section](#filecoin-plus-miner-registry) and select a new storage provider.（如果您的任何存储提供商显示的最小文件大小大于 5 GiB，或者他们对经过验证的交易收取超过 0 FIL 的费用，请返回 [上一部分](#filecoin-plus-miner-registry) 并选择一个 新的存储提供商。）

Now that you've found your miners, you can move onto creating a storage deal!

## Create a deal （创建交易）

To complete this section, you need the **Data CID** you received after running `lotus client import` and the IDs of the storage providers you want to use.

1. Start the interactive deal process:（使用lotus client deal命令行来创建交易）

    ```shell
    lotus client deal
    ```

    The interactive deal assistant will now ask you some questions.

1. Specify the CID of the payload you want to backup on Filecoin. This is the CID that you got from running `lotus client import ~/5gb-filecoin-payload.bin`:（当命令行询问Data CID的时候，把之前导入的5GB大文件的CID输入到命令行）

    ```text output
    Data CID (from lotus client import): bafykbz...
    ```

1. Wait for Lotus to finish calculating the size of your payload. Lotus calculates this size by counting the individual bits in your payload to ensure that the size is accurate. （等待Lotus完成数据大小计算）

    ```text output
    .. calculating data size 
    ```

    The duration of this process depends on the size of your file and the specification of your Lotus node. In tests, Lotus took around 20 minutes file of a ~7.5GB file with a 4-core CPU and 8GB RAM. These specifications are common for most end-user laptops.（此过程的持续时间取决于文件的大小和 Lotus 节点的规范。 在测试中，Lotus 使用 4 核 CPU 和 8GB RAM 的大约 7.5GB 文件需要大约 20 分钟的时间。 这些规格对于大多数最终用户笔记本电脑来说是正常的。）

1. Enter the number of days you want to keep this file on Filecoin. The minimum is 180 days:（输入要存储的时长天数，最少180天）

    ```text output
    Deal duration (days): 180 
    ```

1. Tell Lotus whether or not this is a Filecoin Plus deal. Since you signed up to Filecoin Plus in an earlier step, select `yes` here:

    ```text output
    Make this a verified deal? (yes/no): yes
    ```

1. Enter the miner IDs from the previous section with an empty space separating the two IDs: 

    ```text output
    Miner Addresses (f0.. f0..), none to find: f01000 f01001 
    ```

1. Confirm your transaction by entering `yes`:

    ```text output
    -----
    Proposing from f136b5uqa73jni2rr745d3nek4uw6qiy6b6zmmvcq
            Balance: 2 FIL
    
    Piece size: 8GiB (Payload size: 7.445GiB)
    Duration: 7200h0m0s
    Total price: ~0 FIL (0 FIL per epoch)
    Verified: true
    
    Accept (yes/no): yes
    ```

1. Lotus will returns two **Deal CIDs**:

    ```text output
    .. executing
    Deal (f01000) CID: bafyreict2zhkbwy2arri3jgthk2jyznck47umvpqis3hc5oclvskwpteau
    Deal (f01001) CID: bafeauyreict2zhkbwy2arri3jgthk2jyznck47umvpqis3hc5oclvskwpt
    ```

1. Take a note of the **deal CIDs** `baf...`.

## Check the deal status （检查交易状态）

Once the data has been sent to the storage clients, the storage deals can take up to 24 hours to complete. You can check the progress of your deals.

将数据发送到存储客户端后，存储交易最多可能需要 24 小时才能完成。 您可以查看交易的进度。

1. List successful and pending deals by using the `lotus client list-deals` command:

    ```shell
    lotus client list-deals --show-failed
    ```

    :::danger DO NOT TURN OFF YOUR LOTUS NODE 
    Your Lotus lite-node needs to remain online until the deal state has reached `StorageDealActive`. See the [Processing states](#processing-states) table below to find out which states happen and when. 
    :::

    :::danger 不要关闭你的 LOTUS 节点
    您的 Lotus lite 节点需要保持在线状态，直到交易状态达到“StorageDealActive”。 请参阅下面的 [Processing states](#processing-states) 表以了解发生哪些状态以及何时发生。
    :::

1. You can check the progress of any data transfers by running `lotus client list-transfers`:

    ```shell
    lotus client list-transfers
    ```
    
    This command will output something like:

    ```text output
    Sending Channels
    ID                   Status   Sending To   Root Cid     Initiated?  Transferred  Voucher                                   
    1620782601911586915  Ongoing  ...KPFTTwY7  ...zyd3kapm  Y           224.1MiB     ...bqhcidjmajbelhlxfqry3d7qlu3tvar45a"}}  
    
    Receiving Channels
    ...
    ```

    If the output of `lotus client list-transfers` is empty, then your transfer has finished:

    ```shell with-output
    lotus client list-transfers
    ```
    ```
    Sending Channels
    
    
    Receiving Channels
    
    
    ```

### Deal states（交易状态）

Because of the complex nature of Lotus and the Filecoin network, deals can be in one of many different states. （由于 Lotus 和 Filecoin 网络的复杂性，交易状态有多种。）

#### Processing states（处理中）

The following table is the list of states that a deal should enter, assuming there are no errors. This list is in chronological order, from when the deal is first created to when it has completed successfully:

下表是交易应进入的状态列表，假设没有错误。 此列表按时间顺序排列，从交易首次创建到成功完成：

| State | Description |
| --- | --- |
| StorageDealUnknown | The current status of a deal is undefined or unknown. This could be because your full-node is out of sync. |
| StorageDealReserveClientFunds | The client is checking that it has enough FIL for the deal.|
| StorageDealClientFunding | The client has deposited funds into the StorageMarketActor and is waiting for the funds to appear. |
| StorageDealFundsReserved | Your FIL has been deposited into escrow and is ready to be used to pay for the deal. |
| StorageDealStartDataTransfer | The storage provider is ready to accept data from the client Lotus node. |
| StorageDealTransferring | The data is being transferred from the client Lotus node to the storage provider. |
| StorageDealCheckForAcceptance | The client is waiting for a storage provider to seal and publish a deal. |
| StorageDealProposalAccepted | The storage provider intends to accept a storage deal proposal; however, the storage provider has not made any commitment to do so at this point. |
| StorageDealAwaitingPreCommit | A deal is ready and must be pre-committed. |
| StorageDealSealing | The storage provider, is sealing data into a sector. The larger your data payload, the longer this will take. |
| StorageDealActive | The data is in a sealed sector, and the storage provider can provide the data back to you. |
| StorageDealExpired | A deal has passed its final epoch. The storage provider could still have the data available but is under no obligation to provide it to anyone. |

#### Error states（错误）

The following deal states mean there was a failure somewhere along the line, in alphabetical order: 

以下交易状态表示失败，按字母顺序排列：

| State | Description |
| --- | --- |
| StorageDealError | There has been an unforeseen error. No further updates will occur. |
| StorageDealFailing | Something has gone wrong in a deal. Once data is cleaned up, the deal will finalize. |
| StorageDealProposalNotFound | Your full-node cannot find the deal you are looking for. This could be because it doesn't exist, or your full-node is out of sync. |
| StorageDealProposalRejected | The storage provider, has chosen not to accept this deal. The storage provider may have provided a reason alongside this status message, but not always. |
| StorageDealRejecting | The storage provider has rejected the deal. This comes immediately before StorageDealProposalRejected. |
| StorageDealSlashed | The data was in a sector, and the storage provider got slashed for failing to prove that the data was available. |

#### Informational states（信息状态）

The following deal states are informational, and do not mean that a deal has failed. This list is in alphabetical order:

以下交易状态仅供参考，并不意味着交易失败。 此列表按字母顺序排列：

| State | Description |
| --- | --- |
| StorageDealAcceptWait | The storage provider is running custom decision logic to decide whether or not to accept the deal. The deal will have this status until the custom logic comes to a decision. |
| StorageDealClientTransferRestart | A storage deal data transfer from a client to a storage provider has restarted after a pause, likely caused by StorageDealProviderTransferAwaitRestart. |
| StorageDealFinalizing | All the data is within the sector, and the storage provider is performing the final checks to make sure that all the data is correct. |
| StorageDealProviderFunding | The storage provider has deposited funds into StorageMarketActor and is waiting for the funds to appear. |
| StorageDealProviderTransferAwaitRestart | The storage provider restarted while data was being transferred from the client to the storage provider. Once the storage provider is back online, it will wait for the client to resume the transfer. |
| StorageDealPublish | The deal is ready to be published on-chain. |
| StorageDealPublishing | The deal has been published but is yet to appear on-chain. |
| StorageDealReserveProviderFunds | The storage provider is checking that it has enough FIL for the deal. |
| StorageDealStaged | The deal has been published, and data is ready to be put into a sector. At this point, the storage provider has fully committed to storing your data. |
| StorageDealValidating | The storage provider is validating that the deal parameters are good for a proposal. |
| StorageDealVerifyData | All the data has been transferred, and the storage provider is now attempting to verify it against the PieceCID. |
| StorageDealWaitingForData | Either a manual transfer is occurring, or the storage provider has not received a data-transfer request from the client. |

These states come from the [Lotus project GitHub repository](https://github.com/filecoin-project/go-fil-markets/blob/master/storagemarket/dealstatus.go).

## Next steps

Now that you've added some data onto the Filecoin network [we can move into retrieving data →](./retrieve-data.md)
