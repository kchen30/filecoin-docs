---
title: 'Slashing（惩罚）'
description: 'Slashing penalizes miners that either fail to provide reliable uptime or act maliciously against the network.'
breadcrumb: 'Slashing'
---

# {{ $frontmatter.title }}

{{ $frontmatter.description }}

In Filecoin, miners are succeptible to two different kinds of slashing: _storage fault slashing_, and _consensus fault slashing_.

在 Filecoin 中，矿工容易受到两种不同的惩罚：_存储故障惩罚_和_共识错误惩罚_。

## Storage fault slashing（存储故障惩罚）

This term encompasses a broader set of penalties which are to be paid by miners if they fail to provide sector reliability or decide to voluntarily exit the network. These include:

该术语包含一系列更广泛的罚款，如果矿工未能提供可靠的服务或决定自愿退出网络，则将由矿工支付。 这些包括：

- **Fault fees**: a penalty that a miner incurs for each day a miner's sector is offline (fails to submit Proofs-of-Spacetime to the chain). Fault fees continue until the associated wallet is empty and the miner is removed from the network. In the case of a faulted sector, there will be an additional sector penalty added immediately following the fault fee.
- **Sector penalties**: a penalty that a miner incurs for a faulted sector that was not declared faulted before a _WindowPoSt_ check occurs. The sector will pay a fault fee after a Sector Penalty once the fault is detected.
- **Termination fees** is a penalty that a miner incurs when a sector is voluntarily or involuntarily terminated and is removed from the network.

- **故障费用**：矿工在矿工部门离线的每一天（未能向链提交时空证明）所产生的罚款。 故障费用会一直持续，直到相关的钱包为空并且矿工从网络中移除。 在故障扇区的情况下，将在故障费用之后立即添加额外的扇区罚款。
- **扇区惩罚**：矿工因在_WindowPoSt_检查发生之前未宣布故障的故障扇区而招致的惩罚。 一旦检测到故障，该部门将在 扇区惩罚 后支付故障费用。
- **终止费**是矿工在一个部门自愿或非自愿终止并从网络中删除时产生的罚款。

## Consensus fault slashing（共识错误惩罚）

This penalty is incurred when committing consensus faults. This penalty is applied to miners that have acted maliciously against the network's consensus functionality.

这种惩罚是在提交共识错误时产生的。 这种惩罚适用于对网络共识功能采取恶意行为的矿工。
