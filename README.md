# Introduction
* 在`linux`系统上配置`bcc`、`prometheus`、`grafana`，通过bcc对内核中的数据进行提取并存入`prometheus`中，通过`grafana`进行数据可视化

# 项目启动前置准备工作
## 环境
Ubuntu 20.04

## bcc配置
在Ubuntu中配置bcc环境
1. 根据Ubuntu版本配置bcc环境
   ```shell
    # Trusty (14.04 LTS) and older
    VER=trusty
    echo "deb http://llvm.org/apt/$VER/ llvm-toolchain-$VER-3.7 main
    deb-src http://llvm.org/apt/$VER/ llvm-toolchain-$VER-3.7 main" | \
      sudo tee /etc/apt/sources.list.d/llvm.list
    wget -O - http://llvm.org/apt/llvm-snapshot.gpg.key | sudo apt-key add -
    sudo apt-get update
   
    # For Bionic (18.04 LTS)
    sudo apt-get -y install zip bison build-essential cmake flex git libedit-dev \
      libllvm6.0 llvm-6.0-dev libclang-6.0-dev python zlib1g-dev libelf-dev libfl-dev python3-setuptools \
      liblzma-dev arping netperf iperf
   
    # For Focal (20.04.1 LTS)
    sudo apt install -y zip bison build-essential cmake flex git libedit-dev \
      libllvm12 llvm-12-dev libclang-12-dev python zlib1g-dev libelf-dev libfl-dev python3-setuptools \
      liblzma-dev arping netperf iperf
   
    # For Hirsute (21.04) or Impish (21.10)
    sudo apt install -y zip bison build-essential cmake flex git libedit-dev \
      libllvm11 llvm-11-dev libclang-11-dev python3 zlib1g-dev libelf-dev libfl-dev python3-setuptools \
      liblzma-dev arping netperf iperf
   
    # For Jammy (22.04)
    sudo apt install -y zip bison build-essential cmake flex git libedit-dev \
      libllvm14 llvm-14-dev libclang-14-dev python3 zlib1g-dev libelf-dev libfl-dev python3-setuptools \
      liblzma-dev libdebuginfod-dev arping netperf iperf
   
    # For other versions
    sudo apt-get -y install zip bison build-essential cmake flex git libedit-dev \
      libllvm3.7 llvm-3.7-dev libclang-3.7-dev python zlib1g-dev libelf-dev python3-setuptools \
      liblzma-dev arping netperf iperf
   
    # For Lua support
    sudo apt-get -y install luajit luajit-5.1-dev
   ```
2. 下载[`bcc release v0.24.0`](https://github.com/iovisor/bcc/releases/tag/v0.24.0)的`bcc-src-with-submodule.tar.gz
`并解压
3. 编译`bcc`
    ```shell
    mkdir bcc/build; cd bcc/build
    cmake ..
    make
    sudo make install
    cmake -DPYTHON_CMD=python3 .. # build python3 binding
    pushd src/python/
    make
    sudo make install
    popd
    ```

## prometheus配置
1.下载`prometheus`

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.26.0/prometheus-2.26.0.linux-amd64.tar.gz
tar -zxvf prometheus-2.26.0.linux-amd64.tar.gz
```

2.运行`prometheus`

```bash
./prometheus --config.file=prometheus.yml
```

在浏览器输入访问地址：`http://localhost:9090/graph` 可以看到`Prometheus` 自带的监控管理界面

## grafana配置

1.下载`grafana`

```bash
wget https://dl.grafana.com/oss/release/grafana-7.5.2.linux-amd64.tar.gz
tar -zxvf grafana-7.5.2.linux-amd64.tar.gz
```

2.运行`grafana`

```bash
./grafana-server web
```

Grafana 默认使用 3000 端口启动，浏览器访问：`http://localhost:3000` 查看对应页面
