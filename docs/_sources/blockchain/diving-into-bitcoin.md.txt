(diving-into-bitcoin)=
# Diving into Bitcoin

> **CRYPTO IS HARD, DO NOT TRUST ANYONE, VERIFY YOURSELF !!!**

(prodromes)=
## 序论

正如开头引用的那句话，加密货币里面涉及到的知识太多了，而且经过十多年的发展，
所需要学习的知识也很多，不过，随着慢慢的接触，我渐渐发现其最底层的原理和基础
是大同小异的。因此，为了深入研究，需要从加密货币的鼻祖进行研究（好吧，其实是
工作需求需要用到相关的知识）。

同时因为大部分人接触 bitcoin 只是了解了一下其工作机制，大部分人还是只炒币，
并没有深入去解析 bitcoin 的具体实现和代码，加之区块链目前越来越火，技术和
资料也日新月异，bitcoin的资料其实都比较老了（相对区块链来说），所以在这里
记录下我的一些学习记录。

这篇只是个初稿，之后会抽时间出来扒一扒 bitcoin 的源代码（这个要等到我看完
geth —— Ethereum node 的代码）。技术积累需要时间，同时也需要消耗大
量的精力。

本文只是个人对于 bitcoin 的理解，对于加密算法的具体原理和实现，个人暂时还
没有研究到那个地步，只能说有时间在慢慢去看文章读博客去理解。毕竟我也只接触
crypto 不到一年的时间。如果其中有任何错误的话，可以尽情指正。大家都只是海边
捡贝壳的小孩而已。

> P.S. 本文知识技术分析介绍，对于投资理财不做任何推荐担保。投资有风险，
> 入市需谨慎，对于市场和生活要常怀敬畏之心。


## Keys, Addresses, Wallets

(bitcoin-keys)=
### Keys


(bitcoin-addresses)=
### Addresses


(bitcoin-wallets)=
### Wallets


(bitcoin-tx)=
## Transaction

Transaction，通常缩写成 tx，本文之后统一使用 tx 表示 transaction。
那么什么是 tx 呢？其实 tx 在金融或者跟确切点来说 —— 银行中用的最多，
因为 tx 的字面意思就是交易记录。而 bitcoin 作为分布式的账本，其记录
的就是 tx。所以了解 tx 或者说看懂 tx 是一个懂行的 crypto 的必备知识，
毕竟要想确认自己的转账是否成功，最可靠的办法就是去浏览器（区块链的浏览器）
上查看对应的 tx。而对于技术来说，不单要看懂 tx，还要知道 tx 是怎么构造
的，即根据自己的需求定制一个 tx。


(bitcoin-utxo)=
### UTXO

在讲 tx 之前，首先要介绍一下 bitcoin 记账的独特机制，一般的银行账户来说，会
有一个账户余额的概念，而对于 bitcoin 来说，根本就没有账户余额这一说，而是采用
一种简单的记账机制，即 Unspent Transaction Output（UTXO）。对于每个 tx
来说，都至少要有一个 Input 和 一个 Output，而每个 Input 是来自上一个 Output，
每个 Output 都等待着下一个 Input 来消费，而这些 Output 就叫做 UTXO。例如，
你有 1 btc（10^8 satoshi），那么就意味着你在等待一个或多个 UTXO。那么最初的
UTXO 来自那里呢？当然是矿工了，因为每当矿工挖到了一个 block，就会被奖励对应的
btc，而这些奖励是矿工的 Input，这也就变成了矿工们的 UTXOs 了。


### Transaction Struct

介绍为 UTXO，那么就直接介绍一下 tx 的具体的结构。

每个 tx 有六部分组成：

* `version`：4 个字节（uint32_t），表示当前的版本，数字越大，版本越新，当前都
  是 1 或 2，2 表示该 tx 符合 [BIP-68](https://github.com/bitcoin/bips/blob/master/bip-0068.mediawiki#specification)。
* `tx_in count`：可变大小（uint），表示当前 tx 中包含的 input 个数，即下面
  `tx_in` 数组中 txid 的个数。
* `tx_in`：tx 的输入，数组，具体介绍见 {ref}`transaction-input`
* `tx_out count`：可变大小（uint)，表示当前 tx 中包含的 output 个数，即下面
  tx_out` 数组中 txid 的个数
* `tx_out`：tx 的输出，数组，具体介绍见 {ref}`transaction-output`
* `lock_time`：UTXO 的锁定时间或区块（uint_32_t），用来表示这个 tx 中的 UTXO
  多久或多少个区块后可以被消费（优先级没有 `tx_in` 中 `sequence` 的高）


(transaction-input)=
### TxIn: A Transaction Input (Non-Coinbase)


(transaction-txin-outpoint)=
### Outpoint


(transaction-output)=
### TxOut: A Transaction Output


(bitcoin-scipting)=
## Scripting



(bitcoin-terminology)=
## Terminology


## Reference

1. https://bitcoin.org/bitcoin.pdf
2. https://developer.bitcoin.org/devguide/transactions.html
3. https://developer.bitcoin.org/reference/transactions.html
4. https://github.com/bitcoinbook/bitcoinbook
