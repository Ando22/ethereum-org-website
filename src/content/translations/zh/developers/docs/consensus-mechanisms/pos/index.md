---
title: 权益证明机制(PoS)
description: 权益证明共识算法以及其在以太坊中作用的解释。
lang: zh
sidebar: true
incomplete: true
---

以太坊正在将其共识机制从[工作量证明(PoW)](/developers/docs/consensus-mechanisms/pow/)转变为权益证明(PoS)。 这一直在计划之中，因为这是社区策略中通过 [Eth2 升级](/eth2/)扩展以太坊的关键部分。 然而正确实现 PoS 是一项巨大的技术挑战,，并不像使用 PoW 在整个网络中达成共识那样简单。

## 前置要求 {#prerequisites}

为了更好地理解本文，我们建议你首先阅读以下内容 [共识机制](/developers/docs/consensus-mechanisms/)。

## 什么是权益证明？ {#what-is-pos}

权益证明是一种区块链网络达成共识的[共识机制](/developers/docs/consensus-mechanisms/)。

这将要求用户抵押他们的以太币从而成为网络中合法的验证者。 验证者有着与矿工在 [工作量证明（pow）](/developers/docs/consensus-mechanisms/pow/)中相同的职责：将交易排序和创建新的区块，以便让所有的节点就网路状态达成一致。

权益证明相较于工作量证明系统有许多改进：

- 提高能效——您不需要大量能源去挖掘区块
- 门槛降低，硬件要求减少——您不需要优秀的硬件从而获得建立新区块的机会
- 更强的去中心化——权益证明可以在网络中提供更多的节点。
- 更有力地支持[分片链](/eth2/shard-chains/)，这是以太坊网络扩展的关键升级

## 权益证明、权益质押和验证者 {#pos-staking-validators}

权益证明是一种用于激励验证者接受更多质押的基本机制。 就以太币而言，用户需要质押 32ETH 来获得作为验证者的资格。 验证者被随机选择去创建区块，并且负责检查和确认那些不是由他们创造的区块。 一个用户的权益也被用于激励良好的验证者行为的一种方式。 例如，用户可能会因为离线（验证失败）而损失一部分权益， 或因故意勾结而损失他们的全部权益。

## 以太坊权益证明是如何运作的？ {#how-does-pos-work}

与工作量证明不同的是，验证者不需要使用大量的算力，因为它们是随机选择的，相互间没有竞争。 他们不需要开采区块，他们只需要在被选中的时候创建区块并且在没有被选中的时候验证他人提交的区块。 此验证被称为证明。 你可以认为证明是说“这个块在我看来没问题”。 验证者因提出新区块和证明他们已经看到的区块而获得奖励。

如果你为恶意区块提供证明，你就会失去你的股权。

### 信标链 {#the-beacon-chain}

当以太坊用权益证明取代工作量证明时， [分片链](/eth2/shard-chains/) 的复杂性会增加。 这是需要验证者来处理交易和创建新区块的独立区块链。 计划中将有 64 个分片链，并且它们都需要对网络的当下状态有一个共同的理解。 所以这需要额外的协调工作，这将由[信标链](/eth2/beacon-chain/)来完成。

信标链从碎片接收状态信息，并可供其他碎片使用，以便网络能够一直保持同步。 信标链也会管理验证者，从注册其股份存储到发布奖励和惩罚。

下面是这个过程的运作方式。

### 验证是如何工作的？ {#how-does-validation-work}

当您在分片上提交交易时，验证者将负责将您的交易添加到分片区块中。 信标链通过算法选择验证器以提出新的块。

#### 区块认证 {#attestation}

如果一个验证者没有被选中提出一个新的分块，它们将不得不证明另一个验证者的提议，并确认一切都正常。 它是在信标链中记录的证明，而不是交易本身。

至少需要 128 个被称为“委员会”的验证者来证明每个分片块。

委员会有一个提出和验证分片区块的时限， 这被称为“slot”。 每个插槽只能创建一个有效区块，一个“周期”有 32 个插槽。 每个周期过后，委员会都由不同的、随机的参与者解散与改革。 这有助于避免委员会中的不良参与者伤害到分片。

#### 交叉链接 {#rewards-and-penalties}

一旦一个新的碎片区块提案有足够的证明，就会创建一个“交叉链接”；它确认了在信标链中包含方块和你的交易。

一旦出现交叉链接，推荐区块的验证者将获得奖励。

#### 终局 {#finality}

在分布式网络中，当交易是一个不能改变的区块的一部分时，交易具有“终局性”。

要利用权益证明做到这一点，Cassper（一个终局协议）可以让验证者在某些检查点就一个区块的状态达成协议。 只要 2/3 的验证者同意，该区块就会被最终确定。 验证者如果试图稍后通过 51% 的攻击恢复，就会失去他们的全部权益。

正如 Vlad Zammfir 说的那样，这就像一位采矿者参与了 51% 的攻击，导致他们的采矿硬件立即被烧毁。

## 权益证明和安全性 {#pos-and-security}

权益证明中仍然存在 [51% 攻击](https://www.investopedia.com/terms/1/51-attack.asp)的威胁，但对于攻击者来说攻击成本越来越高。 要发起 51% 攻击，你需要掌控 51% 以上的以太币股权。 这不仅仅是一笔巨款，还很有可能导致以太币贬值。 破坏你的货币价值的大部分权益是非常容易的。 当然也有更强有力的激励措施来保持网络的安全和健康。

信标链上的权益消减、踢出和其余惩罚、协调来防治其他恶意行为。 验证者还将负责记录这些事件。

## 优缺点 {#pros-and-cons}

| 优点                                                                                                                                             | 缺点                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------- |
| 权益质押让您更容易运行一个节点。 无需大量硬件或能源投资，如果您没有足够的 ETH 抵押，您也可以加入权益质押池。                                     | 与工作量证明相比，权益证明仍处于起步阶段，并且没有经过实际应用的测试 |
| 权益质押更加去中心化。 它允许更多人参与，并且更多的节点不意味着像挖矿一样增加百分比的回报。                                                      |                                                                      |
| 权益质押可以保证安全的防护。 分片链允许以太坊同时创建多个区块，增加交易输送量。 将以太坊网络置于工作量证明系统内，这会降低网络被攻击所需的算力。 |                                                                      |

## 延伸阅读 {#further-reading}

- [什么是权益证明（PoS）](https://consensys.net/blog/blockchain-explained/what-is-proof-of-stake/) _ConsenSys_
- [你需要先读一读的以太坊 2.0 信标链全解](https://ethos.dev/beacon-chain/) _Ethos.dev_

## 相关主题 {#related-topics}

- [工作量证明](/developers/docs/consensus-mechanisms/pow/)
