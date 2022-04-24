(diving-into-bitcoin)=
# Dive into Bitcoin

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
* `tx_out count`：可变大小（uint)，表示当前 tx 中包含的 output 个数，即
  下面 `tx_out` 数组中 txid 的个数
* `tx_out`：tx 的输出，数组，具体介绍见 {ref}`transaction-output`
* `lock_time`：UTXO 的锁定时间或区块（uint_32_t），用来表示这个 tx 中的
  UTXO 多久或哪个区块后可以被消费（优先级没有 `tx_in` 中 `sequence` 的高）


(transaction-input)=
### TxIn: A Transaction Input (Non-Coinbase)


* `previous_output`：36 字节，上一个 UTXO，或者说是之前的 `tx_out` 中的一个
  元素，又被称为 `outpoint`，其结构见 {ref}`transaction-txin-outpoint`。
* `script bytes`：签名脚本的大小（即签名脚本是多少个字节）
* `signature script`：char[]，满足 `outpoint` 中的公钥脚本（`pb_script`）
* `sequence`：4 字节，默认为 `0xffffffff`，这个值是用来禁用 `locktime`，
  如果需要启用 `locktime`，但因为目前 `sequence` 并没有什么用途，所以启用
  `locktime` 需要将 `sequence` 设为 `0`，即 `0x00000000`。对于 `locktime`
  启用时的解析如下：
  - 如果 `< 500,000,000`，则 `blocktime` 被解析为 block height，即多少个
    区块后这个 utxo 才能被使用。
  - 如果 `>= 500,000,000`，则被解析为 Unix epoch time[^unix-epoch-time]，
    即锁定到某个确切的时间节点，改时间节点后这个 utxo 才能被使用。

[^unix-epoch-time]: 从1970-01-01T00:00UTC距离现在多少秒，计算机通用的做法。


(transaction-txin-outpoint)=
### Outpoint

* `hash`：32 字节，char[32]，包含本次 input 所花费的 utxo 的 tx 的哈希值，
  这个哈希值是正常得到哈希值相反的字节序（小端对齐，little-endian）
* `index`：4 字节，uint32_t，所需要话费的 utxo 在 tx 中位置，即 utxo 在
  `vout`/`tx_out` 数组中的索引/位置。


(transaction-output)=
### TxOut: A Transaction Output

* `value`：8 字节，int64_t，允许其他人花费的 satoshi，这里的 satoshi 总量
  不能超过 input 中的总量
* `pk_script bytes`：uint，`pk_script` 的大小，`pk_script` 最多不能超过
  10,000 字节，即 `pk_script bytes` 最大为 14 位，2 字节。
* `pk_script`：定义这个 utxo 是谁可以用的，只有这个脚本执行成功的人才能使用
  这个 utxo，通俗点就是转给谁的。


### 实例

Talk is cheap，所以下面是一个 pay-to-pubkey-hash[^scripts] 的 tx：

[^scripts]: 比特币有几种脚本语言，这个不是最新的，是比较老的，但也还有在用。

```
01000000 ................................... Version

01 ......................................... Number of inputs
|
| 7b1eabe0209b1fe794124575ef807057
| c77ada2138ae4fa8d6c4de0398a14f3f ......... Outpoint TXID
| 00000000 ................................. Outpoint index number
|
| 49 ....................................... Bytes in sig. script: 73
| | 48 ..................................... Push 72 bytes as data
| | | 30450221008949f0cb400094ad2b5eb3
| | | 99d59d01c14d73d8fe6e96df1a7150de
| | | b388ab8935022079656090d7f6bac4c9
| | | a94e0aad311a4268e082a725f8aeae05
| | | 73fb12ff866a5f01 ..................... [Secp256k1][secp256k1] signature
|
| ffffffff ................................. Sequence number: UINT32_MAX

01 ......................................... Number of outputs
| f0ca052a01000000 ......................... Satoshis (49.99990000 BTC)
|
| 19 ....................................... Bytes in pubkey script: 25
| | 76 ..................................... OP_DUP
| | a9 ..................................... OP_HASH160
| | 14 ..................................... Push 20 bytes as data
| | | cbc20a7664f2f69e5355aa427045bc15
| | | e7c6c772 ............................. PubKey hash
| | 88 ..................................... OP_EQUALVERIFY
| | ac ..................................... OP_CHECKSIG

00000000 ................................... locktime: 0 (a block height)
```

这是官方提供的转账金额为 49.9999000 BTC 的 tx。

下面让我们使用本地的测试网来自己手动发起一个 tx，然后看看这个 tx 的数据。
首先，在自己的 pc 或服务器上安装好 bitcoin 的 node，具体操作步骤，参考
{ref}`bitcoin-on-pi`。安装好后，先创建三个钱包，一个是矿工的钱包，另外
两个分别是 Alice 和 Bob（加密学中通用的 Alice 和 Bob）[^cryptocouple]。

[^cryptocouple]：http://cryptocouple.com/

注：这里在配置文件需要在 `bitcoin.conf` 中添加 `txindex=1` 这个配置，
然后重启 bitcoind，不然 `getrawtransaction` 命令用不了[^txindex]。

[^txindex]: https://bitcoin.stackexchange.com/questions/90608/cant-get-the-tx-with-gettransaction-or-getrawtransaction-methods

```bash
$ bitcoin-cli -named createwallet wallet_name="miner" \
    avoid_reuse=true load_on_startup=true

$ bitcoin-cli -named createwallet wallet_name="alice" \
    avoid_reuse=true load_on_startup=true

$ bitcoin-cli -named createwallet wallet_name="bob" \
    avoid_reuse=true load_on_startup=true

```

然后分别在钱包中创建一个新地址：

```bash
$ bitcoin-cli -rpcwallet=miner getnewaddress
bcrt1q6xhr6gpxvlnv2n2gjhu03psa20w9rschxktjfv
$ bitcoin-cli -rpcwallet=alice getnewaddress
bcrt1qxwv2hwwh7dsyrxdu4g86vwysexnq87c3mmnw0f
$ bitcoin-cli -rpcwallet=bob getnewaddress
bcrt1q94rqgutjhjhtct6n3l6evpgccrn0mjt9v7qn5h
```

生成地址后挖 101 个块给 miner，这个 101 是有讲究的，因为 bitcoin
miner 挖出来的块需要 100 个区块确认后才可以消费，不然 miner 的
balance 不会改变，依旧为 0。如果你想给 miner 中多放点钱，那就多
挖一些，方正不能少于 101，我就是因为这个困扰了我好几天。

```bash
$ bitcoin-cli generatetoaddress 101 bcrt1q6xhr6gpxvlnv2n2gjhu03psa20w9rschxktjfv
# check the balance of the miner wallet
$ bitcoin-cli -rpcwallet=miner getbalance
50.00000000
# transfer 25 btc to alice from miner
$ bitcoin-cli -rpcwallet=miner -named sendtoaddress \
    address=bcrt1qxwv2hwwh7dsyrxdu4g86vwysexnq87c3mmnw0f \
    amount=25 fee_rate=100
87546021539c56063eeb30e5e28e041f55aeaaee04c6f53f881e88192aaf753d # transaction hash/txid
# generate a block to confirm a tx
$ bitcoin-cli generatetoaddress 1 bcrt1q6xhr6gpxvlnv2n2gjhu03psa20w9rschxktjfv
[
  "4731e661205d634c327380ae5d9de9f52da05d236e1985ea9c079694ed488c3a"
]
$ bitcoin-cli -rpcwallet=alice getbalance
25.00000000
# transfer 10 btc to bob from alice
$ bitcoin-cli -rpcwallet=alice -named sendtoaddress \
    address=bcrt1q94rqgutjhjhtct6n3l6evpgccrn0mjt9v7qn5h \
    amount=10 fee_rate=100
c47bc10dc069bbd2bb669b3bb954564cc75c5c0f184dba896bde33415ee7e359
# generate a block again to confirm the tx
$ bitcoin-cli generatetoaddress 1 bcrt1q6xhr6gpxvlnv2n2gjhu03psa20w9rschxktjfv
[
  "0216b94f03c4d37dd07ca356afecee74e488f13f24b6af7e5badec095ae8e6fd"
]
# check the balance to verify the tx
$ bitcoin-cli -rpcwallet=bob getbalance
10.00000000
```

通过以上步骤，miner 向 alice 转了 25 btc，然后 alice 像 bob 转了 10 btc。
那么现在来看下 alice 转给 bob 的那条 tx，
`c47bc10dc069bbd2bb669b3bb954564cc75c5c0f184dba896bde33415ee7e359`：

```bash
$ bitcoin-cli getrawtransaction c47bc10dc069bbd2bb669b3bb954564cc75c5c0f184dba896bde33415ee7e359
020000000001013d75af2a19881e883ff5c604eeaaae551f048ee2e530eb3e06569c53216054870100000000fdffffff0200ca9a3b000000001600142d46047172bcaebc2f538ff5960518c0e6fdc965ecf76759000000001600148eede2516b0ff9e902440a95225e39d6d98fad330247304402205faa35a19c56faa2c78f40bf84918ee53117e789c9bb27a37c8c36b0b70d713402205855358e1379e225742110fcdfed064b80e1443c1df3e8c7f77a1c12a3c97211012102ccc59184de0b0e5318308ca755b7af3eef3c62a41c1d802d3a0f1e92c87b529166000000
```

没有搞明白为什么 `getawtransaction` 为什么会没结果。
把这个 tx 中的 hex 用 `decoderawtransaction` 来解析一下这个 tx。

```
bitcoin-cli decoderawtransaction 020000000001013d75af2a19881e883ff5c604eeaaae551f048ee2e530eb3e06569c53216054870100000000fdffffff0200ca9a3b000000001600142d46047172bcaebc2f538ff5960518c0e6fdc965ecf76759000000001600148eede2516b0ff9e902440a95225e39d6d98fad330247304402205faa35a19c56faa2c78f40bf84918ee53117e789c9bb27a37c8c36b0b70d713402205855358e1379e225742110fcdfed064b80e1443c1df3e8c7f77a1c12a3c97211012102ccc59184de0b0e5318308ca755b7af3eef3c62a41c1d802d3a0f1e92c87b529166000000
{
  "txid": "c47bc10dc069bbd2bb669b3bb954564cc75c5c0f184dba896bde33415ee7e359",
  "hash": "b7311840269d702779a59014cf21afaaffa5cba8c7ef981d930136bc9e229110",
  "version": 2,
  "size": 222,
  "vsize": 141,
  "weight": 561,
  "locktime": 102,
  "vin": [
    {
      "txid": "87546021539c56063eeb30e5e28e041f55aeaaee04c6f53f881e88192aaf753d",
      "vout": 1,
      "scriptSig": {
        "asm": "",
        "hex": ""
      },
      "txinwitness": [
        "304402205faa35a19c56faa2c78f40bf84918ee53117e789c9bb27a37c8c36b0b70d713402205855358e1379e225742110fcdfed064b80e1443c1df3e8c7f77a1c12a3c9721101",
        "02ccc59184de0b0e5318308ca755b7af3eef3c62a41c1d802d3a0f1e92c87b5291"
      ],
      "sequence": 4294967293
    }
  ],
  "vout": [
    {
      "value": 10.00000000,
      "n": 0,
      "scriptPubKey": {
        "asm": "0 2d46047172bcaebc2f538ff5960518c0e6fdc965",
        "desc": "addr(bcrt1q94rqgutjhjhtct6n3l6evpgccrn0mjt9v7qn5h)#n8l7wa0p",
        "hex": "00142d46047172bcaebc2f538ff5960518c0e6fdc965",
        "address": "bcrt1q94rqgutjhjhtct6n3l6evpgccrn0mjt9v7qn5h",
        "type": "witness_v0_keyhash"
      }
    },
    {
      "value": 14.99985900,
      "n": 1,
      "scriptPubKey": {
        "asm": "0 8eede2516b0ff9e902440a95225e39d6d98fad33",
        "desc": "addr(bcrt1q3mk7y5ttplu7jqjyp22jyh3e6mvcltfncm3ptx)#thq7jlj5",
        "hex": "00148eede2516b0ff9e902440a95225e39d6d98fad33",
        "address": "bcrt1q3mk7y5ttplu7jqjyp22jyh3e6mvcltfncm3ptx",
        "type": "witness_v0_keyhash"
      }
    }
  ]
}
```

好吧，研究一个小时发现翻车了，这个 tx 是比较新的，使用的是 segwit
的脚本语言，这个下一节中会去研究。但是不管怎么样，最基础的没有变，也
就是要做个微调，总之今天就是要将这个 tx 解析出来，以下是解析的结果：

```
02000000 ................................... Version
00 ......................................... Marker, segwit format
01 ......................................... Flag, segwit format
01 ......................................... Number of Inputs: 1, tx_in counts
|
|                                            Txln/vin
| 3d75af2a19881e883ff5c604eeaaae55
| 1f048ee2e530eb3e06569c5321605487 ......... Oupoint TXID
|
| 0100000000 ............................... Outpoint index number: 1
|
| fdffffff ................................. Sequence Number, 4294967293(0xfffffffd)
|
|                                            TxOut/vout

02 ......................................... Number of outputs: 2, tx_out counts
| 00ca9a3b00000000 ......................... Satoshis (10.00000000 BTC)
|
| 16 ....................................... Bytes in pubkey script: 22
| | 00 ..................................... Nothing
| | 14 ..................................... Push 20 bytes as data
| | | 2d46047172bcaebc2f53
| | | 8ff5960518c0e6fdc965 ................. Pubkey hash, big-endian
|
| ecf7675900000000 ......................... Satoshis (14.99985900 BTC)
|
| 16 ....................................... Bytes in pubkey script: 22
| | 00 ..................................... Nothing
| | 14 ..................................... Push 20 bytes as data
| | | 8eede2516b0ff9e90244
| | | 0a95225e39d6d98fad33 ................. Pubkey hash, big-endian
|
|                                            Witness

02 ......................................... Number of witness: 2, size of witness
|
| 47 ....................................... Bytes in witness script: 71
| | 304402205faa35a19c56faa2c78f40bf
| | 84918ee53117e789c9bb27a37c8c36b0
| | b70d713402205855358e1379e2257421
| | 10fcdfed064b80e1443c1df3e8c7f77a
| | 1c12a3c9721101 ......................... Witness Script, big-endian
|
| 21 ....................................... Bytes in witness script: 33
| | 02ccc59184de0b0e5318308ca755b7af
| | 3eef3c62a41c1d802d3a0f1e92c87b5291 ..... Witness Script, big-endian

|                                            nLockTime
66000000 ................................... locktime:  102 (a block height)
```

就先到这里吧，Witness Script 还没有去研究，就不去解析他了，等看
完所有的 scripting 在来解析吧。上面的 locktime 指的是 block
number，而不是多少个 block 之后，而是在这个 block number/height
后就能被花费。

Segwit 的格式参考文章为： https://bitcoincore.org/en/segwit_wallet_dev/#transaction-serialization

(bitcoin-scipting)=
## Scripting



(bitcoin-terminology)=
## Terminology


## Reference

1. https://bitcoin.org/bitcoin.pdf
2. https://developer.bitcoin.org/devguide/transactions.html
3. https://developer.bitcoin.org/reference/transactions.html
4. https://github.com/bitcoinbook/bitcoinbook
