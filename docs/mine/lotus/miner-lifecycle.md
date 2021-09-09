---
title: 'Lotus Miner: lifecycle（生命周期）'
description: 'How to safely perform maintenance on a Lotus miner.'
breadcrumb: Lifecycle
---

# {{ $frontmatter.title }}

{{ $frontmatter.description }}

These operations are normally related to maintenances and upgrades. Given that miners are expected to submit proofs to the chain in a continuous fashion while running long and expensive operations, it is important that operators become familiar with how to manage some of the events in the miner lifecycle so that they can be performed with the maximum guarantees.

这些操作通常与维护和升级有关。 鉴于矿工在运行时间长且成本高昂的操作时需要以连续的方式向链提交证明，因此运营商必须熟悉如何管理矿工生命周期中的一些事件，以便保证最大程度地执行。

[[TOC]]

## Safely restarting the miner daemon（安全重启miner）

The process of shutting down a miner and starting it again is complicated. Several factors need to be taken into account to be able to do it with all the guarantees:

关闭矿机并重新启动它的过程很复杂。 需要考虑几个因素才能在所有保证下做到这一点：

- How long the miner plans to be offline.
- The existence and distribution of proving deadlines for the miner.
- The presence of open payment channels and ongoing retrieval deals.
- The occurrence of ongoing sealing operations.

----

- 矿工计划离线多长时间。
- 矿工证明截止日期的存在和分布。
- 存在开放的支付渠道和正在进行的检索交易。
- 正在进行的密封操作的发生。

### Reducing the time offline（减少离线时间）

Given the need to continuously send proofs to the network, the miner should be offline as little as possible. _Offline-time_ includes the time it takes for the computer to restart the miner daemon fully. For these reasons, we recommend you follow these steps:

鉴于需要不断地向网络发送证明，矿工应该尽可能少地离线。 _Offline-time_ 包括计算机完全重启矿工守护进程所需的时间。 出于这些原因，我们建议您执行以下步骤：

1. Rebuild, install any upgrades before restarting the Lotus Miner process.
1. Ensure the proof parameters are on a fast storage drive like an NVMe drive or SSD. These are the proof parameters that are downloaded the first time the miner is started, and are saved to `var/tmp/filecoin-proof-parameters`, or `$FIL_PROOFS_PARAMETER_CACHE` if the environment variable is defined.

----

1. 在重新启动 Lotus Miner 进程之前重建、安装任何升级。
1. 确保证明参数位于快速存储驱动器（如 NVMe 驱动器或 SSD）上。 这些是矿工第一次启动时下载的证明参数，如果定义了环境变量，则保存到 `var/tmp/filecoin-proof-parameters` 或 `$FIL_PROOFS_PARAMETER_CACHE`。

### Ensuring proofs for the current deadline have been sent（确保已发送当前时间的时空证明）

Shutting down your miner while there are still pending operations could get your [miner slashed](../slashing.md). Check that there are no pending operations by running `lotus-miner proving info`. If any deadline shows a _block height_ in the past, do not restart yet.

在仍有待处理的操作时关闭您的矿机可能会使您的 [miner slashed](../slashing.md)。 通过运行“lotus-miner proving info”来检查没有挂起的操作。 如果任何截止日期显示 _block height_ 过去，请不要重新启动。

In the following example, `Deadline Open` is 454, which is earlier than `Current Epoch` of 500. This miner should not be shut down or restarted:

在以下示例中，`Deadline Open` 为 454，早于 `Current Epoch` 的 500。不应关闭或重新启动该矿机：

```shell
$ lotus-miner proving info

Miner: t01001
Current Epoch:           500
Proving Period Boundary: 154
Proving Period Start:    154 (2h53m0s ago)
Next Period Start:       3034 (in 21h7m0s)
Faults:      768 (100.00%)
Recovering:  768
Deadline Index:       5
Deadline Sectors:     0
Deadline Open:        454 (23m0s ago)
Deadline Close:       514 (in 7m0s)
Deadline Challenge:   434 (33m0s ago)
Deadline FaultCutoff: 384 (58m0s ago)
```

In this next example, the miner can be safely restarted because no `Deadlines` are earlier than `Current Epoch`. You have about 45 minutes before the miner must be back online to declare faults. This is known as the `Deadline FaultCutoff`. If the miner has no faults, you have about an hour.

在下一个示例中，矿工可以安全地重新启动，因为没有“截止日期”早于“当前时期”。 您有大约 45 分钟的时间，矿工必须重新上线以宣布故障。 这被称为“截止日期故障截止”。 如果矿工没有故障，你大约有一个小时。

```shell
$ lotus-miner proving info

Miner: t01000
Current Epoch:           497
Proving Period Boundary: 658
Proving Period Start:    658 (in 1h20m30s)
Next Period Start:       3538 (in 25h20m30s)
Faults:      0 (0.00%)
Recovering:  0
Deadline Index:       0
Deadline Sectors:     768
Deadline Open:        658 (in 1h20m30s)
Deadline Close:       718 (in 1h50m30s)
Deadline Challenge:   638 (in 1h10m30s)
Deadline FaultCutoff: 588 (in 45m30s)
```

The `proving info` examples above show information for the current proving window and deadlines. If you wish to see any upcoming deadlines you can use:

上面的“证明信息”示例显示了当前证明窗口和截止日期的信息。 如果您想查看任何即将到来的截止日期，您可以使用：

```shell
$ lotus-miner proving deadlines
```

Every row corresponds to a deadline (a period of 30 minutes covering 24 hours). The current one is marked. This is sometimes useful to find a time of day in which the miner does not have to submit any proofs to the chain.

每行对应一个截止日期（30 分钟，涵盖 24 小时）。 当前一个被标记。 这有时有助于找到矿工不必向链提交任何证明的一天中的某个时间。

### Checking and temporally disabling deals（检查和暂时禁止交易）

Before stopping the miner, check the state of your deals to make sure the miner is not receiving data or retrieving data for a client:

```shell
lotus-miner storage-deals list
lotus-miner retrieval-deals list
lotus-miner data-transfers list
```

To prevent new deals from coming in while you wait to finish work for the current deadline, you can disable storage and retrieval deals. This ensures that the miner does not find itself in the middle of a new deal when shutting down:

```shell
lotus-miner storage-deals selection reject --online --offline
lotus-miner retrieval-deals selection reject --online --offline
```

After the miner has finished rebooting, the deals can be reset with:

```shell
lotus-miner storage-deals selection reset
lotus-miner retrieval-deals selection reset
```

### Checking ongoing seal operations（检查正在进行密封操作）

To get an overview of your current sectors and states, run:

```shell
lotus-miner sectors list
```

Any ongoing sealing operation will be restarted from the last checkpoint, and usually corresponds to the start of the current sealing phase. Given that sealing is time consuming, you should wait for some stages that are close to finishing before restarting your miner.

### Restarting the miner（重启）

With all the considerations above you can decide on the best moment to shutdown your miner:

```shell
lotus-miner stop
# When using systemd
# systemctl stop lotus-miner
```

You can restart the miner as soon as you wish. Workers do not need to be restarted as they will reconnect to the miner automatically when it comes back up. However, if you are upgrading your node at the same time as shutting down your miner, you will need to restart the machine.

## Restarting workers（重启workers）

Lotus [seal workers](seal-workers.md) can be restarted any time, but if they are in the middle of one of the sealing steps, then the operation will start again (from the last checkpoint).

Lotus [seal workers](seal-workers.md) 可以随时重启，但是如果正在密封，那么下次启动要重新开始（从最后一个检查点开始）。

::: warning
There is a maximum of three attempts to complete the _precommit2_ operation before sealing is fully started from scratch (_pre-commit1_ phase).

在完全从头开始密封之前（_pre-commit1_ 阶段），最多有 3 次尝试来完成 _precommit2_ 操作。
:::

## Changing storage locations（改变存储位置）

If you wish to change the location of your miner-related storage to a different path, for the miner or the seal workers, make sure that the Lotus miner and any seal workers are aware of the new location.

```sh
lotus-miner storage list
```

The above command will give you an overview of [storage locations known to the miner](custom-storage-layout.md). This information is stored in `~/.lotusminer/storage.json` (or `$LOTUS_MINER_PATH/storage.json` if defined). Lotus seal workers store all the data in the `~/.lotusworker` folder (or `$LOTUS_WORKER_PATH` if defined).

If you wish to change any of the storage locations of the Lotus miner, follow these steps:

1. Set your miner to reject any new storage and retrieval deals so the storage is not modified during the copy.
2. Copy the data **as is**, keeping it in the original location, while the miner is running. Since this usually involves moving large amounts of storage, it will take time. In the meatime our miner will keep tending to its tasks.
3. Once the data is copied, [stop the miner](#safely-restarting-the-miner-daemon), following the recommendations above.
4. Edit `storage.json` with the new location for the data and make the old data unavailable for the miner (by renaming, or unmounting) to make sure it is not going to be used anymore when you start it.
5. Start the miner.
6. Verify that things are working correctly. If so, you can discard the old copy.

If you wish to expand your storage, while keeping the current, you can always [add additional storage locations to a Lotus miner](custom-storage-layout.md) with `lotus storage attach` (see `--help`).

If you wish to change the storage location for any of the lotus workers:

1. Stop the Lotus Worker.
2. Move the data to the new location.
3. Set `$LOTUS_WORKER_PATH` accordingly.
4. Start the worker again.

Any operations that the worker was performing before stopping will be restarted from the last checkpoint (a point in which they can be restarted, which may correspond to the start of the current sealing phase).

:::warning
Moving data between different workers is not currently supported. Moving the worker storage folder to a different worker machine will not work as the miner expects the ongoing sealing operations to be completed by the worker they were assigned to in the first place.
:::

## Using a different Lotus Node

If you are planning to run maintenance on the Lotus node used by the miner, or if you need to failover to a different Lotus node because the current one does not work, follow these steps:

1. [Stop the miner](#safely-restarting-the-miner-daemon)
2. Set the `FULLNODE_API_INFO` environment variable to the location of the new node:

```sh
export FULLNODE_API_INFO=<api_token>:/ip4/<lotus_daemon_ip>/tcp/<lotus_daemon_port>/http
```

Follow these steps to learn [how to obtain a token](../../build/lotus/api-tokens.md).

3. If you have not exported your wallets yet, export them now from the old node and re-import them to the new Lotus node.
4. Start the miner. It should now communicate with the new Lotus Node and, since it has the same wallets as the older one, it should be able to perform the necessary operations on behalf of the miner.

::: callout
Make sure your new Lotus Node is fully synced.
:::
