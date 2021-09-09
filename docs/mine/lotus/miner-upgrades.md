---
title: 'Lotus Miner: upgrades（升级）'
description: 'This guide covers how to safely upgrade Lotus when running a miner.'
breadcrumb: 'Miner upgrades'
---

# {{ $frontmatter.title }}

{{ $frontmatter.description }}

The are two types of upgrades. The _upgrade in-place_ is the default procedure and just updates the software. The _upgrade with reset_ removes all the data and starts from scratch:

升级有两种方式。_upgrade in-place_ 是默认方式，只是更新软件。 _upgrade with reset_ 删除所有数据并从头开始：

[[TOC]]

## Upgrade in-place（直接升级）

1. Safely shutdown your Lotus Miner as explained [here](miner-lifecycle.md).
1. Shutdown any seal workers
1. Shutdown your Lotus Node (`lotus daemon stop` or `systemctl stop lotus-daemon`)
1. Pull the new version and rebuild. For more information read the [Lotus installation guide](../../get-started/lotus/installation.md) again:

```sh
export RUSTFLAGS="-C target-cpu=native -g"
export FFI_BUILD_FROM_SOURCE=1
git pull
git checkout <tag_or_branch>
git submodule update
make clean
make all
make install
```

1. Start the Lotus daemon and wait for sync:

```sh
lotus daemon
# or when using systemctl
systemctl start lotus-daemon
```

```sh
lotus sync wait
```

2. Start your miner and your workers

```sh
lotus-miner run
```

```sh
lotus-worker run
```

## Upgrade with reset（重置升级）

::: warning
This upgrade procedure should only be used as a last resort or when the chain has been upgraded and requires such action to be taken.

此升级过程仅应作为最后的手段或当链已升级并需要采取此类行动时使用。

:::

It is similar to re-installing everything from scratch, so you can follow the usual [installation](../../get-started/lotus/installation.md) and [miner-setup](miner-setup.md) guides after it. Before you do this, consider:

- [Backing up your Lotus wallets](../../get-started/lotus/send-and-receive-fil/#exporting-and-importing-addresses)
- You may want to backup your Lotus Node and Miner configurations as well.

Once you are ready, stop everything and delete the data folders (or rename them):

```sh
# Assuming you are using the default data folders
rm -rf ~/.lotus
rm -rf ~/.lotusminer
rm -rf ~/.lotusworker
```

After that Lotus applications will start from scratch.

