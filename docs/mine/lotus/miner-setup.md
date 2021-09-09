---
title: 'Lotus Miner: set up a high-performance miner（配置一个高效矿工）'
description: 'This guide describes the necessary steps to configure a Lotus miner for production.'
breadcrumb: 'Miner setup'
---

# {{ $frontmatter.title }}

{{ $frontmatter.description }}

::: warning
Mining will only work if you fully cover the [minimal hardware requirements](../hardware-requirements.md) for the network in which you will mine. As the mining process is very demanding for the machines on several aspects and relies on precise configuration, we strongly recommend Linux systems administration experience before embarking.

只有当您完全满足您将在其中进行挖矿的 [最低硬件要求](../hardware-requirements.md) 时，挖矿才能工作。 由于挖矿过程对机器的多方面要求很高，并且依赖于精确的配置，我们强烈建议在开始之前有Linux系统管理经验。

:::

[[TOC]]

## Pre-requisites（先决条件）

Before attempting to follow this guide:

- Make sure you meet the [minimal hardware requirements](../hardware-requirements.md).
- Make sure you have followed the instructions to [install the Lotus suite](../../get-started/lotus/installation.md) and make sure you have built Lotus with ["Native Filecoin FFI"](../../get-started/lotus/installation.md#native-filecoin-ffi). Once the installation is complete, `lotus`, `lotus-miner` and `lotus-worker` will be installed.
- Make sure your Lotus Node is running as the miner will communicate with it and cannot work otherwise.
- If you are in China, read the [tips for running in China](tips-running-in-china.md) page first.

::: callout
Be warned: if you decide to skip any of the sections below, things will not work! Read and tread carefully.
请注意：如果您决定跳过以下任何部分，将无法正常工作！ 请仔细阅读。
:::

## Before starting the miner（开始之前）

### Performance tweaks（性能调整）

It is recommended to set the following environment variables in your environment so that they are defined **every time any of the Lotus applications is launched** (meaning, when the daemons are started):

建议在您的环境中设置以下环境变量，以便在 ** 每次启动任何 Lotus 应用程序时**（即守护进程启动时）定义它们：

```sh
# See https://github.com/filecoin-project/bellman
export BELLMAN_CPU_UTILIZATION=0.875
```

The `BELLMAN_CPU_UTILIZATION` is an optional variable to designate a proportion of the multi-exponentiation calculation to be moved to a CPU in parallel to the GPU. This is an effort to keep all the hardware occupied. The interval must be a number between `0` and `1`. The value `0.875` is a good starting point, but you should experiment with it if you want an optimal setting. Different hardware setups will result in different values being optimal. Omitting this environment variable might also be optimal.

`BELLMAN_CPU_UTILIZATION` 是一个可选变量，用于指定将多幂运算的一部分移动到与 GPU 并行的 CPU。 这是为了保持所有硬件都被占用。 间隔必须是“0”和“1”之间的数字。 值 `0.875` 是一个很好的起点，但如果你想要一个最佳设置，你应该尝试使用它。 不同的硬件设置将导致不同的最佳值。 省略此环境变量也可能是最佳选择。

```sh
# See https://github.com/filecoin-project/rust-fil-proofs/
export FIL_PROOFS_MAXIMIZE_CACHING=1 # More speed at RAM cost (1x sector-size of RAM - 32 GB).
export FIL_PROOFS_USE_GPU_COLUMN_BUILDER=1 # precommit2 GPU acceleration
export FIL_PROOFS_USE_GPU_TREE_BUILDER=1

# The following increases speed of PreCommit1 at the cost of using a full
# CPU Core-Complex rather than a single core. Should be used with CPU affinities set!
# See https://github.com/filecoin-project/rust-fil-proofs/ and the seal workers guide.
export FIL_PROOFS_USE_MULTICORE_SDR=1
```

### Running the miner on a different machine as the Lotus Node

If you opt to run a miner on a different machine as the Lotus Node, set:

```sh
export FULLNODE_API_INFO=<api_token>:/ip4/<lotus_daemon_ip>/tcp/<lotus_daemon_port>/http
```

and **make sure the `ListenAddress` has [remote access enabled](../../build/lotus/enable-remote-api-access.md)**. Instructions on how to obtain a token are [available here](../../build/lotus/api-tokens.md).

Similarly, `lotus-miner` (as a client application to the Lotus Miner daemon), can talk to a remote miner by setting:

```sh
export MINER_API_INFO="TOKEN:/ip4/<IP>/tcp/<PORT>/http"
export MARKETS_API_INFO="TOKEN:/ip4/<IP>/tcp/<PORT>/http"
```

### Adding the necessary swap（添加必要的swap分区）

If you have only 128 GiB of RAM, you will need to make sure your system provides at least an extra 256 GiB of very fast swap (preferably NVMe SSD) or you will be unable to seal sectors:

如果您只有 128 GiB 的 RAM，则需要确保您的系统提供至少 256 GiB 的超快速swap（最好是 NVMe SSD），否则您将无法密封扇区：

```sh
sudo fallocate -l 256G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
# show current swap spaces and take note of the current highest priority
swapon --show
# append the following line to /etc/fstab (ensure highest priority) and then reboot
# /swapfile swap swap pri=50 0 0
sudo reboot
# check a 256GB swap file is automatically mounted and has the highest priority
swapon --show
```

### Creating wallets for the miner（给矿工创建钱包）

You will need at least a BLS wallet (`f3...` for mainnet) for mining. We recommend using [separate owner and worker addresses](miner-addresses.md):

每个矿工至少需要一个 BLS 钱包（`f3...` 用于主网）进行挖矿。 我们建议使用[单独的所有者和工人地址](miner-addresses.md)：

```sh
# A new BLS address to use as owner address:
lotus wallet new bls
f3...

# A new BLS address to use as worker address:
lotus wallet new bls
f3...
```

::: callout
Next make sure to [send some funds](../../get-started/lotus/send-and-receive-fil.md) to the **worker address** so that the miner setup can be completed.
接下来确保[转账一部分资金](../../get-started/lotus/send-and-receive-fil.md)到**矿工地址**，以便完成矿工设置。
:::

For additional information about the different wallets that a miner can use and how to configure them, read the [miner addresses guide](miner-addresses.md).

有关矿工可以使用的不同钱包以及如何配置它们的更多信息，请阅读 [矿工地址指南](miner-addresses.md)。 

::: tip
Safely [backup your wallets](../../get-started/lotus/send-and-receive-fil.md#exporting-and-importing-addresses)!
:::

### Downloading parameters（下载参数）

For the miner to start, it will need to read and verify the Filecoin proof parameters. These can be downloaded in advance (recommended), or otherwise the init process will. Proof parameters consist of several files, which in the case of 32 GiB sectors, total **over 100 GiB**.

要启动矿工，它需要读取并验证 Filecoin **证明参数**。 这些可以提前下载（推荐），否则 init 进程会下载。 证明参数由多个文件组成，在 32 GiB 扇区的情况下，总计**超过 100 GiB**。

We recommend setting a custom location to store parameters and proofs parent cache -created during the first run- with:

我们建议设置一个自定义位置来存储参数和证明父缓存 - 在第一次运行期间创建 - 使用：

```sh
export FIL_PROOFS_PARAMETER_CACHE=/path/to/folder/in/fast/disk
export FIL_PROOFS_PARENT_CACHE=/path/to/folder/in/fast/disk2
```

Parameters are read on every (re)start, so using disks with very fast access, like NVMe drives, will speed up miners and workers (re)boots. When the above variables are not set, things will end up in `/var/tmp/` by default, which **often lacks enough space**.

每次（重新）启动时都会读取参数，因此使用访问速度非常快的磁盘（如 NVMe 驱动器）将加快矿工启动速度。 当上面的变量没有设置时，事情会默认在`/var/tmp/`中结束，这**往往缺乏足够的空间**。

To download the parameters do:

```sh
# Use sectors supported by the Filecoin network that the miner will join and use.
# lotus-miner fetch-params <sector-size>
lotus-miner fetch-params 32GiB
lotus-miner fetch-params 64GiB
```

You can verify sectors sizes for a network in the [network dashboard](https://network.filecoin.io). The `FIL_PROOFS_*_CACHE` variables should stay defined not only for download, but also when starting the Lotus miner (or workers).

您可以在 [网络仪表板](https://network.filecoin.io) 中验证网络的扇区大小。 `FIL_PROOFS_*_CACHE` 变量不仅应该在下载时保持定义，而且在启动 Lotus 矿工时也应该保持定义。

## Checklist before launch（启动之前的检查）

To summarize, make sure that:

- The _worker address_ has some funds so that the miner can be initialized.
- The following environment variables have been defined and will be available for any Lotus miner runs:

- _worker address_ 有一些资金，以便可以初始化矿工。
- 以下环境变量已定义，可用于任何 Lotus 矿工运行：

  ```
  export LOTUS_MINER_PATH=/path/to/miner/config/storage
  export LOTUS_PATH=/path/to/lotus/node/folder # When using a local node.
  export BELLMAN_CPU_UTILIZATION=0.875 # Optimal value depends on your exact hardware.
  export FIL_PROOFS_MAXIMIZE_CACHING=1
  export FIL_PROOFS_USE_GPU_COLUMN_BUILDER=1 # When having GPU.
  export FIL_PROOFS_USE_GPU_TREE_BUILDER=1   # When having GPU.
  export FIL_PROOFS_PARAMETER_CACHE=/fast/disk/folder # > 100GiB!
  export FIL_PROOFS_PARENT_CACHE=/fast/disk/folder2   # > 50GiB!
  export TMPDIR=/fast/disk/folder3                    # Used when sealing.
  ```

- Parameters have been prefetched to the cache folders specified above.
- The system has enough swap and it is active.

- 参数已提前加载到上面指定的缓存文件夹。
- 系统有足够的swap分区并且处于活跃状态。

## Miner initialization（矿工初始化）

Before starting your miner for the first time run:

```sh
lotus-miner init --owner=<address>  --worker=<address> --no-local-storage
```

- The `--no-local-storage` flag is used so that we can later configure [specific locations for storage](custom-storage-layout.md). This is optional but recommended.
- The Lotus Miner configuration folder is created at `~/.lotusminer/` or `$LOTUS_MINER_PATH` if set.
- The difference between _owner_ and _worker_ addresses is explained in the [miner addresses guide](miner-addresses.md). As mentioned above, we recommend using two separate addresses. If the `--worker` flag is not provided, the owner address will be used. _Control addresses_ can be added later when the miner is running.

- 使用 `--no-local-storage` 参数，以便我们以后可以配置 [特定存储位置](custom-storage-layout.md)。 这是可选的，但建议使用。
- Lotus Miner 配置文件夹创建在 `~/.lotusminer/` 或 `$LOTUS_MINER_PATH`（如果设置）。
- [矿工地址指南](miner-addresses.md) 中解释了_owner_ 和_worker_ 地址之间的区别。 如上所述，我们建议使用两个单独的地址。 如果未提供`--worker` 标志，则将使用所有者地址。 _控制地址_可以稍后在矿机运行时添加。

## Connectivity to the miner（与矿工建立连接）

Before you start your miner, it is important to configure it so that it is reachable from any peer in the Filecoin network. For this, you will need a stable public IP and edit your `~/.lotusminer/config.toml` as follows:

在您启动您的矿工之前，重要的是对其进行配置，以便可以从 Filecoin 网络中的任何节点访问它。 为此，您需要一个稳定的公共 IP 并按如下方式编辑您的 `~/.lotusminer/config.toml`：

```toml
...
[Libp2p]
  ListenAddresses = ["/ip4/0.0.0.0/tcp/24001"] # choose a fixed port
  AnnounceAddresses = ["/ip4/<YOUR_PUBLIC_IP_ADDRESS>/tcp/24001"] # important!
...
```

Once you start your miner, [make sure you can connect to its public IP/port](connectivity.md).

启动矿机后，[确保您可以连接到公共 IP/端口](connectivity.md)。

## Starting the miner（启动矿工程序）

You are now ready to start your Lotus miner:

```sh
lotus-miner run
```

or if you are using the systemd service file:

```sh
systemctl start lotus-miner
```

::: warning
**Do not proceed** from here until you have verified that your miner not only is running, but also [reachable on its public IP address](connectivity.md).
:::

## Publishing the miner addresses（发布矿工地址）

Once the miner is up and running, publish your miner address (which you configured above) on the chain so that other nodes can talk to it directly and make deals:

矿工启动并运行后，在链上发布您的矿工地址（您在上面配置），以便其他节点可以直接与其对话并进行交易：

```sh
lotus-miner actor set-addrs /ip4/<YOUR_PUBLIC_IP_ADDRESS>/tcp/24001
```

## Next steps（下一步）

Your miner should now be preliminarily set up and running, but **there are still a few more recommended tasks** to be ready for prime-time:

您的矿工现在应该已经初步设置并运行，但**还有一些推荐的任务**为黄金时段做好准备：

- Set up your [custom storage layout](custom-storage-layout.md) (required if you used `--no-local-storage`).
- Edit the miner [configuration settings](miner-configuration.md) to fit your requirements.
- Learn what is a right moment to [shut down/restart your miner](miner-lifecycle.md)
- Update `ExpectedSealDuration` with the time it takes your miner to seal a sector: discover it by [running a benchmark](benchmarks.md) or by [pledging a sector](sector-pledging.md) and noting down the time.
- Configure additional [seal workers](seal-workers.md) to increase the miner's capacity to seal sectors.
- Configure a [separate address for WindowPost messages](miner-addresses.md).
- Consider [splitting markets and miners processes](split-markets-miners.md) for increased stability.
----
- 设置您的[自定义存储布局](custom-storage-layout.md)（如果您使用了`--no-local-storage`，则需要）。
- 编辑矿工 [配置设置](miner-configuration.md) 以满足您的要求。
- 了解什么是 [关闭/重启你的矿机](miner-lifecycle.md) 的合适时机
- 更新`ExpectedSealDuration` 与您的矿工密封扇区所需的时间：通过[运行基准测试](benchmarks.md) 或通过[抵押扇区](sector-pledging.md) 并记下时间来发现它。
- 配置额外的 [seal workers](seal-workers.md) 以增加矿工密封扇区的能力。
- 配置[WindowPost 消息的单独地址](miner-addresses.md)。
- 考虑[拆分市场和矿工流程](split-markets-miners.md) 以提高稳定性。