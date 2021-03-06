# 安装 virtio 驱动 {#concept_dvq_cqs_xdb .concept}

为避免部分服务器、虚拟机或者云主机的操作系统 [在控制台上导入镜像](intl.zh-CN/用户指南/镜像/导入镜像/在控制台上导入镜像.md#) 后，使用该自定义镜像创建的 ECS 实例无法启动，您需要在导入镜像前检查是否需要在源服务器中安装 Xen（pv）或 virtio 驱动，本文档主要以安装 virtio 驱动为说明。

## 无需安装 virtio 驱动的镜像 {#section_lby_pqs_xdb .section}

从本地 [在控制台上导入镜像](intl.zh-CN/用户指南/镜像/导入镜像/在控制台上导入镜像.md#) 时，阿里云会自动处理导入的自定义镜像的 virtio 驱动的操作系统有：

-   Windows Server 2008
-   Windows Server 2012
-   Windows Server 2016
-   CentOS 6/7
-   Ubuntu 12/14/16
-   Debian 7/8/9
-   SUSE 11/12

以上列表的镜像，默认已安装 virtio 驱动的系统，需要注意 [修复临时文件系统](#RecoverTheInitramfs)。

## 需要安装 virtio 驱动的镜像 {#section_nqx_yqs_xdb .section}

其他不在以上列表的操作系统，您需要在导入镜像之前，为源服务器安装 virtio 驱动。

**检查服务器内核是否支持 virtio 驱动**

1.  运行 `grep -i virtio /boot/config-$(uname -r)` 检查当前操作系统的内核是否支持 virtio 驱动。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9707/4632_zh-CN.png)

    **说明：** 

    -   如果在输出信息中没有找到 VIRTIO\_BLK 及 VIRTIO\_NET 的信息，表示该操作系统没有安装 virtio 相关驱动，暂时不能直接导入阿里云云平台。您需要为您的服务器 [编译安装 virtio 驱动](#compile)。
    -   如果参数 CONFIG\_VIRTIO\_BLK 及 CONFIG\_VIRTIO\_NET 取值为 y，表示包含了 virtio 驱动，您可以 参阅 [导入镜像注意事项](intl.zh-CN/用户指南/镜像/导入镜像/导入镜像注意事项.md#) 直接 [在控制台上导入镜像](intl.zh-CN/用户指南/镜像/导入镜像/在控制台上导入镜像.md#) 到阿里云。
    -   如果参数 CONFIG\_VIRTIO\_BLK 及 CONFIG\_VIRTIO\_NET 取值为 m，需要进入第 2 步。
2.  执行命令 `lsinitrd /boot/initramfs-$(uname -r).img | grep virtio`确认 virtio 驱动是否包含在临时文件系统 initramfs 或者 initrd 中。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9707/4633_zh-CN.png)

    **说明：** 

    -   截图表明，initramfs 已经包含了 virtio\_blk 驱动，以及其所依赖的virtio.ko、virtio\_pci.ko 和 virtio\_ring.ko，您可以参阅 [导入镜像注意事项](intl.zh-CN/用户指南/镜像/导入镜像/导入镜像注意事项.md#) 直接 [在控制台上导入镜像](intl.zh-CN/用户指南/镜像/导入镜像/在控制台上导入镜像.md#) 到阿里云。
    -   如果临时文件系统 initramfs 没有包含 virtio 驱动，则需要修复临时文件系统。

**修复临时文件系统**

通过 [检查](#Check)，发现源服务器内核支持 virtio 驱动，但是临时文件系统 initramfs 或者 initrd 中没有包含 virtio 驱动时，需要修复临时文件系统。以 CentOS 等为例。

-   CentOS/RedHat 5

    ```
    
    mkinitrd -f --allow-missing \
    --with=xen-vbd --preload=xen-vbd \
    --with=xen-platform-pci --preload=xen-platform-pci \
    --with=virtio_blk --preload=virtio_blk \
    --with=virtio_pci --preload=virtio_pci \
    --with=virtio_console --preload=virtio_console \
    ```

-   CentOS/RedHat 6/7

    ```
    
    mkinitrd -f --allow-missing \
    --with=xen-blkfront --preload=xen-blkfront \
    --with=virtio_blk --preload=virtio_blk \
    --with=virtio_pci --preload=virtio_pci \
    --with=virtio_console --preload=virtio_console \
    /boot/initramfs-$(uname -r).img $(uname -r)
    ```

-   Debian/Ubuntu

    ```
    
    echo -e 'xen-blkfront\nvirtio_blk\nvirtio_pci\nvirtio_console' >> \
    /etc/initramfs-tools/modules
    mkinitramfs -o /boot/initrd.img-$(uname -r)"
    ```


## 编译安装 virtio 驱动 {#compile .section}

此处以 Redhat 服务器为例，为您示范如何编译安装 virtio 驱动。

**下载内核安装包**

1.  运行 `yum install -y ncurses-devel gcc make wget` 安装编译内核的必要组件。
2.  运行 `uname -r` 查询当前系统使用的内核版本，如示例中的 4.4.24-2.a17.x86\_64。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9707/4634_zh-CN.png)

3.  前往 [Linux 内核列表页面](https://www.kernel.org/pub/linux/kernel/) 下载对应的内核版本源码，如示例中的 4.4.24 开头的 linux-4.4.24.tar.gz 的网址为 [https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.4.24.tar.gz](https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.4.24.tar.gz)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9707/4638_zh-CN.png)

4.  运行 `cd /usr/src/` 切换目录。
5.  运行 `wget https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.4.24.tar.gz` 下载安装包。
6.  运行 `tar -xzf linux-4.4.24.tar.gz` 解压安装包。
7.  运行 `ln -s linux-4.4.24 linux` 建立链接。
8.  运行 `cd /usr/src/linux` 切换目录。

**编译内核**

1.  依次运行以下命令编译内核。

    ```
    
    make mrproper
    symvers_path=$(find /usr/src/ -name "Module.symvers")
    test -f $symvers_path && cp $symvers_path .
    cp /boot/config-$(uname -r) ./.config
    make menuconfig
    ```

2.  出现以下界面时，开始打开 virtio 相关配置：

    **说明：** 选 \* 配置表示编译到内核，选 m 配置表示编译为模块。

    1.  使用空格勾选 Virtualization 项。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9707/4639_zh-CN.png)

        确认是否勾选了 KVM（Kernel-based Virtual Machine） 选项。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9707/4640_zh-CN.png)

        ```
        
        Processor type and features --->
        [*] Paravirtualized guest support --->
        --- Paravirtualized guest support
        (128) Maximum allowed size of a domain in gigabytes
        [*] KVM paravirtualized clock
        [*] KVM Guest support
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9707/4641_zh-CN.png)

        ```
        
        Device Drivers --->
        [*] Block devices --->
         Virtio block driver (EXPERIMENTAL)
        -*- Network device support --->
         Virtio network driver (EXPERIMENTAL)
        ```

    2.  按下 Esc 键退出内核配置界面并根据弹窗提示保存 .config 文件。
    3.  [检查](#Check) virtio 相关配置是否已经正确配置。
    4.  （可选）若 [检查](#Check) 后发现暂未设置 virtio 相关配置，运行以下命令手动编辑 .config 文件。

        ```
        
        make oldconfig
        make prepare
        make scripts
        make
        make install
        ```

    5.  运行以下命令查看 virtio 驱动的安装情况。

        ```
        
        find /lib/modules/"$(uname -r)"/ -name "virtio.*" | grep -E "virtio.*"
        grep -E "virtio.*" < /lib/modules/"$(uname -r)"/modules.builtin
        ```

        **说明：** 如果任一命令输出 virtio\_blk、virtio\_pci.virtio\_console 等文件列表，表明您已经正确安装了 virtio 驱动。


## 后续操作 {#section_e12_gws_xdb .section}

检查 virtio 驱动后，您可以 [使用迁云工具迁移服务器至阿里云](https://www.alibabacloud.com/help/doc-detail/62394.htm)。

