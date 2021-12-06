# 基础概念

## Hash 函数

## Merkle Tree

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

$ Hash(x, nonce) = C $

其中 `nonce` 是系统随机分配的，`C` 是由系统根据当前的状态进行指定，`Hash` 函数的算法也是确定的，
所需要的是求出 `x` 的值，而因为 hash 算法是不可逆的，所以只能通过穷举法来尝试，而穷举法是很消耗
CPU 资源的，需要做大量的计算，因而叫做 **PoW** —— 工作量证明。其他人要验证也很简单，只需要将 `x`
和 `nonce` 代入函数，将得到的结果与 C 进行对比，因为哈希碰撞域很小，小到几乎可以忽略，因此可以
根据这个结果来判断对方是否正确，算出答案的这个节点就能够获得奖励。因此，区块链就是在凭空造币。

因为这种机制，所以又将做这种计算的工作叫做挖矿，执行计算工作的计算机叫矿机。

> Tips. 第一个算出答案并同步上链的机器就证明了它算出了这个答案，而其他的晚了的被称为孤块，不同
> 的链对孤块的处理方式不同，BitCoin 是直接将孤块丢掉，Ethereum 是将孤块定义为 Uncle Block，
> 前 7 个 Uncle Block 都有奖励。

### PoS


### PoW VS. PoS


### PoST



## Zero-Knowledge Proof
