---
title: Store and retrieve（存储和检索）
description: The process of storing and retrieving data using the Filecoin network is slightly different from how most storage platforms work. This tutorial walks you through the whole end-to-end process of keeping your data and then getting it back when you need it! We're going to use Lotus and the command-line in this tutorial.
---

# Store and retrieve （存储和检索）

The process of storing and retrieving data using the Filecoin network is slightly different from how most storage platforms work. This tutorial walks you through the whole end-to-end process of keeping your data and then getting it back when you need it! We're going to use Lotus and the command-line in this tutorial.

Filecoin存储和检索数据的过程，和大多数存储平台略有不同。本教程将引导您了解存储和检索数据整个端到端的过程！我们将在本教程中使用Lotus命令行。

The process is split into three main parts: the set-up, storing your data and retrieving your data. Each section has several sub-processes that we need to follow.

该过程分为三个部分：设置、存储数据、检索数据。每个部分都有几个需要我们遵循的子过程。

![End-to-end process for storing and retrieving data.](./images/end-to-end-process.png)

| Section | Sub-tasks |
| --- | --- |
| Set up | 1. Get access to a Lotus full-node.<br> 2. Start a Lotus lite-node on your local computer.<br> 3. Get a FIL address.<br> 4. Sign up for Filecoin Plus. |
| Store data | 1. Package your data.<br> 2. Import your data into Lotus.<br> 3. Find a storage provider through the Filecoin Plus miner registry.<br> 4. Create a storage deal.<br> 5. Wait for the deal to complete. |
| Retrieve data | 1. Create a retrieval deal.<br> 2. Download your data.|

| 部分 | 子过程 |
| --- | --- |
| 设置 | 1. 找到一个Lotus全节点<br> 2. 在你本机电脑启动一个Lotus lite节点<br> 3. 获取一个FIL地址.<br> 4. 注册Filecoin+. |
| 存储数据 | 1. 封装你的数据 <br> 2. 导入数据到Lotus <br> 3. 通过Filecoin Plus矿工列表找到一个存储提供商 <br> 4. 创建一个存储交易 <br> 5. 等待交易完成 |
| 检索数据 | 1. 创建一个检索交易.<br> 2. 下载你的数据 |

It will take about an hour to complete this tutorial. While there aren't too many steps involved, there's a bit of waiting around for the network to process your requests.

完成本教程大约需要一个小时。 虽然涉及的步骤不多，但需要等待网络处理您的请求。

:::warning Running on mainnet
This tutorial uses the Filecoin mainnet. Everything you'll see over the next hour is happening on a production network with other users storing and retrieving data. But don't worry, this tutorial won't cost you anything! It's just important to know that you'll be dealing with real storage providers, real data, and real transactions.
:::

:::warning 在主网运行
本教程使用Filecoin主网，您将在接下来一个小时内看到一切都发生在生产网络上，其他用户存储和检索数据。不过别担心，本教程不会花费您任何费用！重要的是要知道，您将与真实的存储提供者、真实的数据和真实的交易打交道。
:::

## Prerequisites（先决条件）

If you are using macOS you must have [Homebrew](https://brew.sh) installed. If you are using Linux you must have [Snapd](https://snapcraft.io/docs/installing-snapd) installed.

如果您使用的是mac，您必须保证已安装 [Homebrew](https://brew.sh) 。如果您使用的是Linux，您必须保证已安装 [Snapd](https://snapcraft.io/docs/installing-snapd)。

## Take notes（做笔记）

There are a few things to remember throughout this tutorial, such as Miner IDs and addresses. There is a table at the end of each section showing the information you should record:

| Variable | Description | Example |
| --- | --- | --- |
| Miner ID | The unique identifier for each storage provider. | `f01000`

The above table is an example of what you will see throughout the tutorial; you don't have to copy it down.

本教程需要记住一些事情，例如：矿工ID和地址。每个部分结尾都有一个表格，显示您应该记录的信心：

| 字段    | 描述                   | 示例     |
| ------- | ---------------------- | -------- |
| 矿工 ID | 每个数据提供者的唯一ID | `f01000` |

上面的表格是您将在整个教程中看到的示例，你不必复制它。

## Terms and phrases（术语和短语）

This tutorial contains some words and phrases that you might not be familiar with. Refer back to this table if you encounter something you don't understand:

本教程包含一些术语和短语。如果您遇到不理解的内容，请查阅以下表格：

| Word | Definition |
| --- | --- |
| Address | A string of letters and numbers that other users can send FIL to. |
| [Block explorer](../../get-started/explore-the-network/#block-explorers) | A service, usually a website, that lets you view details of a blockchain such as transactions, deals, and addresses. |
| Deal | An agreement between two computers about what to do with some data. |
| FIL | The shorthand representation of the filecoin cryptocurrency. For example: _We charge 0.5 FIL per GiB._ |
| Filecoin (upper-case `F`) | The network that transactions and storage deals take place on. For example: _Museums can use the Filecoin network to store their digital archives._ |
| filecoin (lower-case `f`) | The cryptocurrency that the Filecoin network runs on. For example: _You can use filecoin to pay for your transactions._ |
| Miner | An alternate name for a _storage provider_. |
| Private key | A string of letters and numbers that programs use to interact with the Filecoin network. Keep your private key safe, and don't share it with anyone. |
| Storage deal | An agreement between a storage provider and a client about what data to store, how long for, and how much the storage provider can charge for storage. |
| Retrieval deal | An agreement between a storage provider and a client about how much the storage provider can charge to send data to a client. |
| Storage client | The user that wants to store something on the Filecoin network. In this tutorial, _you_ are the storage client. |
| Storage provider | A computer on the Filecoin network offering storage space to other users who want to store data. Storage providers are sometimes called _miners_. |
| Wallet | A collection of addresses. Think of each wallet as a folder and each address as a single file in that folder. |

| 词语 | 定义 |
| --- | --- |
| 地址 | 其他用户可以发送FIL的地址，由一串字母和数字组成。 |
| [区块链浏览器](../../get-started/explore-the-network/#block-explorers) | 一种服务，通常是网站，可让您查看区块链的详细信息，例如transactions, deals和地址。 |
| 交易(Deal) | 两台计算机之间交换数据的协议 |
| FIL | Filecoin加密货币的缩写。例如：我们每GB收取0.5个FIL。 |
| Filecoin (大写 `F`) | 存储和交易发生的网络。例如：博物馆可以用Filecoin网络来存储他们的数字档案。 |
| filecoin (小写 `f`) | 基于Filecoin网络上运行的加密数字货币。例如：我们使用filecoin支付你的交易。 |
| 矿工 | 存储提供者的别名。 |
| 私钥 | 一串由字母和数组组成的字符串，用于与Filecoin网络交互。妥善保管您的私钥，不要与任何人分享。 |
| 存储交易（Storage deal） | 矿工和客户之间如何存储数据的协议。包括存储什么数据，存储时间，以及收取多少费用。 |
| 检索交易（Retrieval deal） | 矿工和客户之间关于检索和发送数据收取多少费用的交易。 |
| 存储客户端（Storage client） | 想要在Filecoin网络上存储内容的用户。在本教程中，您是存储客户端。 |
| 存储提供者/矿工（Storage provider） | 一台Filecoin网络上的计算机，为想要存储数据的客户提供存储空间。 |
| 钱包（Wallet） | 地址的集合。钱包可以视为一个文件夹，每个地址在文件夹中对应单个文件。 |

## Next steps（下一步）

Before we can manage data on the Filecoin network, we need to set up things like a Lotus node and a Filecoin wallet. [Head to the set-up section to start gathering your resources →](set-up)

在我们可以管理Filecoin网络上的数据之前，我们需要设置诸如Lotus节点和Filecoin钱包之类的东西. [前往设置相关章节开始收集您的资源 →](set-up)

