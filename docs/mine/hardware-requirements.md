---
title: 'Hardware requirements（硬件要求）'
description: 'Minimum hardware requirements for Filecoin mining.'
breadcrumb: 'Hardware requirements'
---

# {{ $frontmatter.title }}

The hardware requirements for Filecoin mining are tied to the computational resources needed to _seal_ a sector and generating regular _Proof of Spacetime_ for every sealed sector (_WindowPoSt_).

Filecoin 挖掘的硬件要求与_密封_扇区和为每个密封扇区 (_WindowPoSt_) 生成常规 _时空证明_ 所需的计算资源相关联。

These are computationally expensive operations which depend on the sector sizes used by the [Filecoin network](https://network.filecoin.io) on which the miner is running -nerpa, testnet, mainnet etc.-. For reference, the requirements listed below correspond to **32GiB sectors**, as used by mainnet and some testnets (_calibration_, _nerpa_).

这些是计算成本高昂的操作，取决于运行矿工的 [Filecoin 网络](https://network.filecoin.io) 使用的扇区大小 -nerpa、testnet、mainnet 等。 作为参考，下面列出的要求对应于主网和一些测试网（_calibration_、_nerpa_）使用的 **32GiB 扇区**。

Different Filecoin Miner implementations may distribute sealing tasks differently, for example, using additional workers apart from the Miner. The following are _general_ requirements assuming all the mining operations are performed by the same machine. Resources needed by each operation are detailed later below. For a concrete example of hardware type and usage, see the [mining architectures](mining-architectures.md).

不同的 Filecoin Miner 实现可能会以不同的方式分配密封任务，例如，使用除 Miner 之外的其他工人。 以下是假设所有挖矿操作由同一台机器执行的_一般_要求。 每个操作所需的资源将在后面详述。 有关硬件类型和用法的具体示例，请参阅[挖掘架构](mining-architectures.md)。

[[TOC]]

## General hardware requirements

### CPU

A miner will need an **8+ core CPU**.

We strongly recommend a CPU model with support for _Intel SHA Extensions_: AMD since Zen microarchitecture, or Intel since Ice Lake. Lack of SHA Extensions results in a very significant slow down.

我们强烈推荐支持 _Intel SHA Extensions_ 的 CPU 型号：自 Zen 微架构以来的 AMD，或自 Ice Lake 以来的 Intel。 缺少 SHA 扩展会导致非常显着的减速。

### RAM

**128 GiB of RAM** are needed at the very least. This **should** be complemented with **256 GiB of swap on a very fast NVMe SSD** storage medium.

### GPU

A powerful GPU is **recommended** as it can significantly speed up SNARK computations. See below for operations which can take advantange of GPU presence.

**推荐**强大的 GPU，因为它可以显着加快 SNARK 计算。 有关可以利用 GPU 存在的优势的操作，请参见下文。

The [authoritative list of supported GPUs](https://github.com/filecoin-project/bellman#supported--tested-cards) is in the [Bellman repository](https://github.com/filecoin-project/bellman#supported--tested-cards).

Additional GPU models need to be enabled manually ([instructions for Lotus](lotus/gpus.md)).

::: warning
Mixing AMD and Nvidia GPUs in the same machine is known to cause issues with OpenCL and should be avoided.

已知在同一台机器中混合使用 AMD 和 Nvidia GPU 会导致 OpenCL 出现问题，应该避免。
:::

### Disk

Performance of Miner operations can be heavily affected by slow disks. For example, a 320GiB expands to ~480GiB during the sealing process. Filecoin network parameters are over 100GiB and need to be read and verified during Miner start. As mentioned above, lack of RAM needs to be addressed with a fast swap drive or file.

慢速磁盘会严重影响 Miner 操作的性能。 例如，320GiB 在密封过程中扩展到 ~480GiB。 Filecoin 网络参数超过 100GiB，需要在 Miner 启动时读取和验证。 如上所述，内存不足需要通过快速交换驱动器或文件来解决。

For this reasons, a minimal amount of 1TiB NVMe-based disk space for cache storage is recommended. This disk should be used to store data during the sealing process, to cache Filecoin parameters and serve as general temporal storage location.

出于这个原因，建议使用最少 1TiB 的基于 NVMe 的磁盘空间用于缓存存储。 该磁盘应用于在密封过程中存储数据，缓存 Filecoin 参数并用作一般临时存储位置。

Additional hard drives for the final storage of "sealed sectors", the Lotus chain etc. will be needed as well.

还需要用于最终存储“密封扇区”、Lotus 链等的额外硬盘驱动器。

## Specific operation requirements（具体操作要求）

As mentioned, the Miners have to perform operations of different nature which differ in how they use CPU and GPU resources. The following table shows how resources are utilized depending on the sealing phase or proof calculation being run:

如前所述，矿工必须执行不同性质的操作，它们使用 CPU 和 GPU 资源的方式不同。 下表显示了如何根据密封阶段或正在运行的证明计算来利用资源：

| Operation                               | CPU used                       | GPU used | Memory (32Gib sectors) | Notes                                                        |
| --------------------------------------- | ------------------------------ | -------- | ---------------------- | ------------------------------------------------------------ |
| Sealing（密封）: preCommit phase 1      | Yes (1 core or 1 core-complex) | No       | 128GiB                 | PoRep SDR encoding. Not amenable to parallelization.（不适合并行化） Core usage depends on value of [`FIL_PROOFS_USE_MULTICORE_SDR`](https://github.com/filecoin-project/rust-fil-proofs/).（核心使用取决于FIL_PROOFS_USE_MULTICORE_SDR） Also see the [Lotus seal workers guide](lotus/seal-workers.md). |
| Sealing（密封）: preCommit phase 2      | Yes (when no GPU, all cores)   | Yes      | 128GiB                 | Merkle tree generation using the Poseidon hashing algorithm. Slower with just CPUs.（使用波塞冬Hash算法生成默克尔根树，仅使用CPU会比较慢） |
| Sealing（密封）: commit phase 1         | Yes (all cores)                | No       | -                      |                                                              |
| Sealing（密封）: commit phase 2         | Yes (when no GPU, all cores)   | Yes      | ~ 192GiB               | Slow with just CPUs.（仅使用CPU会比较慢）                    |
| Unsealing（开封）                       | Yes (1 core)                   | No       | 128GiB                 |                                                              |
| Proving _WindowPoSt_（提供时空证明）    | Yes (all cores, when no GPU)   | Yes      | -                      | _WindowPoSts_ must be submitted in 30 minute windows. When no GPU available, the more CPU cores the faster（时空证明必须在30分钟内提交。当没有GPU可用时，CPU核数越多速度越快） |
| Proving _WinningPoSt_（提供开采权证明） | Yes                            | No       | -                      | _WinningPoSt_ is a less intensive computation. Must be completed in a 25 seconds window.（_WinningPoSt_ 是一种不太密集的计算。 必须在 25 秒内完成。） |

Note that the [Lotus](lotus/README.md) implementation allows to configure and delegate specific sealing phases to [Lotus workers](lotus/seal-workers.md).

## About hardware requirements

The above requirements will not increase in the presumable future, and money spent on hardware should provide users with many years of reliable service, paying for themselves several times over.
