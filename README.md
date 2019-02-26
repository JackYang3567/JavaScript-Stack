# vagrant

* 一、安装步骤如下：
1. 下载virtualBox，地址点[这里](https://www.virtualbox.org/wiki/Downloads),进入后点 Windows hosts，下载完成后运行安装
2. 下载vagrant，地址点[这里](https://www.vagrantup.com/downloads.html),进入后点 Windows 64-bit，下载完成后运行安装
3. 下载镜像，有几个地方可以下载：http://www.vagrantbox.es/和https://atlas.hashicorp.com/boxes/search都可以，这里我随便找了个centos65-x86_64-20140116
4. 将下载的镜像加载，顺便说下，第3步可以不用，vagrant支持在线安装镜像，但由于长城的原因，所以最好通过其它方法将镜像下载下来,再在本地加载,，打开cmd，输入以下命令：
# 命令是这样的，title为自己起的名字，url为box地址，可以在线也可以本地
# vagrant box add {title} {url}
# 实际安装命令，本地下载下来的
vagrant box add CentOS7.0_64 D:\User\vagrant\boxes\centos-7.0-x86_64.box 
 5、找一个/新建一个目录，例如vagrant_Centos7.0，然后初始化环境



mkdir vagrant_Centos7.0
cd vagrant_Centos7.0
vagrant init  CentOS7.0_64

vagrant up

Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'CentOS7.0_64'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: vagrant_CentOS70_default_1523580130953_14376
==> default: Clearing any previously set forwarded ports...
Vagrant is currently configured to create VirtualBox synced folders with
the `SharedFoldersEnableSymlinksCreate` option enabled. If the Vagrant
guest is not trusted, you may want to disable this option. For more
information on this option, please refer to the VirtualBox manual:

  https://www.virtualbox.org/manual/ch04.html#sharedfolders

This option can be disabled globally with an environment variable:

  VAGRANT_DISABLE_VBOXSYMLINKCREATE=1

or on a per folder basis within the Vagrantfile:

  config.vm.synced_folder '/host/path', '/guest/path', SharedFoldersEnableSymlinksCreate: false
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
The guest machine entered an invalid state while waiting for it
to boot. Valid states are 'starting, running'. The machine is in the
'unknown' state. Please verify everything is configured
properly and try again.

If the provider you're using has a GUI that comes with it,
it is often helpful to open that and watch the machine, since the
GUI often has more helpful error messages than Vagrant can retrieve.
For example, if you're using VirtualBox, run `vagrant up` while the
VirtualBox GUI is open.

The primary issue for this error is that the provider you're using
is not properly configured. This is very rarely a Vagrant issue.

如果报错：
Timed out while waiting for the machine to boot. This means that
Vagrant was unable to communicate with the guest machine within
the configured ("config.vm.boot_timeout" value) time period.

If you look above, you should be able to see the error(s) that
Vagrant had when attempting to connect to the machine. These errors
are usually good hints as to what may be wrong.

If you're using a custom box, make sure that networking is properly
working and you're able to connect to the machine. It is a common
problem that networking isn't setup properly in these boxes.
Verify that authentication configurations are also setup properly,
as well.

If the box appears to be booting properly, you may want to increase
the timeout ("config.vm.boot_timeout") value.

打开vagrantfile

To enable GUI mode, make sure this section in your Vagrantfile is uncommented:

config.vm.provider "virtualbox" do |vb|
   vb.gui = true
end

又如果出现错误：
VT-x/AMD-V 硬件加速在您的系统中不可用。您的 64-位虚拟机将无法检测到 64-位处理器，从而无法启动。
https://jingyan.baidu.com/article/09ea3ede7a9dd7c0aede39fb.html

7、此时windows下可以用putty或者是xshell来连接，host地址如上方标识的注意： 127.0.0.1:2222，用户名密码都为：vagrant

若要取得root用户的权限（避免每次命令都输入sudo）
$ su root
Passwort:vagrant

#

默认的共享文件夹的对于关系是windows下的vagrant_project文件夹对应linux下的/vagrant文件夹，可以随便在双方建立几个文件测试下是否两方是同步的
8、关闭等命令，由于还有好多需要配置，暂时可以关闭，等配置完成后再启动
#关闭
vagrant halt
#重启
vagrant reload

常用的几个vagrant命令

$ vagrant init      # 初始化

$ vagrant up        # 启动虚拟机
$ vagrant halt      # 关闭虚拟机
$ vagrant reload    # 重启虚拟机
$ vagrant ssh       # SSH 至虚拟机
$ vagrant suspend   # 挂起虚拟机
$ vagrant resume    # 唤醒虚拟机
$ vagrant status    # 查看虚拟机运行状态
$ vagrant destroy   # 销毁当前虚拟机


#box管理命令
$ vagrant box list    # 查看本地box列表
$ vagrant box add     # 添加box到列表

$ vagrant box remove  # 从box列表移除 

二、网络配置、共享目录等相关配置
以上仅仅是展示了安装和开启的简单用法，但真正能用于开发还需要额外的配置，打开上方提到的Vagrantfile配置文件来进行配置，好多都是已注释掉的，选择去掉即可
1、网络配置，vagrant有三种配置方法：官网文档点这里查看
　　a、端口映射，意思是将虚拟机的端口映射到主机的端口上，主机局域网内可以通过访问这个端口来访问到你虚拟机上的东西
config.vm.forwarded_port 80, 8080
　　上述作用是将虚拟机的80端口映射到主机的8080端口上，那我直接访问主机的8080端口就相当于访问虚拟机的80端口
　　b、私有网络，只有主机才能访问虚拟机，主句局域网内成员不能访问的虚拟机的内容
config.vm.network "private_network", ip: "192.168.33.10"
　　上述设置完成后，在本机可以通过192.168.33.10来访问虚拟机，需要注意192.168.33.10虽然可以随便设定，但如果主机的ip为192.168.1.xxx字段，那就不要这个再用这个区段，可以2.xxx或3.xxx或其它，以免造成冲突。
　　c、共有网络，主机局域网内成员可以访问虚拟机中的内容，虚拟机相当于局域网内的一员
config.vm.network "public_network", ip: "192.168.1.120"
　　以上配置一般来说开发环境也不需要局域网内成员访问，所以大部分情况下是选择用b、私有网络来开发，这个可根据实际情况来选择。
2、共享目录，用户可以自定义共享目录，在Vagrantfile配置文件中配置：
#禁用原有的默认的共享目录
config.vm.synced_folder '.', '/vagrant', disabled: true
#增加新的共享目录，第二个参数以当前文件夹为基准
config.vm.synced_folder "abc", "/www/web/abc",
上方相当与windows下的vagrant_project/abc文件夹对应linux下的/www/web/abc文件夹
除了可以自定义共享文件外，还可以选择共享的方式，vagrant有四种共享的方式，官网文档请点这里查看
　　a、NFS共享方式，windows平台无法使用此配置，且用此方式时不能修改文件的所有者和所有组
　　b、RSync共享方式，所有平台都可以使用，但这种方式貌似有个缺点就是主机的改动可以同步到虚拟机中，但虚拟机中的改动无法同步到主机中，如果要实现双向同步可能还需做其它设置，由于没有用过，所以不太清楚。
　　c、SMB共享方式，只有windows平台可以使用，曾有找一些资料来配置，但一直没有成功，所以具体的并不太清楚，但貌似效率是挺不错的。
　　d、VirtualBox共享方式，这是VirtualBox提供的也是vagrant默认的共享方式。当文件数量小时用着不错，当文件数量大的时候打开网页会卡到超时。
总之以上，如果主机是mac，则推荐用NFS共享方式，如果是windows则推荐用smb或者rsync方式，如果文件很少，那用默认的就可以了。
但除了上方说的，windows下其实还有方式可以加快网站访问速度，那就是用Vagrant WinNFSd这款插件，插件地址点这里，这种方式是windows下提高速度最简单的方式了：
vagrant plugin install vagrant-winnfsd
安装完成后配置type为nfs，虽然官网上说nfs不能应用于windows，但用这个插件后就可以了。
config.vm.synced_folder "abc", "/www/web/abc",type:"nfs"
测试了下，此插件还是非常有效的，原先打开yii2框架的文件会超时，或10、20多秒，但用了这个后响应在2s以内，还是可以接受的。另提供一个参考资料：让Vagrant在Windwos下支持使用NFS/SMB共享文件夹从而解决目录共享IO缓慢的问题
 三、域名访问及打包分发
1、域名访问，主要是虚拟的本地域名配置问题
这里还是安装了lanmp_v3.1，具体的安装方法可以看以前的文章：Linux HYPERLINK "http://www.cnblogs.com/vishun/p/6089520.html" HYPERLINK "http://www.cnblogs.com/vishun/p/6089520.html"或者去下载官网上去找安装说明
新建一个站点，指定目录到共享的目录，随便取个虚拟的域名：www.abc.com
测试需要在主机中修改host，如下：（至于虚拟机中/etc/hosts文件中，测试修改与不修改都不影响访问）
192.168.33.10    www.abc.com
192.168.33.10    abc.com
然后直接在浏览器中用http://www.abc.com既可显示虚拟机中的内容了。而直接在windows中的的IDE修改文件，会实时通过这个网址展示出来。
2、打包分发
vagrant package

win10下在PowerShell中运行：
关闭当前centos系统
>vagrant halt
>vagrant package 

等待一段时间，会发现在目录生成一个package.box的文件，这个就是已经包含安装环境的镜像了，将此镜像发给同事，让其安装上方所属的步骤加载镜像vagrant box add {title} {url}命令，然后配置自己的网络及共享文件夹就可以直接开发了，远离重新配置环境的烦恼。
===================================================
vagrant管理虚拟机之虚拟机扩展磁盘、根目录

https://blog.csdn.net/zxjxingkong/article/details/62419379


1. 思路

    转换原始vmdk镜像为可扩展的vdi(虚拟磁盘镜像)镜像，扩展vdi，重新挂在虚拟磁盘，利用pv(物理卷)、vg(卷组)、lv(逻辑卷)实现根目录或其他目录的扩展。

2. 转换并扩展vdi

   停止虚拟机，此处test虚拟机为例

# vagrant halt test  
  转换镜像

# cd ~/VirtualBox\ VMs/test_1489122040823_24948  
# VBoxManage clonehd "box-disk1.vmdk" "clone-disk1.vdi" --format vdi  
  扩展镜像，此处以扩展到100G为例

# VBoxManage modifyhd "clone-disk1.vdi" --resize 102400  
  重新挂载磁盘到虚拟机，并启动虚拟机

# VBoxManage storageattach test_1489122040823_24948 --storagectl "SATA Controller" --port 0 --device 0 --type hdd --medium clone-disk1.vdi  
  启动虚拟机

# vagrant up  


3. 扩展到根目录
  进入虚拟机，查看磁盘，并格式化，此处以/dev/sda/ 为例

# fdisk -l  
# fdisk /dev/sda  
 a. 按p显示分区表，默认是 sda1 和 sda2。
  b. 按n新建主分区。
  c. 按p设置为主分区。
  d. 输入3设置为第三分区。
  e. 输入两次回车设置默认磁盘起始位置。
  f. 输入t改变分区格式
  g. 输入3选择第三分区
  h. 输入8e格式成LVM格式
  i. 输入w执行


  重新启动虚拟机


# reboot  
  创建物理卷

# pvcreate /dev/sda3  
  查看卷组，扩展到相应卷组，这里以centos为例

# vgdisplay  
# vgextend centos /dev/sda3  
  扩展到相应逻辑卷，这里以/dev/mapper/centos-root为例

# lvextend -l +100%FREE  /dev/mapper/centos-root  
  更新文件系统，centos7和centos6使用不同命令
  centos7


# xfs_growfs /dev/mapper/centos-root  
  centos6


# resize2fs /dev/mapper/centos-root  
  查看目录扩展是否成功
[plain] view plain copy
# df -h  
=============================================================================
https://blog.csdn.net/q12haha345678/article/details/78941570



Commands (CLI)
⦁	box
⦁	connect
⦁	destroy

⦁	global-status
⦁	halt
⦁	init
⦁	login
⦁	package
⦁	plugin
⦁	port
⦁	powershell
⦁	provision
⦁	rdp
⦁	reload
⦁	resume
⦁	share
⦁	snapshot
⦁	ssh
⦁	ssh-config
⦁	status
⦁	suspend
⦁	up 
vagrant up [name|id]
⦁	validate
⦁	version
⦁	More Commands
⦁	Machine Readable Output
docker-exec
docker-logs
docker-run
rsync
rsync-auto



