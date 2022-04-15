(smart-contract-development-toolkits)=
# Smart Contract Development Toolkits

Date: Wed Apr. 13 2022


## 序

智能合约，目前公认的是 Ethereum 上的定义，可以简单的理解为运行在
Ethereum 中 EVM 上的程序，推广一下就是运行在分布式系统上的程序。

而一个程序的开发过程，不外乎来说有三步：编写源代码，编译以及链接。

通常来说，编译和链接都是有一个工具完成的，而对于区块链来说，链接
对应的是上链（我喜欢这么叫，反正就是将编译出来的 EVM 字节码发布
到区块链上），而上链设计到网络交互，这个差异就导致了编译和链接是
由不同的工具完成。

当然，官方有提供 IDE —— Remix，使用也很简单，但是相信我，作为一个
学习者，使用 IDE 不是个好途径。（因为网络的原因，我经常会遇到要修改
一个合约代码时加载不出来，然后又要重写。所以国内的话，能不用 Remix
就不用 Remix）。出了网络的问题，还有一个就是 IDE 有时会掩盖掉一些
细节，anyway，IDE 这个问题只能说是见仁见智，反正我学习时是不喜欢。

以下是我了解到的智能合约开发的 toolkits。

> P.S. 目前我所指得智能合约均指代的是 Etheruem 生态的智能合约，不涉及
> NEAR 和 SOLONA 这些链的智能合约开发（这两个链最好还是先把 Rust 学
> 一遍吧，因为他们的合约使用 Rust 去写的）。

NOTE: 因为区块链又和 "Web3" 紧密联系到一起了，所以，在开发智能合约时，
最好还是先去学一下 JavaScript(或者 NodeJS)，不然就会像我一开始学开发
智能合约一样，两眼懵逼，又要看 Solidity 的文档，还要去查 NodeJS 的文档。


(hardhat)=
## Hardhat

这个 toolkit 使用起来很简单，我目前使用的就是这个，但是最近准备把 Rust
用起来，准备切换到 Foundry 开发了。首先，先将官方文档的链接放出来：
https://hardhat.org/getting-started 。如果有条件的话，建议还是去看
英文文档。


(hardhat_installation)=
### 安装

安装有两种选择:

* 安装在对应的项目，只有进入当前项目才能使用：

```bash
npm install --save-dev hardhat
```

* 安装到系统环境中，可以在系统的任意目录下使用：

```bash
npm install -g hardhat
```


(hardhat_usage)=
### 使用


(hardhat_init)=
#### 初始化

```bash
npx hardhat
```

执行完这条有如下输出，会有一些配置选项：

```
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.9.3 👷‍

? What do you want to do? …
❯ Create a basic sample project
  Create an advanced sample project
  Create an advanced sample project that uses TypeScript
  Create an empty hardhat.config.js
  Quit
```

可以根据自己的需求进行选择，如果不知道该选哪个，直接一路按回车选默认的就好了。
默认会创建一些基础的文件（以下是除了 `node_modules` 的目录结构）：

```
$ tree -I node_module ./
./
├── README.md
├── contracts
│   └── Greeter.sol
├── hardhat.config.js
├── package-lock.json
├── package.json
├── scripts
│   └── sample-script.js
└── test
    └── sample-test.js

3 directories, 7 files
```

* `contracts` 目录：存放 solidity 源代码文件
* `hardhat.config.js`: Hardhat 配置文件
* `scripts`: 存放部署（上链）合约的 JS 脚本文件
* `test`: 存放测试合约的 JS 脚本文件

至于 `package-lock.json` 和 `package.json` 这两个文件是 npm 的配置文件。
主要是因为 `node_modules` 目录太大了，不会上传到 Github 上，所以别人使用你
的代码时，只要执行 `npm install` 就回把所以的依赖都安装好。


(hardhat_config)=
#### 配置

这里需要额外介绍一下 `hardhat.config.js` 文件，最初的文件内容为：

```js
require("nomiclabs/hardhat-waffle");

// This is a sample Hardhat task. To learn how to create your own go to
// https://hardhat.org/guides/create-task.html
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: "0.8.4",
};
```

其中配置中可以添加 `task`，上面代码中是一个打印当前配置所以的账户地址，名字叫
`accounts` —— `task` 函数中第一个参数。执行 `npx hardhat accounts` 就
是执行这个 `task` 函数。如果你喜欢的话，也可以添加一个部署（`deploy`）任务：

```js
task("deploy", "Deploying the contract", async (taskArgs, hre) => {
  // set the first account to deployer
  const [deployer] = await hre.ethers.getSigners();

  console.log("Deploying contracts with account: ", deployer);
  console.log("Account balance: ", (await deployer.getBalance()).toString());

  // get contract instance
  const Greeter = await hre.ethers.getContractFactory("Greeter");
  const greeter = await Greeter.deploy("Hello, world");
  await greeter.deployed();

  console.log("Greeter deployed to: ", greeter.address);
});
```

保存后需要部署时直接执行：`npx hardhat deploy` 就可以将合约部署到链上了。
不过我不推荐，因为配置文件一旦设置好后，我基本不会去改，而部署的脚本经常要改动
的，我更喜欢在 `scripts` 目录下创建一个 `deploy.js` 的部署脚本。

这里稍微讲解一下脚本里的一些方法和属性：

* `hre.ethers.getContractFactory(Greeter"` 函数中的参数是构造一个合约
  的对象，参数是 `contracts` 目录下 `.sol` 的文件名，即合约的文件名。

* `Greeter.deploy("Hello, world")`，是将合约部署到链上，其中参数就是合约
  构造函数的参数，如果有多个参数的话，以逗号分隔。

* `await greeter.deployed()`，是等待合约上链成功，因为区块打包需要一定时间。

* `greeter.address`: 合约部署成功后的地址，可以去区块链的 scan 网站查看，
  Ethereum 的是 https://etherscan.io。

其实前两个方法可以合并成一段代码：

```js
const greeter = await hre.ethers.getContractFactory("Greeter")
                                .deploy("Hello, world");
```

不过这样看起来有点乱。不优雅。

Whoo, 介绍完 `task` （其实也顺带介绍了部署），然后再介绍一下 `module.exports`。

首先是其中的第一个 key/value：`solidity: "0.8.4"`，这个是制定对应的 `solidity`
的版本，有一点需要注意，不同版本的 `solidity` 所支持的特性有所不同。

然后可以在其中添加 `network` 的配置，就是指定合约具体要部署到 Ethereum Ecosystem
上具体的那个 Network，是 `mainnet`，还是 `testnet`，还是 `sidechain`，亦或是
`layer 2`？以下给出一些对应的 network 的配置以供参考：

```js
module.exports = {
  // solidity: "0.8.4", // depends on your solidity version
  solidity: {
    version: "0.8.4",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  }
  defaultNetwork: "rinkeby", // defined below
  networks: {
    mainnet: {
      url: "https://xxxxxxxxxxx", // your node provider link
      accounts: [privateKey1, privateKey2, ...], // this array stores your accounts' private key
    },
    rinkeby: {
      url: "https://xxxxx",
      accounts: [privateKey1, privateKey2, ...],
    },
    mumbai: {
      url: "https://xxxxxxx",
      accounts: [privateKey1, privateKey2, ...],
    },
  },
  paths: {
    sources: "./contracts",
    tests: "./test",
    cache: "./cache",
    artifacts: "./artifacts"
  },
  mocha: {
    timeout: 40000
  }
}
```

* `networks`

`networks` 中每个网络的 `url` 的值是节点的链接，就是说你需要去链接一个**全节点**，
通过这个全节点来对链进行操作，可以是第三方（如 Infura、Alchemy）提供的，也可以是
自己本地运行的，如果是本地运行的全节点，`url` 的值可以设置为：
`http://127.0.0.1:8545`。因为第三方提供的服务是有 api key 或是 token 的，所以
呢，需要进行一个封装，通用的做法是将一些私密性的配置单独存放在项目更目录下的一个文件中：
`.env`。然后代码就需要改一下，下面只展示需要修改的，若没有出现的，保持原有的不变：

```js
// ..., the same as before
require('dotenv').config();
require('@nomiclabs/hardhat-ethers');
const { PROVIDER_URL, PRIVATE_KEY } = process.env;
// or you can write like this:
// const PROVIDER_URL = process.env.PROVIDER_URL;
// const PRIVATE_KEY = process.env.PRIVATE_KEY;

module.exports = {
  // ...
  networks {
    mainnet: {
      url: PROVIDER_URL, // your node provider link
      accounts: [`0x${PRIVATE_KEY}`], // this array stores your accounts' private key
    },
    rinkeby: {
      url: PROVIDER_URL,
      accounts: [`0x${PRIVATE_KEY}`],
    },
    mumbai: {
      url: PROVIDER_URL,
      accounts: [`0x${PRIVATE_KEY}`],
    },
  }
  // ...
}
```

`.env` 文件里的内容为：

```
PROVIDER_URL = "https://eth-rinkeby.alchemyapi.io/v2/123abc123abc123abc123abc123abcde"
PRIVATE_KEY = "" # fill your own private key
```

注：这里的 `PROVIDER_URL` 不同的网络对应不同的 URL，不能像上面示例一样使用同样的，
否则无法连上 full node。


`accounts` 字段的值是一个数组，数组中是账号的私钥，这里需要在导出的私钥前面添加 `0x`
字符串。当然，如果代码要上传到 Github 上，私钥泄露的话，你这个地址基本也就废了，所以
呢，也需要放在 `.env` 文件中。

* `solidity`

`solidity` 出了可以指定版本外，还可以设置编译是是否进行优化。

* `path`

`path` 指定了对应的源代码文件路径，`sources` 指定合约代码路径，`tests` 指定
测试代码的路径，`cache` 指定缓存目录路径，`artifacts` 指定了合约代码编译后生
成的 abi 文件的存放路径。

* `mocha`

`mocha` 是执行测试时的一些配置，上面只配置了超时时间，更多的配置参考：
https://mochajs.org/#command-line-usage ，其中的参数是一致的。


更多相关的配置介绍，参考：https://hardhat.org/config/#path-configuration


(hardhat_compile)=
#### 编译

```bash
npx hardhat compile
```

编译没啥好介绍的，通过了就通过了，出错了就自己去修复。


(hardhat_deploy)=
#### 上链（部署）

在 `scripts` 目录下创建一个 `deploy.js`（或者你自己取个名字，我用 deploy 命名）
的文件，文件内容如下：

```js
const { ethers } = require("hardhat");

async function main() {
  const [deployer] = await ethers.getSigners();

  console.log("Deploying contracts with the account: ", deployer.address);
  console.log("Account balance: ", (await deployer.getBalance()).toString());

  const Greeter = await ethers.getContractFactory("Greeter");
  const greeter = await Greeter.deploy("Hello, world");

  await greeter.deployed();

  console.log("Contract deployed at: ", greeter.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  })
```

然后执行：

```bash
npx hardhat run scripts/deploy.js
```

正常的话你将你的合约部署好了。

Update[Fir Apr. 15 2022]: 这两天又仔细瞄了一眼 Hardhat 的文档，发现 `scripts`
目录下的脚本是完全不依赖 Hardhat 的，如果使用 `web3.js`/`etheres.js` 库的话，可以
直接用 node 执行。

对了，忘了写怎么测试了，我测试的话直接是用的 `web3.js`，直接部署到测试网上测试的，反正
不要钱。


(truffle)=
## Truffle

Date: Fri Apr. 15 2022

今天试着用了一下 Truffle，发现是真的难用（可能是我水平不够），反正我不喜欢。
Truffle 基础的使用是真的简单，但是它的配置和部署不是很适合我。最主要的原因
还是，在里面需要额外多加一个 `Migrate.sol` 的合约，部署时也要先部署
`Migrate`，官网的解释是说确保安全，同时也说也可以自己去修改 `Migrate.sol`
合约。我比较喜欢 KISS( Keep It Simple and Stupid) 的准则。额外多加一个
合约，部署时就得付 gas fee，安全的话直接使用 Openzeppelin 的库就好了。
还有一个就是如果合约要引用 npm 安装的库的话，不能使用通用的引用格式。这个也让
我不太喜欢。

当然也不是没有优点的，那就是 truffle 可以和 ganache 直接交互，使用命令
`truffle develop` 或是 `truffle console` 就可以直接进入交互模式，
前提是需要先启动 ganache。

好了，介绍就说到这里了，下面简单介绍下怎么使用吧。


(truffle_installation)=
### 安装

说到安装，在国内的环境是真的很痛苦，除非使用代理，不过代理最好是稳定一点的，
我就是因为代理稍微有点不稳定（可能是我用的设备比较多），导致出错好几次，浪费
我将近一个小时的时间。

```bash
# configure your proxy first
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890
node install -g truffle
```


(truffle_usage)=
### 使用


(truffle_init)=
#### 初始化

```bash
mkdir my-project
truffle init
```


(truffle_config)=
#### 配置

编辑项目目录下的 `truffle-config.js`:

```js
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*"
    },
    test: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*"
    }
  },

  compilers: {
    solc: {
      version: "^0.8.0"
    }
  }

}
```

上面只配置了测试开发的网络，主网的话需要配置一样，只不过是 `host` 的值设为
全节点的 IP 或 URL，和 hardhat 配置中的网络配置的 `url` 参数去掉端口的值，
然后 `port` 就写成对应的端口就好，本地的 ganache-cli 的话是 8545，ganache
GUI 版本的话端口就是 7545，如果是三方提供的全节点的话，那么就是 80/443。
或者是直接不使用 host 参数，而是使用 `provider` 参数，值设置为
`new Web3.providers.HttpProvider("https://<host>:<port>)`。
当然 `network` 里面也可以设置 `gas`、`gasPrice`、`from` 等参数。
详细的配置说明见：https://trufflesuite.com/docs/truffle/reference/configuration


(truffle_compile)=
#### 编译

编译很简单，直接执行 `truffle compile` 就可以了，编译好的 abi 文件默认存放
在 `build` 目录下。这个目录路径也可以在 `truffle-config.js` 中配置，在
其中添加一行：
```js
contracts_build_directory: "/path/to/the/abi/output/director"
```
路径自己定义，不要照抄。


(truffle_deploy)=
#### 部署

首先要在 `migrations` 目录下创建一个对应的脚本，一般习惯命名为
`2_deploy_contracts.js`, 因为该目录下已经有一个 `1_initial_migration.js`
文件了。这里以官方给的例子讲解：

```bash
mkdir MetaCoin
cd MetaCoin
truffle unbox metacoin
truffle migrate
```

`truffle migrate` 会自动执行 `migrations` 目录下所有的脚本。
MetaCoin 的目录结构如下：

```
$ tree ./
./
├── LICENSE
├── contracts
│   ├── ConvertLib.sol
│   ├── MetaCoin.sol
│   └── Migrations.sol
├── migrations
│   ├── 1_initial_migration.js
│   └── 2_deploy_contracts.js
├── test
│   ├── TestMetaCoin.sol
│   └── metacoin.js
└── truffle-config.js

3 directories, 9 files
```

其中 `MetaCoin.sol` `import` 了 `ConvertLib.sol`。

MetaCoin 的 `2_deploy_contracts.js` 如下：

```js
const ConvertLib =artifacts.require("ConvertLib");
const MetaCoin = artifacts.require("MetaCoin");

module.exports = function(deployer) {
  deployer.deploy(ConvertLib);
  deployer.link(ConvertLib, MetaCoin);
  deployer.deploy(MetaCoin);
}
```

前两行没啥可讲的，就是导入合约的 abi，即 json 文件。然后先部署被
`import` 的合约（`ConvertLib.sol`)，将两个合约链接到一起
（`deployer.link(ConvertLib, MetaCoin)`，最后在部署调用其他
合约的合约。

这里的部署函数被完全封装起来了，我也不想去扒其中的代码了，所以不要问
`deployer` 是什么了，具体是什么我没去看源代码，不确定，但可以推测出
truffle 封装了一个根据配置文件中的一些配置生成了个
`web3.eth.Contract` 对象传进去了。所以说我不太喜欢它，封装的有点
过了，虽然我可以自己完全用 `web3.js` 写一个部署脚本，但那样的话我
为什么不用 hardhat 呢？

Truffle 介绍也就到这里了，最后再附上官方文档地址：
https://trufflesuite.com/docs/truffle/quickstart/

> P.S. truffle 还有一个槽点就是团队不怎么维护了，所以懂得都懂。


(brownie)=
## Brownie

**TODO: 添加完整的使用体验和介绍**。

Brownie，一个由 python 和 web3.py 实现的一个 toolkit，或者说
framework 吧。怎么叫随你了。


(brownie_installation)=
### 安装

```bash
python3.9 -m pip install --user pipx
python3.9 -m pipx ensurepath
source ~/.zshrcr
pipx install eth-brownie
```

**注**：这里需要使用 python3.9 或相近的版本，但不能高于 3.9，比如
3.10 安装会出错的。我只验证过 python3.9，3.8 和 3.7 没有验证过。


(brownie_usage)=
### 使用


(brownie_init)=
#### 初始化


(brownie_config)=
#### 配置


(brownie_compile)=
#### 编译


(brownie_deploy)=
#### 部署


reference: https://eth-brownie.readthedocs.io/en/stable/


(foundry)=
## Foundry

**TODO: 添加完整的使用体验和介绍**

Foundry 是个用 rust 实现的 toolkit，用法和 Brownie 差不多。


(foundry_installation)=
### 安装

官方提供的安装二进制的方法我在 M1 上失败了，所以我就从源代码
直接编译出来了。

官方的安装二进制的命令：

```bash
curl -L https://foundry.paradigm.xyz | bash
```

在从源代码编译安装之前，需要首先安装好 Rust 和 Cargo，
不然你怎么编译呢？

从源代码编译安装官方也提供了对应的命令：

```bash
curl https://sh.rustup.rs -sSf | sh
```




(foundry_usage)=
### 使用


(foundry_init)=
#### 初始化


(foundry_config)=
#### 配置


(foundry_compile)=
#### 编译


(foundry_deploy)=
#### 部署


reference: https://book.getfoundry.sh/getting-started/installation.html
