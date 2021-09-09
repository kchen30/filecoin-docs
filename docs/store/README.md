---
title: 'Store data on Filecoin'
description: 'Start storing data on the Filecoin network.'
breadcrumb: 'Store'
---

# Store data on Filecoin（Filecoin存储数据）

Storing data on Filecoin lets users harness the power of a distributed network and an open market served by thousands of different storage providers or miners.

Filecoin 网络由数千个不同存储提供商或矿工组成，在Filecoin上存储数据可以让用户利用这种开放市场的力量。

:::tip Looking for a quick start guide?
Storing and retrieving data on the Filecoin network is a bit different to other services or APIs you may have used. That's why we put the [Store & retrieve guide together →](../get-started/store-and-retrieve)
:::

## Get started（开始）

Users can use the following software solutions to store data on the Filecoin network:

用户可以使用以下解决方案在 Filecoin 网络上存储数据：

### Apps

The following applications allow you to store data on the Filecoin network with no technical setup:

- [ChainSafe Files](https://files.chainsafe.io/) provides decentralized cloud storage with end-to-end encryption in a Dropbox-like interface. Check out the [blog post by ChainSafe](https://medium.com/chainsafe-systems/introducing-chainsafe-files-3eedabdec922) for a quick overview of the service.
- [Estuary](https://estuary.tech) allows uploading and storing content on the Filecoin network directly from your browser, command line, and API.
- [Space Storage](https://space.storage/) by Fleek is an open-source, user-controlled, encrypted file storage and sharing platform using IPFS and Filecoin, tied to Ethereum accounts or common web OAuth options.
- [Web3.Storage](https://web3.storage) is an IPFS pinning service and Filecoin storage platform built-in-one. Upload files to Web3.Storage and access them instantly, safe in the knowledge that your data is securely backed up using the Filecoin network.

以下应用程序允许您无需技术支持的情况下将数据存储在 Filecoin 网络上：

- [ChainSafe Files](https://files.chainsafe.io/) 一个界面类似 Dropbox 应用程序，提供具有端到端加密的分散式云存储。查看 [ChainSafe 的博客文章](https://medium.com/chainsafe-systems/introducing-chainsafe-files-3eedabdec922) 以快速了解该服务。
- [Estuary](https://estuary.tech) 允许直接从浏览器、命令行和 API 上传和存储 Filecoin 网络上的内容。
- Fleek 的 [Space Storage](https://space.storage/) 是一个开源的、用户控制的、加密的文件存储和共享平台，使用 IPFS 和 Filecoin，绑定到以太坊帐户或常见的 Web OAuth 账户。
- [Web3.Storage](https://web3.storage) 是IPFS pinning 服务和Filecoin 存储平台合二为一。将文件上传到 Web3.Storage 并立即访问它们，因为您的数据已使用 Filecoin 网络进行安全备份。

### Filecoin Node Client（Filecoin节点客户端）

- [Lotus](lotus/README.md) imports data and performs deals on the chain using its daemon and CLI. Lotus users get full control of the deals, the chosen miners, and the wallets used to pay. Make sure you are familiar with [Lotus](../get-started/lotus/README.md) and have it [installed and running](../get-started/lotus/installation.md).

- [Lotus](lotus/README.md) 使用常驻进程和 CLI 导入数据并在链上执行交易。 Lotus 用户可以完全控制交易、选择的矿工和用于支付的钱包。 确保您熟悉 [Lotus](../get-started/lotus/README.md) 并[安装并运行](../get-started/lotus/installation.md)。

### Digital Preservation

- [Starling](starling.md) uses Lotus to simplify decentralized storage for digital _preservationists_ and archivists.

### Video

- [File.video](https://file.video/) and [Voodfy](https://beta.voodfy.com/) both provide decentralized video hosting using decentralized transcoding using the [LivePeer](https://livepeer.org/) network.

## Additional resources（附加资源）

There are additional storage solutions that you should not miss. While they have a focus on developers, some of them have simple CLI interfaces that simplify their usage:

- [Fleek](https://fleek.co) provides easy hosting and storage solutions on IPFS and their [SpaceDaemon](https://docs.fleek.co/space-daemon/overview/) and upcoming Space SDK use Filecoin.
- [Pinata](https://pinata.cloud/) is an IPFS pinning service that will soon include Filecoin in its portfolio.
- [Powergate](https://github.com/textileio/powergate) a multitiered storage solution that stores data with [IPFS and Filecoin](../about-filecoin/ipfs-and-filecoin.md). It can be used [self-hosted](../build/powergate.md) or [hosted](../build/hosted-powergate.md).
- [Textile Buckets](../build/textile-buckets.md) provide S3-like storage using IPFS with Filecoin-backed archival and has an easy-to-use CLI.

