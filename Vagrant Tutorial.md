# Vagrant 使用手册
 
## 1 Vargant介绍
### 1.1 什么是Vagrant
Vagrant 是一款用跨平台，基于虚拟机，用于构建统一的开发环境的工具，它可以运行在Windows，Mac，Linux上，底层支持VirtualBox, VMware甚至AWS作为虚拟机系统。

使用Vagrant能够为开发人员带来以下好处：

1. 统一开发环境，一次配置打包，统一分发给团队成员，统一团队开发环境。解决诸如“编码问题”、“缺少模块”、“配置文件不同”等带来的相关环境配置问题。
2. 避免重复搭建开发环境。新员工加入，不用浪费时间搭建开发环境，快速加入开发，减少时间成本的浪费
3. 多个相互隔离开的开发环境。可以在不同的box里跑不同的语言，或者安装同一语言的不同版本，搭建多个相互隔离的开发环境，卸载清除时也很快捷轻松。

### 1.2 什么是Boxes
Boxes是Vargent使用的包格式，这里的**包**可以理解为各种按需定制后的操作系统镜像。Boxes可以在被任何Vagrant支持的平台上使用，带来一致的工作环境体验。

最简单获取Boxes的方式是通过Vargant提供的[公共Vagrant Boxes列表](https://atlas.hashicorp.com/boxes/search)，你也可以自己创建Boxes并在这里进行发布。

Boxes支持版本管理，所以你的团队成员可以很简单的通过Vargant对使用的Boxes进行升级。Boxes的创建者也可以持续的对Boxes进行更新。

**PS:下面所有的讲解都将基于VirtualBox虚拟机环境。(为什么？ 因为它是轻量级的，也最容易获取，最重要的是它是免费的)**
## 2 Vagrant的运行和启动
### 2.1 快速使用
第一次安装完Vagrant后，你可以通过以下两条命令来快速尝试运行和启动Vagrant：

	$ vagrant init hashicorp/precise64
	$ vagrant up

上诉命令会自动从[公共Vagrant Boxes列表](https://atlas.hashicorp.com/boxes/search)下载指定的Boxes(如果之前没有下载过)，并进行启动。

启动结束后，你可以使用 
	
	$vagrnt ssh
命令通过SSH登入到虚拟机。当你在虚拟机上做完相关操作后，可以使用

	$vagrant destroy
	
来终止虚拟机。

后续每次需要重新启动虚拟工作环境，仅仅需要执行vagrant up命令即可。

### 2.2 Vagrant使用详细步骤
#### 2.2.1 项目初始化
配置任何基于Vagrant的项目的第一个步骤是创建**Vagrantfile**文件，Vagrantfile文件主要有以下两个作用：

1. 标记项目的根目录。大部分Vagrant中的配置都是相对于根目录进行操作。
2. 描述用来运行项目的底层虚拟机的类型和资源，以及如何对其进行访问。

Vagrant提供了一个专门的命令用来创建Vagrantfile: **vagrant init**.执行下述命令:
	
	$mkdir vagrant_getting_started
	$cd vagrant_getting_started
	$vagrant init
	
会将一个Vagrntfile文件放置在vagrant_getting_started目录，你可以使用你喜欢的编辑器查看Vagrantfile文件。不要被Vagrantfile文件的内容吓到，后面我们会对它进行详细的解释。

你可以在一个已存在目录内执行vagrant init命令，设置一个已经存在的项目。

Vagrantfile文件和你的项目一起提交到版本管理系统，这样每个在这个项目上工作的员工都可以使用Vagrant而无需重头开始设置。	

#### 2.2.2 Boxes配置
创建完Vagrantfile文件后的下一个步骤，就是添加Boxes。Boxes通过下述命令添加到Vagrant:

	$vagarant box add hashicorp/precise64
	
这条命令会从[公共Vagrant Boxes列表](https://atlas.hashicorp.com/boxes/search)下载名成为hashicorp/precise64的box。你也可以通过本次文件路径或者自定义的url等等来添加box。

被添加的Boxes通常存储在当期用户目录下(*INX系统在当前用户的.vagrant.d目录).每个项目使用box作为基础镜像，并不会修改实际的box内容。例如，当你有两个项目均使用上述添加的hashicorp/precise64 box，在其中一台虚拟中添加一个文件，并不会影响到另外一台虚拟机。

Boxes的命名规则被分为两个部分:用户名和box名，两者之间通过斜杠‘/’进行分割。在上诉的例子中，用户名为hasicorp，precise64为box名。

在将box添加到vagrant后，我们需要修改Vagrantfile文件来指定我们需要使用的box。打开Vagrantfile文件，按照下述类容来进行修改:

	Vagrant.configure("2") do |config|
  		config.vm.box = "hashicorp/precise64"
	end
	
这里**hashicorp/precise64**必须匹配你添加到vagrant的box名称。如果对应的box还没有添加，vagrant会在启动的时候自动进行下载和添加。

#### 2.2.3 运行和SSH登入
到现在为止，你可以启动你的第一个Vagrant虚拟环境:

	$vagrant up
	

命令运行结束后，你的虚拟环境将正式启动，但是你不会看到任何虚拟机的界面，这是因为Vagrant是以非UI界面形式来运行虚拟环境。你可以通过SSH来登入虚拟环境:

	$vagrant ssh
	
这条命令会启动一个SSH会话来和虚拟环境进行交互，现在你可以在这个会话内以命令行的方式和虚拟机进行交互，做任何你想要做的事情。如果想要退出SSH会话，可以使用*INX命令:exit退出SSH，回到宿主机。

你可用通过下述命令，来删除虚拟机并释放虚拟机所使用的资源:
	
	$vagrant destroy

这条命令只会删除对应的虚拟机，并不会删除对应的box。如果想要删除对应的box，可以执行vagrant box remove命令。

#### 2.2.4 共享目录
默认的，vagrant共享你的项目目录(Vagrantfile文件所在的目录)到你虚拟机的/vagrant目录。

注意，当你使用vagrant ssh命令登入虚拟后，你所在的目录为/home/vagrant目录，/home/vagrant目录和/vagrant目录为两个不同的目录。

执行下述命令:

	$ vagrant up
	...
	$ vagrant ssh
	...
	vagrant@precise64:~$ ls /vagrant
	Vagrantfile
	
这里你在虚拟环境中所看到的Vagrantfile文件实际上就是位于你宿主机本地环境中的Vagrantfile文件。在这里我们可以创建一个文件，并退出虚拟环境:

	vagrant@precise64:~$ touch /vagrant/foo
	vagrant@precise64:~$ exit
	$ ls
	foo Vagrantfile
	
这时新创建的foo文件同时存在于你的宿主机目录。就像你所看到的，vagrant保持共享目录间的实时同步。

通过共享目录，你可以继续在你的宿主机上使用你喜欢的编辑器和IDE来编辑你的文件，并同时保证这些文件可以实时同步到虚拟环境。

#### 2.2.5 虚拟环境的停止操作

vagrant支持3种虚拟机的停止方式，每种方式各有优点和缺点，可以根据实际需要确定使用何种方式停止虚拟机：

1. 挂起(Suspending)
	通过命令**vagrant suspend**完成。该方式会保存虚拟机当前的运行状态并暂停运行。当你准备重新运行虚拟机时，执行**vagrant up**命令会从你暂停的状态重新运行。该方式主要的优点是速度快，通常只需要5到10s就可以停止和重新启动虚拟机。缺点是由于需要保存停止时的虚拟机状态，会占用额外的硬盘空间。
2. 关闭(Halt)
	通过命令**vagrant halt**完成。该方式会关闭虚拟机，当你准备重新运行时，执行**vagrant up**命令。该方式的优点是会完全的关闭虚拟机，保留当前对虚拟机硬盘的修改。缺点是，由于彻底关闭了虚拟机，会花费更多的时间来重新启动虚拟机，同时虚拟机仍然占用你的硬盘空间。
3. 销毁(Destroy)
	通过命令**vagrant destroy**完成。该方式会从你的硬盘空间中彻底的删除虚拟机。当你准备重新运行时，执行**vagrant up**命令。该方式的优点是虚拟机不再占用你的硬盘空间，缺点也是显而易见，由于彻底删除了虚拟机，重新启动时，需要花费更多的时间来重新创建虚拟机并进行相应的设置。

#### 2.2.6 自动化开通(provisioning)基础

现在我们已经有了一个可以运行的基于vagrant的虚拟机环境，我们可以通过SSH进行登入并用我们平常的方式安装我们所需的应用。但是这样的话，我们每登入一个新的vagrant虚拟环境，我们就需要手工的重复进行操作。vagrant通过提供自动化开通服务(automated provisioning)，使得我们每次重新创建虚拟环境，第一次执行vagrant up命令启动的时候，自动的安装我们所需的服务应用。

这里我们通过自动话安装apached web server来描述自动化开通步骤。在Vagrantfile文件所在的目录创建下述脚本，并保存为bootstrap.sh:

	#!/usr/bin/env bash

	apt-get update
	apt-get install -y apache2
	if ! [ -L /var/www ]; then
  		rm -rf /var/www
  		ln -fs /vagrant /var/www
	fi

然后我们通过修改Vagrantfile文件来告诉vagrant在每次创建虚拟环境并第一次执行vagrant up时自动运行该脚本:

	Vagrant.configure("2") do |config|
  		config.vm.box = "hashicorp/precise64"
  		config.vm.provision :shell, path: "bootstrap.sh"
	end
	
上述配置告诉vagrant使用SHELL的方式运行bootstrap.sh脚本来自动化开通(provisioning)设置虚拟环境。bootstrap.sh脚本的所在路径相对于项目的根路径(也就是Vagrantfile文件所在的目录)。


注意: 自动化开通(provisioning)方式试用于小而快的配置，而不试用于大量的服务应用安装。可以通过自定义打包的方式在box中提现安装你所需的应用服务来避免在每次创建虚拟机时重新进行安装。这部分的内容会在后续章节进行描述。

#### 2.2.7 简单网络设置
现在我们已经在虚拟环境中有一个了可用的web服务，我们如何在自己宿主机的流浪器对其进行访问呢？ 这里可以通过修改Vagrantfile的中的网络配置来实现。这里我们仅仅介绍如何配置端口转发，其它的网络配置选项会在后续章节进行介绍。

打开Vagrantfile文件，按照下述对其进行修改:

	Vagrant.configure("2") do |config|
  		config.vm.box = "hashicorp/precise64"
  		config.vm.provision :shell, path: "bootstrap.sh"
  		config.vm.network :forwarded_port, guest: 80, host: 4567
	end

运行vagrant reload或者vagrant up命令(依赖于你的虚拟环境是否正在运行)，一旦虚拟环境重新运行完毕，你可以在你的宿主机的浏览器中输入http://127.0.0.1:4567来访问你虚拟机中的web服务。

#### 2.2.8 其它虚拟机类型使用方式

在上述所有的章节中，我们都是基于Virtualbox来配置我们的虚拟环境，但是在开头的章节中提到过vagrant不仅仅支持Virtualbox，还支持vmware，parallels desktop 设置AWS云环境。如果使用这些其它的虚拟机类型呢？

一旦你安装了新的虚拟机软件，你可以不用修改Vagrantfile中的任何配置，仅仅在vagrant up中提供新的虚拟机类型即可，vagrant会为你自动重新设置:

	vagrant up --provider=vmware_fusion
	
或者使用AWS云:
	
	vagrant up --provider=aws
	
一旦你重新指定了新的虚拟机类型，在后续的vagrant命令中无需再次指定，vagrant会自动记住你最近的设置，所以再次关闭虚拟换机并重启时，像平常一样执行vagrant up即可，而不用提供额外的选项。

## 3 Vagrant 命令说明
vagrant命令格式:

	vagrant [options] <command> [<args>]
### 3.1 INIT
### 3.1.1 命令说明
	vagrant init [box-name] [box-url]
	
该命令通过在当前目录创建一个初始的Vagrantfile文件(如果不存在的话)来初始化vagrant环境。
如果指定了第一个参数[box-name]，其值会被填写在配置文件的config.vm.box项
如果指定了第二个参数[box-url]，其值会被填写在配置文件的config.vm.box_url项。

#### 3.1.2 选项
* --force 如果指定了该选项，则会覆盖已经存在的Vagrantfile文件。
* --minimal 生成最小可用的Vagrantfile，其不会包含通常Vagrantfile文件包含的注释说明
* --output FILE 如果指定了该选项，Vagrantfile则会被输出指定的文件名。如果FILE为‘-’，则Vagrantfile则会被输出到标准输出(stdout)。

### 3.2 BOX
### 3.3 UP
### 3.4 DESTROY
### 3.5 HALT
### 3.6 SUSPEND
### 3.7 SSH
### 3.8 PLUGIN
### 3.9 PACKAGE 

	
## 4 Vagrant 配置文件

## 5 Vagrant 网络设置
在 **2.2Vagrant的运行和启动**一节中我们介绍了NAT端口转发模式的网络配置，这里我们将介绍Vagrant的其余网络设置。

### 5.1 私有网络(private network)配置
Vagrant私有网络允许你通过私有网络地址来访问你的虚拟机。这种情况下，你的每个虚拟机都会被分配一个无法通过公网访问的私有网络地址。

在同一个私有网断的虚拟机可以互相进行访问。私有网络地址的配置通常有以下两种配置方式。

#### 5.1.1 DHCP
配置私有网络最简单的方式就是通过DHCP服务来自动分配私有网络地址。
	
	Vagrant.configure("2") do |config|
  		config.vm.network "private_network", type: "dhcp"
	end

上诉配置会自动从保留的IP地址空间分配一个IP地址给到虚拟机。 由于这个IP地址是动态分配的，可以通过vagrant ssh指令登入虚拟机，然后使用ifconfig(或类似命令)得到。

#### 5.1.2 STATIC IP(静态IP)
你也可以为虚拟机指定一个静态的IP地址。这使你可以使用一个固定的、已知的IP地址来来访问虚拟机。

	Vagrant.configure("2") do |config|
  		config.vm.network "private_network", ip: "192.168.50.4"
	end
	
这里取决于使用者来确保指定的IP地址在同一网段内唯一。

虽然你理论上可以指定你所喜欢的IP地址，但是你应该从保留私有地址网段来指定IP地址。这些IP地址被保证永远不会在外部网络被使用，且大部分的路由设置也保证其不会被外部网络锁访问。

注意: 当配置私有地址时，vagrant会自动向底层所使用的provider(例如:VirtualBox)添加一个虚拟网卡，使宿主机与虚拟机在同一个私有网段。

### 5.1 公有网络(public network)配置
Vagrant的公有网络比私有网络(private network)要更为开放，其确切的实际意义在不同的底层provided各有不同。与私有网路不同的，Vagrant的公有网络可以被外网所访问。

Vagrant的公有网络同样有两种配置方式。

#### 5.1.1 DHCP
配置公有网络最简单的方法仍然是DHCP。

	Vagrant.configure("2") do |config|
  		config.vm.network "public_network"
	end

上诉配置会自动从保留的IP地址空间分配一个IP地址给到虚拟机。 由于这个IP地址是动态分配的，可以通过vagrant ssh指令登入虚拟机，然后使用ifconfig(或类似命令)得到。

在某些情况下要求DHCP服务默认的路由规则不能被改变，这个时候可以指定**use_dhcp\_assigned\_default\_route**选项。

Vagrant.configure("2") do |config|
    config.vm.network "public_network",
    use_dhcp_assigned_default_route: true
end

#### 5.1.2 STATIC IP(静态IP)

你可以手工来指定IP地址。

	config.vm.network "public_network", ip: "192.168.0.17"


如果你的宿主机存在多个网络接口，vagrant会要求你来选择需要被被桥接的网络接口。你也可以直接在配置文件通过**:bridge**来指定。

	config.vm.network "public_network", bridge: "en1: Wi-Fi (AirPort)"
	
这里的接口名称必须完全匹配你宿主机上的接口名称。

你可以指定一份接口列表:


	config.vm.network "public_network", bridge: [
  		"en1: Wi-Fi (AirPort)",
  		"en6: Broadcom NetXtreme Gigabit Ethernet Controller",
	]

这个时候，列表中一个存在且可以被正常桥接的网络接口将会被使用。









