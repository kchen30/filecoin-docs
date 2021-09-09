---
title: Lotus Miner
description: The Lotus Miner is a Filecoin miner implementation written by Protocol Labs
---

# Lotus Miner（矿工）

{{ $frontmatter.description }}.

This sections contains guides to setup and run succesful mining operations using Lotus and should be approached by **advanced users only**, familiar with [how Filecoin works](../../about-filecoin/how-filecoin-works.md), [how mining works](../how-mining-works.md) and the operation of the [Lotus Node](../../store/lotus/README.md).

本节包含使用 Lotus 设置和运行成功挖矿操作的指南，**仅限高级用户**，熟悉 [Filecoin 的工作原理](../../about-filecoin/how-filecoin-works. md)、[挖矿的工作原理](../how-mining-works.md) 和 [Lotus Node](../../store/lotus/README.md) 的操作。

::: warning
Lotus Mining for _mainnet_ has stringent minimal **[hardware requirements](../hardware-requirements.md)**. Do not attempt this installation if your computer does not meet the minimum requirements.
Lotus Mining for _主网_ 具有严格的最低 **[硬件要求](../hardware-requirements.md)**。 如果您的计算机不满足最低要求，请勿尝试此安装。
:::

## Getting started with Lotus Miner（开始挖矿）

The following guides are essential starting points for those willing to launch a Lotus miner:
以上都是废话。

- The miner installation is covered in the [Installation guide](../../get-started/lotus/installation.md). Once the installation is complete, the Lotus node, Lotus miner, and Lotus worker applications should all be installed.（矿工安装方法在教程中有介绍，安装完成后，Lotus 节点、Lotus miner 和 Lotus worker 应用程序都应该安装完毕。）

- The [Miner setup](miner-setup.md) covers all the details to configure your miner to achieve the maximum performance and avoid common pitfalls.（涵盖了配置您的矿机以实现最大性能并避免常见陷阱的所有细节。）
- The [Configuration reference](miner-configuration.md) explains what the different miner configuration options mean.（解释了不同矿工配置选项的含义。）
- The [Seal workers](seal-workers.md) guide covers how to run additional seal workers co-located or not with the Lotus Miner.（密封workers指南介绍了如何脱离Lotus Miner的情况下，进行密封文件）

We nevertheless recommend careful reading of every existing section and gaining as much background as possible before proceeding with a Lotus miner deployment.
尽管如此，我们还是建议在继续部署 Lotus miner 之前仔细阅读每个现有部分并获得尽可能多的背景知识。
