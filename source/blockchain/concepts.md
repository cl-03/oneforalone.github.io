# Basic Concepts

## Hash Function

哈希函数是密码学中的一种加密算法，所谓函数呢，即是两个集合之间对应的映射关系。哈希函数的描述是：
> A mathematical algorithm that maps data of an arbitrary size (often called the "message")
> to a bit array of a fixed size (the "hash value", "hash", or "message digest").

翻译过来就是：对任意输入，输出固定长度的字符

其工作流程如图所示：
![hash-function](/_static/images/hash-function.png)

对 hash 函数来说，有以下特性：

* 计算快
* 不可逆
* 冲突域[^conflict-domain]小

[^conflict-domain]: 冲突域，就是两个不同的输入会得到相同的输出

不可逆是其最为重大的特征，只能通过穷举法来破解，但对于现在的计算机来说，计算的时间过长，所以就认为不可被破解。

根据其输出的长度，有多种不同的具体实现：

* MD5: 输出长度为 128 bits，即 16 bytes
* SHA-1: 输出长度为 160 bits，即 20 bytes
* RIPEMD-160: 输出长度为 160 bits，即 20 bytes，基于 MD4[^md4] 设计的
* Whirlpool: 输出长度为 512 bits，即 64 bytes，基于 AES 的改进
* SHA-2:
  - SHA224: 输出长度为 224 bits，即 24 bytes
  - SHA256: 输出长度为 256 bits，即 32 bytes
  - SHA384: 输出长度为 384 bits，即 48 bytes
  - SHA512: 输出长度为 512 bits，即 64 bytes

[^md4]: MD5的初版

### Reference

[1]. [Wikipedia: Cryptographic hash function](https://en.wikipedia.org/wiki/Cryptographic_hash_function)


## Merkle Tree

Wiki 对 Merkle Tree 的定义是：
> A tree in which every "leaf" (node) is labelled with the cryptographic hash of a data block,
> and every node that is not a leaf (called a *branch*, *inner node*, or *inode*) is labelled
> with the cryptographic hash of the labels of its child nodes.

以下是 Merkle Tree 的图示：
![merkle-tree](/_static/images/merkle-tree.svg)

简单的概括来说，就是 hash 的 hash。

为什么要这么做呢？这和 hash 函数的第三个特性有关，冲突域小。虽然hash 函数的冲突域很小，但是对区块链来说，
其存在的安全漏洞还是比较大，那么怎么解决的，给它在加一层 hash，这样就能在既保证效率的同时还能保证安全。

### Reference

[1]. [Wikipedia: Merkle tree](https://en.wikipedia.org/wiki/Merkle_tree)


## Consensus Mechanism

Consensus Mechanism，共识机制。以下是个比较贴切的定义：
> How the individual nodes of a distributed network agree on the current state
> of the system

共识共识，即对某件事的处理方式和结果打成一致。在计算机的分布式系统中，即是各个节点的当前系统中
的状态都有遵守一个协议，至于这个协议是什么，那就要下面要介绍到的 `PoW/PoS/PoST/PoA`。如果
对共识机制感兴趣的话，可以自行了解下[Byzantine fault](https://en.wikipedia.org/wiki/Byzantine_fault)。
中文的话搜索拜占庭将军问题。


### PoW

**Proof of Work** 的简写，工作量证明（说了也是白说），一种共识机制协议。目前 Ethereum 和
Bitcoin 使用的都是 **PoW** 协议，Ethereum 目前正准备将协议从 **PoW** 改为 **PoS**，
升级为 Eth2。

那么 **PoW** 到底是个什么呢？

在分布式网络中，最大的问题是要解决信任问题，就是你需要怎么证明这件事确实是你做的，而不是别人做的。
感谢 Hash 函数（那些到处宣扬数学无用论的人，祝愿他们永远也用不到数学），因为 Hash 函数的特性，
所以 **PoW** 就相当于在做数学题，以下只是形式上的描述，仅供参考，具体的实现要举例的要复杂的多。

$ Hash(x || nonce) = C $

其中 `nonce` 是系统随机分配的，`C` 是由系统根据当前的状态进行指定，`Hash` 函数的算法也是确定的，
所需要的是求出 `x` 的值，而因为 hash 算法是不可逆的，所以只能通过穷举法来尝试，而穷举法是很消耗
CPU 资源的，需要做大量的计算，因而叫做 **PoW** —— 工作量证明。其他人要验证也很简单，只需要将 `x`
和 `nonce` 代入函数，将得到的结果与 C 进行对比，因为哈希碰撞域很小，小到几乎可以忽略，因此可以
根据这个结果来判断对方是否正确，算出答案的这个节点就能够获得奖励。因此，区块链就是在凭空造币。

因为这种机制，所以又将做这种计算的工作叫做挖矿，执行计算工作的计算机叫矿机。

> Tips. 第一个算出答案并同步上链的机器就证明了它算出了这个答案，而其他的晚了的被称为孤块，不同
> 的链对孤块的处理方式不同，BitCoin 是直接将孤块丢掉，Ethereum 是将孤块定义为 Uncle Block，
> 前 7 个 Uncle Block 都有奖励。

#### Pros and Cons

* Pros：
  - 无需成本，只要有矿机就可以开挖
  - 运行稳定，目前已经在 Bitcoin 上运行了十多年了
  - 实现简单

* Cons：
  - 耗电
  - 开挖前需要对矿机硬件进行调研
  - 大矿池容易控场，从去中心化又转变成中心化

#### Reference

[1]. [PROOF-OF-WORK](https://ethereum.org/en/developers/docs/consensus-mechanisms/pow/)


### PoS

**Proof of Stake** 的简写，权益证明，对 **PoW** 的一种改进。由 **PoW** 的特性可知，执行
**PoW** 的的计算机需要做大量的计算，这就意味着需要消耗大量的电量，同时也催生了一些定制的CPU：
*ASIC* —— Application Specific Integrated Circuit。随着 BTC 的价格飙升，这些缺点
所带来的弊端也越来越严重，所以也不怪国家会打击 BTC 的矿场。

话题扯远了，回到 **PoS**，它具体是怎么工作的呢？首先，权益证明就是说，你需要先提交一定的 Token[^token]
进行质押，质押了 Token 的机器叫做*验证者（Validator）*。验证者负责对提交上链的新的 block 进行
验证，而不需要进行大量大计算，只有在新的 block 验证不通过的时候才会创建新的 block，同时并不是说
你质押了就会一直验证，而是系统进行随机的抽选，而一旦验证者进行作弊或是离线，系统将会没收质押的 Token。
所以说验证者之间没有像 **PoW** 中那样——第一个出块才有奖励——的竞争关系。

[^token]: Token，就是每个链发行的虚拟货币，如ETH就是Ethereum的Token。

从操作流程来看， **PoS** 对 **PoW** 的改进有：

* 省电
* 门槛低：这里的门槛指的是服务器的门槛，服务器的硬件不在像 **PoW** 那样高性能。
* 对中心化的抵抗更强：这回引入更多的节点加入网络，网络的节点越多，可信度就越高。

对 Ethereum 来说，**PoS** 也带来对 `shard chains`[^shard] -- [shard chains](https://ethereum.org/en/eth2/shard-chains/) 更强劲的支持，而这是 Ethereum Network
扩展的关键性更新。

[^shard]: 在垂直方向拓展整条链，允许在同一时间在垂直方向创建多个block，也就相当于原来一个block的位置，之后可以放多个block，即block的超集（我自己这么理解的）。多个block组成了一个epoch，即epoch为block的超集。

#### Pros and Cons

* Pros:
  - 硬件要求低：不需要对硬件进行调查，如果没有质押，只需要加入质押池
  - 更去中心化：硬件要求低意味着可以让更多的计算机加入网络，以此来增加 `51% 攻击`[^51attack]的抵抗性
  - 允许 sharding: 这个优点是针对 Eth2 来说的，Ethereum 的 shard chains 可在同一时间创建更多的block，
    增加 transaction 的吞吐。
* Cons:
  - 相比 **PoW** 技术还不是很成熟

[^51attack]: 所谓51%攻击，就是说如果有人/组织掌握了整个链上的51%的节点，那么该人/组织就能控制整条链。

#### Reference

[1]. [PROOF-OF-STAKE](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/)

[2]. [shard chains](https://ethereum.org/en/eth2/shard-chains/)

[3]. [51% attack](https://www.investopedia.com/terms/1/51-attack.asp)


### PoST

**Proof of Spacetime** 或 **Proof of Space and Time** 的简写，时间和空间证明，简称时空证明。每隔一段时间，随机
抽取存放在 miner[^miner] 上的存储数据，来验证这段时间内该 miner 确实存储了对应的数据。代表性的链是：[filecoin](https://filecoin.io/)

[^miner]: 即节点的实现，通俗点说就是挖矿软件

### Reference

[1]. [What sets it apart: Filecoin's proof system](https://filecoin.io/blog/posts/what-sets-us-apart-filecoin-s-proof-system/)

[2]. [How Filecoin Works: Proofs](https://docs.filecoin.io/about-filecoin/how-filecoin-works/#proofs)

[3]. [FAQ: What is Proof of Space and Time](chia.net/faq/#faq-3)


### PoA

> TODO: Need more time to invesigating.
> Deadline: 2021-12-12

**Proof of Authority**，Using in [Polkadot Network](https://polkadot.network/)


### Reference

[1]. [PoA Private Chains](https://github.com/ethereum/guide/blob/master/poa.md)


## Zero-Knowledge Proof

> **Notes: it's a long term study subject, need more time to read paper & reference.**

### Non-interactive Zero-Knowledge Proof


### Interactive Zero-Knowledge Proof


### Reference

[1]. [Wikipedia: Zero-knowledge proof](https://en.wikipedia.org/wiki/Zero-knowledge_proof#Zero_knowledge_types)

[2]. [Wikipedia: Non-interactive zero-knowledge proof](https://en.wikipedia.org/wiki/Non-interactive_zero-knowledge_proof)

[3]. [Non-interactive zero-knowledge proof of knowledge and chosen ciphertext attack](https://link.springer.com/chapter/10.1007/3-540-46766-1_35)

[4]. [Definitions and properties of zero-kenowledge proof systems](https://link.springer.com/article/10.1007/BF00195207)

[5]. [On the composition of zero-knowledge proof systems](https://epubs.siam.org/doi/abs/10.1137/S0097539791220688)