---
title: Store and retrieve（存储和取回）
description: Before you begin storing any data on the Filecoin network, you need to run through a few steps to get everything set up. This section covers getting access to a Lotus full-node, creating a Lotus lite-node on your computer, getting a FIL address, and signing up to Filecoin+. 
---

# Set up（设置）

Before you begin storing any data on the Filecoin network, you need to run through a few steps to get everything set up. This section covers getting access to a Lotus full-node, creating a Lotus lite-node on your computer, getting a FIL address, and signing up to Filecoin+. 

在开始在 Filecoin 网络上存储任何数据之前，您需要执行几个步骤来完成所有设置。 本节包括访问 Lotus 全节点、在您的计算机上创建 Lotus lite 节点、获取 FIL 地址以及注册 Filecoin+。

![A flowchart showing to steps within this set up process.](./images/set-up-process.png)

::: tip What is Lotus?
Programs that interact with the Filecoin network are called _implementations_, and [Lotus](../../get-started/lotus) is a command-line interface (CLI) implementation. There are [other implementation](../../get-started#filecoin-implementations) being created alongside Lotus, however Lotus is the only Filecoin implementation created and maintained by Protocol Labs.
:::

::: tip Lotus是什么?
[Lotus](../../get-started/lotus) 是一个命令行程序，是Filecoin 网络的实现层，Filecoin 网络还有一些[其他实现层](../../get-started#filecoin-implementations) ，但是 Lotus 是唯一由 Protocol Labs 创建和维护的 Filecoin 实现。
:::

## Things to note（需要记录的一些事情）

As you're going through this section, make a note of the following variables:

| Variable              | Description                                                  | Example                                     |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| Your Filecoin address | The public part of your Filecoin address. This address is what other users can use to send your FIL. | `f1fwavjcfb32nxbczmh3kgdxhbffqjfsfby2otloi` |

进入本章之前，请记录以下字段:

| 字段             | 描述                                                         | 示例                                        |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------- |
| 你的Filecoin地址 | Filecoin地址的公开部分，此地址可以接收其他客户转账过来的FIL。 | `f1fwavjcfb32nxbczmh3kgdxhbffqjfsfby2otloi` |

## Access a full-node （访问一个完整节点）

A Lotus full-node is a computer running the `lotus daemon`. Full-nodes are unique because they have complete access to the Filecoin blockchain. The computer specifications required to run a Lotus full-node are relatively high and might be out of reach for most end-user laptops and PCs. 

Usually, we'd have to _spin up_ a full-node, but we're going to use a Lotus full-node provided by Protocol Labs for this tutorial. This node, called `api.chain.love`, is only for practice sessions like this tutorial and should not be relied upon for any production or development purposes.

Lotus的完整节点是在一台运行 `lotus daemon`的计算机，完整节点是唯一的，因为他们可以完全访问Filecoin区块链。运行Lotus全节点所需的计算机性能要求相对较高，对于大多数最终用户的PC和笔记本电脑来说是遥不可及的。

通常，我们必须启动一个完整节点，但我们将在本教程中使用 Protocol Labs提供的Lotus全节点。这个名为"api.chain.love"的节点仅用于本教程，不能用于任何生产或开发。

## Install a lite-node（安装lite节点）

A lite-node lets your computer interact with the Filecoin network without having to run a resource-intensive full-node! Lite-nodes can do things like sign messages and talk to storage providers, but any processes that need data from the blockchain must come from a full-node. Luckily, lite-nodes automatically route any blockchain-based requests to a full-node. For this tutorial, you're going to run a Lotus lite-node on your local computer and have it connect to a full-node managed by Protocol Labs.

lite节点可以在不运行完整节点的情况下，与Filecoin网络交互。Lite节点可以进行诸如签署消息、与矿工交互等，但是任何依赖区块链数据的进程都必须基于完整节点，所以lite节点会自动通过路由访问一个完整节点来进行交互。在本教程中，您将在本地计算机上运行Lotus lite节点，并将其连接到由 Protocol Labs 管理的完整节点。

![A diagram showing how Lotus lite-nodes interact with Lotus full-nodes.](./images/lite-nodes-process-diagram.png)

To install a Lotus lite-node on your computer, you must have the tools required to _build_ a Lotus binary from the GitHub repository.

Take a look at [Lotus: install and setup](../lotus/installation.md) to learn how to install Lotus on other operating systems. 

要在本地计算机上安装Lotus lite节点，您必须必须拥有从GitHub上获取源码，并执行程序的工具。

查看 [Lotus: 安装文档](../lotus/installation.md) 以便了解如何在其他操作系统上安装Lotus。

::::: tabs

:::: tab macOS

:::warning Requirements （系统要求）
You can install Lotus on MacOS 10.11 El Capitan or higher. You must have [Homebrew](https://brew.sh/) installed.

您可以在 MacOS 10.11 El Capitan 或更高版本上安装 Lotus。 您必须安装了 [Homebrew](https://brew.sh/)。

:::

1. Add the `filecoin-project/lotus` Homebrew tap:

    ```shell
    brew tap filecoin-project/lotus
    ```

1. Install Lotus:

    ```shell
    brew install lotus
    ```

1. Lotus is now installed on your computer. 

::::
:::: tab Ubuntu

There are two simple ways to install Lotus on Ubuntu:

- [AppImage](#appimage)
- [Snap](#snap)

#### AppImage

1. Update and upgrade your system:

    ```shell
    sudo apt update -y && sudo apt upgrade -y
    ```

1. Download the latest `AppImage` file from the [Lotus GitHub releases page](https://github.com/filecoin-project/lotus/releases/):

    ```shell
    wget https://github.com/filecoin-project/lotus/releases/download/v1.10.0/Lotus-v1.10.0-x86_64.AppImage
    ```

1. Make the `AppImage` executable:

    ```shell
    chmod +x Lotus-v1.10.0-x86_64.AppImage
    ```

1. Move the `AppImage` to `/usr/local/bin` and rename it `lotus`:

    ```shell
    sudo mv Lotus-v1.10.0-x86_64.AppImage /usr/local/bin/lotus
    ```

[Head onto the next section to run your Lotus lite-node ↓](#run-a-lotus-lite-node)

#### Snap

:::warning Requirements 
You must have [Snapd](https://snapcraft.io/docs/installing-snapd) installed.
:::

1. To install Lotus using Snap, run:

    ```shell
    snap install lotus-filecoin
    ```

[Head onto the next section to run your Lotus lite-node ↓](#run-a-lotus-lite-node)


:::::

## Run a Lotus lite-node （运行Lotus lite节点）

Now that you have Lotus ready to run, you can start a Lotus lite-node on your computer and connect to the `api.chain.love` Lotus full-node! 

现在您可以开始运行Lotus，您可以在本地计算机上启动一个Lotus lite节点，并连接到"api.chain.love"的完整节点。

:::warning
Just as a reminder, `api.chain.love` is a Lotus full-node managed by Protocol Labs. It's ideal for use in this tutorial, but should not be used in a development or in a production environment.

警告：`api.chain.love` 是一个由 Protocol Labs 管理的 Lotus 完整节点。 仅供本教程中使用，不能开发或生产环境中使用。

:::

1. Open a terminal windows and run the `lotus daemon --lite` command, using `api.chain.love` as the full-node address: 

    ```shell with-output
    FULLNODE_API_INFO=wss://api.chain.love lotus daemon --lite
    ```

    ```
    ...
    2021-06-16T02:00:08.390Z        INFO    markets loggers/loggers.go:56   module ready   {"module": "storage client"}
    2021-06-16T02:00:08.392Z        INFO    markets loggers/loggers.go:56   module ready   {"module": "retrieval client"}
    2021-06-16T02:00:18.190Z        INFO    basichost       basic/natmgr.go:91      DiscoverNAT error:no NAT found
    ...
    ```

1. MacOS users may see a warning regarding Lotus. Select **Accept incoming connections** if you see a warning.
1. The Lotus daemon will continue to run. You must run further commands from a separate terminal window.

Next up is [getting a FIL address ↓](#get-a-fil-address)

## Get a FIL address

Filecoin addresses are similar to regular bank account numbers. Other users can use your address to send you FIL, and you can use your address to pay storage providers for storing and retrieving your data. 

There are two parts to a Filecoin address: the public address and the private key. You can freely share your public address with anyone, but you should never share your private key. We're not going to view any private keys in this tutorial, but it's essential to understand the difference between your public address and your private key.

1. Open a new terminal window and create an address using the `lotus wallet new` command:

    ```shell with-output
    lotus wallet new 
    ```

    ```
    f1fwavjcfb32nxbczmh3kgdxhbffqjfsfby2otloi
    ```

    Lotus outputs your public address. Public addresses always start with `f1`.

1. Make a note of this address. We'll use it in an upcoming section.

## Backup your address

Your address is made up of two parts: your _public address_ and your _private key_. The public address is what you see when you run `lotus wallet new`, and you're safe to share that address with whoever you want. Your private key, however, must be kept secret and secure. If you lose your private key, you lose access to any FIL stored in that address.

It is incredibly important that you backup your addreses. Storing a copy of your addresses on another device is a great way to ensure you don't lose access to your funds. 

1. If your public address `f1...` is still in the terminal window, copy it to your clipboard. If not, list the addresses associated with your Lotus node and copy your public address:

    ```shell with-output
    lotus wallet list
    ```

    ```
    Address                                    Balance  Nonce  Default  
    f1nau67e6k6ggdwluatfz4waexetjfrqmx6fil3nq  0 FIL    0      X  
    ```

1. Use `lotus wallet export` to export your private key, replacing `f1...` with your public key:

    ```shell
    lotus wallet export f1... > my_address.key
    ```

    This will create a new file called `my_address.key` in the current directory.

Once you have your address in a file, you can copy it to another drive, securely send it to another computer, or even print it out. It's important to keep this file safe. If anything happens to your Lotus node, you can still access your funds using this file.

## Filecoin Plus

Storage providers get paid either by receiving FIL directly from users for storing their data, winning block rewards from the network, or both!

存储提供商从客户手中接收FIL来存储他们的数据，从网络中获得挖矿奖励，或两种兼而有之，从而获得报酬。

Getting paid from users is straightforward. If Laika wants to store some data, and Albert is a storage provider, the two of them can create a deal to store Laika's data for `X` amount of time for `Y` FIL.

从客户那里获得报酬很简单。如果 Laika 想要存储一些数据，而 Albert 是一个存储提供商，那么他们两个可以创建一个交易，Albert 将 Laika 的数据进行存储，存储时长为“X”，一共支付Y个FIL。

Block rewards are randomly given to a storage provider every 30 seconds. The more data that a storage provider is _storing_, the higher their chances of winning the block reward. So if a storage provider accepts a deal from a user to store 5 GB of data, they have 5 chances to win the block reward for each 30 second round.

区块奖励每 30 秒随机分配给一个矿工。矿工 _存储_ 的数据越多，他们赢得区块奖励的几率就越高。因此，如果矿工接受用户的交易以存储 5 GB 数据，他们每 30 秒就有 5 次机会赢得区块奖励。

DataCap acts as a kind of _multiplier_ for block rewards. If a storage provider accepts a deal from a user with DataCap attached, also known as a _verified deal_ , then the Filecoin network treats that deal as though it's 10x bigger. So a 5 GB deal gives the storage miner 50 chances to win the block reward instead of the usual 5 chances. Some storage providers find DataCap so valuable that they're willing to make verified deals without charging any FIL! You can find a list of these storage providers using the [Filecoin Plus miner registry](https://plus.fil.org/miners/).

DataCap 充当一种 _multiplier_ 块奖励。如果矿工接受来自附加了 DataCap 的用户的交易，也称为 _verified deal_ ，则 Filecoin 网络将该交易视为大 10 倍。因此，5 GB 的交易为存储矿工提供了 50 次赢得区块奖励的机会，而不是通常的 5 次机会。一些矿工发现 DataCap 非常有价值，以至于他们愿意在不收取任何 FIL 的情况下进行经过验证的交易！您可以使用 [Filecoin Plus 矿工注册表](https://plus.fil.org/miners/) 找到这些矿工的列表。

### Sign up（注册）

Signing up to Filecoin Plus is easy and free!

注册一个Filecoin+很简单且免费！

:::tip
You need a GitHub account that is at least 180 days old. If you don't have a GitHub account that's old enough, [get in touch with the team on Filecoin Slack](https://filecoin.io/slack/).
:::

1. Go to [plus.fil.org](https://plus.fil.org).
1. Under **For isClients**, click **Proceed**.
1. Under **Get verified**, click **Get Verified**.
1. Click **Automatic Verification**.
1. Click **Start** next to the GitHub logo.
1. In the `Request` field, enter the public address you got from running `lotus wallet list`. This step may take a few minutes to complete. 

## Next steps

Now that we've got all the set up out of the way, we can move onto [storing data with the Filecoin network →](./store-data.md) 

