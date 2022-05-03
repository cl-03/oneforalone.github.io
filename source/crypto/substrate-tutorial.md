(substrate)=
# Substrate Tutorial

## Substrate 01

### Environment Setup

#### Required Packages

* Ubuntu or Debian

```bash
sudo apt update && sudo apt install -y git clang curl libssl-dev llvm libudev-dev
```

* macOS

如果没有安装 Homebrew, 先安装 brew：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

安装完成后使用 Homebrew 安装以来库：

```bash
brew update && brew install openssl
```

#### Rust and Rust toolchain

```bash
# install rustp
curl https://sh.rustup.rs -sSf | sh
# reload current shell PATH environment
source ~/.cargo/env
# configure
rustup default stable
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly

# check it out
rustc --version
rustup show
```

#### Font-end template

##### node.js & yarn

如果没有安装 node.js，可根据自己平台下载官网对应的安装包进行安装，官网地址：https://nodejs.org/
版本最好是最新版或是 > 12, < 14, 或 14.2.0。

```bash
npm install -g yarn
```

#### Get the template source code

```bash
git clone https://github.com/substrate-developer-hub/substrate-front-end-template
cd substrate-front-end-template
git checkout latest
yarn install
```

### Build Substrate node

```bash
git clone https://github.com/substrate-developer-hub/substrate-node-template
cd substrate-node-template
git checkout latest
cargo build --release
```

### Start node

切换到 `substrate-node-template` 目录下，然后执行：

```bash
./target/release/node-template --dev
```

打开另一个 shell，切换到 `substrate-front-end-template` 下面执行：
```bash
yarn start
```
