# Docker
## 虚拟化和容器化技术
### 虚拟化技术
虚拟化技术是一种将计算机物理资源进行抽象、转换为虚拟的计算机资源提供给程序使用的技术。这些资源包括了 CPU 提供的运算控制资源，硬盘提供的数据存储资源，网卡提供的网络传输资源等。

#### 跨平台
保证程序跨平台兼容，也就是要保证操作系统或物理硬件所提供的接口调用方式一致，程序便不需要兼容不同硬件平台的接口。此时突然想到，使用 `Swift` 编写 iOS app 时，构建出包后总是会带上 `Swift` 的整个运行时，以保证随着 iOS 系统版本的升级 app 的正常运行，因其 `ABI` 并未稳定，还不能内置在操作系统中。

#### 资源管理
可将虚拟化技术运用于计算机资源的管理，其中最实用的就是“虚拟内存”虚拟化技术能够提高计算机资源的使用率，是指利用虚拟化，可以将原来程序用不到的一些资源拿出来，分享给另外一些程序，让计算机资源不被浪费。


### 虚拟化技术的分类
主要分为两大类：**硬件虚拟化**和**软件虚拟化**。

* 硬件虚拟化：比如假设 iOS 基于的 arm 架构 CPU 能够运行基于 x86 架构的 macOS 应用程序，这是因为 CPU 能够将另外一个平台的指令集转换为自身的指令集执行（但实际上并不可能）。

* 软件虚拟化：在 2018 WWDC 中，宣布可以在 `UIKit` 层面提供一部分把 iOS app 转移到 macOS app 中的特性，可以理解为是 Apple 在 Xcode 层面协助开发者构建了迁移代码，帮开发者解决了不同平台指令的转换。也就是说，软件虚拟化实际上是通过一层夹杂在应用程序和硬件平台上的虚拟化实现软件来进行指令的转换。

其它虚拟化技术的分类：
* **平台虚拟化**：在操作系统和硬件平台间搭建虚拟化设施，使得整个操作系统都运行在虚拟后的环境中。类似 `VMware`、`PD`；
* **应用程序虚拟化**：在操作系统和应用程序间实现虚拟化，只让应用程序运行在虚拟化环境中。类似 `Python` 的虚拟环境；
* **内存虚拟化**：将不相邻的内存区，甚至硬盘空间虚拟成统一连续的内存地址，即虚拟内存；
* **桌面虚拟化**：让本地桌面程序利用远程计算机资源运行，达到控制远程计算机的目的。类似华为云的云桌面以及各种远程桌面控制软件，如 Teamviewer。
* ......

### 虚拟机
虚拟机通常说法是通过一个**虚拟机监视器( Virtual Machine Monitor )** 的设施来隔离操作系统与硬件或应用程序和操作系统，以达到虚拟化的目的。这个虚拟机监视器，通常被称为：**`Hypervisor`**。

虚拟机有一个永远都逃不掉的问题：性能低下。这种效率的低下有时候是无法容忍的，故真实的虚拟机程序常常不完全遵守 `Hypervisor` 的设计结构，而是引入一些其它技术来解决效率低下问题，比如解释执行、即时编译（Just In Time）运行机制，但这些技术的引入已不属于虚拟化的范畴了。

### 容器技术
按分类或者实现方式来说，容器技术应该属于**操作系统虚拟化**，也就是在由操作系统提供虚拟化的支持。总的来说，容器技术指的是操作系统自身支持一些接口，能够让应用程序间可以互不干扰的独立运行，并能够对其在运行中所使用的资源进行干预。

那这也不应该被称为“容器”呀？是的，这里所谓的容器指的是由于应用程序的运行被隔离在了一个独立的运行环境之中，这个独立的运行环境就好似一个容器，包裹了应用程序。

容器这么火爆，火到一心扑在 iOS 上的我都要好好梳理一番，很重要的一个原因是其在运行性能上远超虚拟机等其它虚拟化实现，甚至在运行效率上与真实运行在物理平台的应用程序不相上下。但注意，容器技术并没有进行指令转换，运行爱容器中的应用程序自身必须支持在真实操作系统上运行，也就是必须遵守硬件平台的指令规则。

曾经看到一篇文章说 `linux` **内核命名空间**的改进，直接推动了容器的最大化发展。

> 利用内核命名空间，从进程 ID 到网络名称，一切都可在 Linux 内核中实现虚拟化。新增的用户命名空间“使得用户和组 ID 可以按命名空间进行映射。对于容器而言，这意味着用户和组可以在容器内部拥有执行某些操作的特权，而在容器外部则没有这种特权。”Linux 容器项目 (LXC) 还添加了用户亟需的一些工具、模板、库和语言绑定，从而推动了进步，改善了使用容器的用户体验。LXC 使得用户能够通过简单的命令行界面轻松地启动容器。（来源 `redhat` 官网）

容器由于没有虚拟操作系统和虚拟机监视器这两个层次，大幅减少了应用程序带来的额外消耗。所以在容器中的应用程序其实完全运行在了宿主操作系统中，与其它真实运行在其中的应用程序在指令运行层面是完全没有任何区别的。


## `Docker` 的核心组成
### 四大组成对象
#### 镜像
可以理解为一个只读的文件包，其中包含了虚拟环境运行的最原始文件系统的内容。

因为 `Docker` 采用 `AUFS` 作为底层文件系统的实现，实现了一种**增量式**的镜像结构。每次对镜像内容修改，`Docker` 都会将这些修改铸造成一个镜像层，而一个镜像本质上是由其下层所有的镜像层所组成的，而每一个镜像层单独拿出来，都可以与它之下的镜像层组成一个镜像。正是由于这种结构，`Docker` 的镜像本质上是无法被修改的，因为所以的镜像修改只会产生新的镜像，而不是更新原有的镜像。

#### 容器
在容器技术中，容器是用来隔离虚拟环境的基础设施，但在 `Docker` 中，被引申为隔离出来的虚拟环境。如果我们把镜像理解为类，则容器为实例对象。镜像内存放的是不可变化的东西，当以他们为基础的容器启动后，容器内也就成为类一个“活”的空间。

`Docker` 的容器应该有三项内容组成：
* 一个 `Docker` 镜像；
* 一个程序运行环境；
* 一个指令集合。

#### 网络
在 `Docker` 中可对每个容器进行单独的网络配置，也可对各个容器间建立虚拟网络，将数个容器包裹其中，同时与其它网络环境隔离，并且 `Docker` 还能在容器中构造独立的 `DNS`，我们可以在不修改代码和配置的前提下直接迁移容器。

#### 数据卷
在以往的虚拟机中，大部分情况下都直接使用虚拟机的文件系统作为应用数据等文件的存储位置，但并未是完全安全的，当虚拟机或容器出现问题导致文件系统无法使用时，虽可直接通过快速的镜像进行重制文件系统以至于恢复，但数据也就丢失了。

为保证数据的独立性，通常会单独挂在一个文件系统来存放数据，得意与 `Docker` 底层的 `Union File System` 技术，我们可以不用管类似于搞定挂载在不同宿主机中实现的方法、考虑挂载文件系统兼容性、虚拟机操作系统配置等问题。

## 镜像与容器
### `Docker` 镜像
所有的 `Docker` 镜像都是按照 `Docker` 所设定的逻辑打包的，也是收到 `Docker Engine` 所控制。常见的虚拟机镜像都是由其它用户通过各自熟悉的方式打包成镜像文件，公布到网上再被其它用户所下载后，恢复到虚拟机中的文件系统中，但 `Docker` 的镜像必须通过 `Docker` 来打包，也必须通过 `Docker` 下载或导入后使用，不能单独直接恢复成容器中的文件系统。这样，我们就可以直接在服务器之间传递 `Docker` 镜像，并配合 `Docker` 自身对镜像的管理功能，使得在不同的机器中传递和共享变得非常方便。

每一个记录文件系统修改的镜像层 `Docker` 都会根据它们的信息生产一个64位的 `hash` 码，正是因为这个编码，可以能够区分不同的镜像层并保证内容和编码是一致的，我们可以在镜像之间共享镜像层。当 `A` 镜像依赖了 `C` 镜像，且 `B` 镜像也依赖了 `C` 镜像，在实际使用过程中，`A` 和 `B` 两个镜像是可以公用 `C` 镜像内部的镜像层的。

#### 查看镜像
```
$ docker images
```

#### 镜像命名
可以分为三部分：
* **username**：一般都是镜像创作者，但如果不写则是由官方进行维护。
* **repository**：一般都是该镜像中所包含的软件名。但镜像名归镜像名，镜像归镜像，`Docker` 对容器的设计和定义是微型容器而不是庞大臃肿的完整环境，所有通常只会在一个容器中运行一个应用程序，能够大幅降低程序之间互相的影响，利用容器技术控制每个程序所使用的资源。
* **tag**：

#### 主进程
在 `Docker` 的设计中，容器的生命周期与容器中 `PID` 为 1 这个进程由密切的关系，容器的启动本质上可以理解为这个进程的启动，而容器的停止也就意味着这个进程的停止。

### 写时复制
通过镜像运行容器时并不是立即把镜像里所有内容拷贝到容器所运行的沙盒文件系统中，而是利用 `UnionFS` 将镜像以只读方式挂载到沙盒文件系统中，只有在容器对文件的修改时，修改才会体现到沙盒环境上。

## 从镜像仓库获得镜像
### 获取镜像
```
docker pull ubuntu
```

### 获取镜像更详细的信息
```
docker inspect ubuntu
```

### 搜索镜像
```
docker search django
```

### 删除镜像
```
docker rmi ubuntu
```

## 运行和管理容器
### 容器的生命周期
* **Created**
* **Running**
* **Paused**
* **Stopped**：容器的停止状态下，占用的资源和沙盒环境都存在，只是容器中的应用程序均已停止
* **Deleted**

#### 创建容器
```
$ docker create ubuntu
```
如果我们之前选择的 `docker pull` 容器并不是默认的 `latest` 版本，而是手动选择了一个版本，那镜像的名字将会比如 `nginx:1.12`，对于后续的操作都十分的不方便，对此，我们可以采用 `--name` 进行重命名：

```
$ docker create --name nginx nginx:1.12
```

#### 启动容器
```
$ docker start ubuntu
```

通过 `docker run` 可将上述两个命令进行合并：
```
$ docker run --name nginx nginx:1.12
```

以上命令跑起来的容器运行都是运行在前台，如果我们想要容器运行在后台，可以通过 `-d`，其是 `-detach` 的简称，告诉 `Docker` 在启动后将程序和控制进行分离。：
```
$ docker run -d ubuntu 
```

#### 管理容器
列出运行中的所有容器
```
$ docker ps 
```

列出所有容器
```
$ docker ps -a/-all
```

其中打印出的列表需要注意的是 **STATUS** 字段，常见的状态表示有三种：
* **Create**：容器已创建，没有启动过；
* **Up[ Time ]**：容器正在运行，[ Time ] 代表从开始运行到查看时的时间；
* **Exited([ Code ]) [ Time ]**：容器已结束运行，[ Code ] 表示容器结束运行时，主程序返回的程序退出码，而 [ Time ] 则表示容器结束到查看时的时间。

#### 停止和删除容器
```
$ docker stop ubuntu
```
容器停止后，其维持的文件系统沙盒环境会一直保存，内部被修改的内容也会被保留。通过 `docker start` 将容器继续启动。

当需要把容器完全删除容器，可以使用：
```
$ docker rm ubuntu
```
但在运行中的容器默认情况下是不能被删除的，但我们可以通过以下命令进行删除：
```
$ docker rm -f ubuntu
```

#### 随时删除容器
`Docker` 与其它虚拟机不同，其所定位的轻量级设计讲究随用随开，随关随删，当我们短时间内不需要使用容器时，最佳的做法是删除它而不是仅仅停止它。

如果我们要对程序做一些环境配置，完全可以直接将这些配置打包至一个新的镜像中，下次直接使用该镜像创建容器即可。对于一些重要的文件资料，不能随着容器的删除而删除，可以使用 `Docker` 中的**数据卷**来单独存放。

### 进入容器
#### 直接创建，进入
```
$ docker run -it --name ubuntu ubuntu 
```

#### 已经创建完成，进入
```
$ docker exec -it ubuntu /bin/bash
```

* `-i` 表示保持我们的输入流；
* `-t` 表示启用一个伪终端，形成我们与 bash 的交互。

当容器运行在后台，想要在将当前的输入输出流连接到指定的容器上，可以这么做：
```
$ docker attach ubuntu
```
通过 `docker attach` 启动的容器，可以理解为与 `docker run -d` 做了相反的事情，把当前容器从后台拉回了前台。


## 为容器配置网络
### 容器网络
在 `Docker` 网络中，有三个比较核心的概念，形成了 `Docker` 的网络核心模型，即**容器网络模型（Container Network Model）**：
* 沙盒：提供容器的虚拟网络栈。比如端口套接、`IP` 路由表、防火墙等；
* 网络：`Docker` 内部的虚拟子网，网络内的参与者相互可见并能够进行通讯。需要注意的是，这种虚拟网络与宿主机存在隔离关系。
* 端点：主要目的是形成一个可以控制的突破封闭网络环境的出入口，当容器的端点与网络的端点形成配对后，就如同在这两者之间搭建了桥梁，可进行数据传输。

### `Docker` 的网络实现
目前官方提供了五种网络驱动：
* Bridge Driver(default)：通过基于硬件或软件的网桥来实现通讯
* Host Driver
* Overlay Driver：借助 `Docker` 的集群模块 `Docker Swarm` 来搭建的跨 `Docker Daemon` 网络，可以通过它搭建跨物理主机的虚拟网络，从而让不同物理机中运行的容器感知不到多个物理机的存在。
* MacLan Driver
* None Driver

### 容器互联
这块等用到了再补充.....



