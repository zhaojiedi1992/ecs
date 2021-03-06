# Partition and format data disk more than 2 TiB {#concept_i15_qpc_ydb .concept}

This article describes how to partition and format a large data disk in different operating systems using the GPT format. Note that a large data disk indicates a disk greater than 2 TiB, while a disk size smaller than 2 TiB indicates a small data disk.

**Note:** If you want to partition and format a data disk less than 2 TiB, please see [Format a data disk for Linux instances](../../../../reseller.en-US/Quick Start for Entry-Level Users/Step 4. Format a data disk/Format a data disk for Linux instance.md#) and [Format a data disk for Windows instances](../../../../reseller.en-US/Quick Start for Entry-Level Users/Step 4. Format a data disk/Format a data disk for Windows instances.md#).

## Note {#section_xmm_psc_ydb .section}

Before partition and formatting a large data disk, note the following:

-   Large data disks support the partition tools and file systems shown in the following table.

    |Operating system|Partition tool|File system|
    |:---------------|:-------------|:----------|
    |Linux|`parted`|ext4 or xfs|
    |Windows|**Disk management**|NTFS|

-   **We do not recommend that you create a large data disk by using a snapshot of a small data disk. **

    If you create a large data disk by using the snapshot of a small data disk, the following risks may occur:

    -   he system expands the block-level of the device's disk, but does not automatically convert between the partition format and the file system.
    -   If the MBR format is used in the snapshot of the small data disk, none of the supported partition tools \(`parted`on Linux and  **Disk Management**on Windows\) can convert the MBR to GPT and retain the data.  Therefore, even if you create a large data disk by using a snapshot of a small data disk, while partitioning and initializing, you must delete the original data and partition with the GPT format.  If you have created large data disk by using a snapshot of a small data disk, see [use windows to partition and format a large data disk created by a snapshot of a small data disk.](#Windows2012Snapshot) .

        **Note:** This is not the case if the snapshot of the small data disk is in GPT format, or if you have another powerful partitioning tool. You can select based on your own situation.

    Instead, create an empty large data disk, or create large data disk by using snapshots of large data disks, because of the following reasons

-   **Effect of data disk snapshots**

    No matter if you are using a large data disk or a small data disk, the process for creating a snapshot from a data disk is proportional to the total data volume of the disk. If the disk contains a large amount of compromised or damaged data, or residual data fragments, then the snapshot creation time is extended


## Use Windows to partition and format an empty large data disk {#section_enm_psc_ydb .section}

The following examples uses a Windows Server 2008 R2 64-bit operating system to describe how to partition and format a large data disk in Windows instance. Assume the data disk to be processed is a 4 TiB empty disk.

**Prerequisites**

The data disk has been attached to an instance. For more information, see [attach a cloud disk](reseller.en-US/User Guide/Cloud disks/Attach a cloud disk.md#).

**Procedure**

To partition and format a large data disk, follow these steps:

1.  [Connect to a Windows instance](reseller.en-US/User Guide/Connect to instances/Overview.md#).
2.  Click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9672/15414313534424_en-US.png) icon in the task bar.
3.  In the left-side navigation pane select **Storage** \> **Disk Management**.
4.  Find the disk that is to be partitioned and formatted \(in this example, **Disk 4**\). The disk status should be shown as **Offline**.
5.  Right click the blank area around Disk 4, and then click **Online**.

    After going online, Disk 4 enters the **Not Initialized** status.

6.  Right click the blank area around Disk 4, and then select **Initialize Disk** in the context menu.
7.  In the Initialize Disk dialog box, select **Disk 4** and select **GPT** as the disk partitioning method.
8.  In the Disk Management window, right click the **Unallocated** area of Disk 4, and then select **New Simple Volume** to create a 4 TiB volume in the NTFS format.
9.  In the New Simple Volume Wizard, follow these steps:
    1.  Click **Next**.
    2.  Choose a volume size: designate size of simple volume. If you want to create a master area only, use the default value. Click **Next**. You can also partition **Disk 4**  into several partitions.

        **Note:** The maximum NTFS volume is, theoretically, the maximum volume of NTFS containing 264-1 clusters. However, in Windows XP  Pro, the maximum volume of NTFS is 232-1 clusters. For example, for a 64 KiB cluster, the maximum NTFS volume is approximately 256 TiB. If you select a 4 KiB cluster, the maximum NTFS volume is 16  TiB. NTFS selects the size of a cluster automatically based on the disk capacity.

    3.  Assign drive letter and path: select a drive letter, then select G in this instance. Click **Next**.
    4.  Format Partition: Select the formatting settings, including file system, distributed unit size, and volume label, and then confirm whether to **Perform a quick format** and **Enable file and folder compression**. Select **Perform a quick format** here only. Click **Next**.
    5.  Start creating a new simple volume by following the prompts displayed by the wizard. Then, click **Finish**.

After the formatted partition is completed, in **Disk Management**, the status of **Disk 4** is shown in the following screenshot.

## Use Windows to partition and format a large data disk created by a snapshot of a small data disk {#Windows2012Snapshot .section}

If you create a large data disk by using snapshots of a small data disk, you first need to convert the partition format of data disk from MNR to GPT, and then format the data disk. However, because data of the original snapshots is saved, we recommend you do not create large data disk by using a snapshot of a small data disk.

If you have already created large data disks in this method, perform the following actions to partition and format this data disk. The following example operating system uses a Windows Server 2012 R2 64-bit operating system. Assume the data disk to be processed is a 3 Tib disk.

**Prerequisites**

The data disk has been [attached](reseller.en-US/User Guide/Cloud disks/Attach a cloud disk.md#) to an instance.

**Procedure**

To partition and format a large data disk, follow these steps:

1.  [Connect to a Windows instance](reseller.en-US/User Guide/Connect to instances/Overview.md#).
2.  On the Windows Server desktop, right click the **Start** icon, and select **Disk Management**.

    The data disk \(Disk 2, in this example\) that has not been formatted or partitioned is in the **Offline** status.

3.  Right click the blank area around Disk 2, and then select **Offline** in the context menu.
4.  Right click a simple volume, and then select **Delete Volume** in the context menu.
5.  Right click the blank area around Disk 2, and then select **Convert to GPT Disk** in the context menu.
6.  In the Disk Management window, right click **Unallocated** area of Disk 2, and then select **New Simple Volume** to create a 3 TiB volume in the NTFS format.
7.  In the New Simple Volume Wizard, follow these steps:
    1.  Click **Next**.
    2.  Specify the size of the simple volume.  If you need only one primary partition, use the default value,  and then click **Next**. You can also partition **Disk 2** into several partitions.

        **Note:** The maximum NTFS volume is, theoretically, the maximum volume of NTFS containing 264-1 clusters.  Howver, in Windows XP Pro,  the maximum volume of NTFS is 232-1 clusters.  For example, for a 64 KiB cluster,  the maximum NTFS volume is approximately 256 TiB. If you select a 4 KiB cluster, the maximum NTFS volume is 16  TiB. NTFS selects the size of a cluster automatically based on the disk capacity.

    3.  Assign Drive Letter or Path: Select a drive letter, and then click **Next**.
    4.  Format Partition: Select the formatting settings, including file system, distributed unit size and volume label, and then confirm whether to **Perform a quick format** and **Enable file and folder compression**. Select **Perform a quick format** here only. Click **Next**.
    5.  Start creating a new simple volume by following the prompts displayed by the wizard. Then, click **Finish**.

After the formatted partition is completed, in **Disk Management**, the status of **Disk 4** is shown in the following screenshot.

## Use Linux to partition and format a large data disk {#section_a4m_psc_ydb .section}

To partition and format a large data disk that is attached to a Linux instance, use the GPT format.  In Linux system, large data disks normally uses xfs or ext4 file system. 

The following example uses a CentOS 7.4 64-bit operating system.This section describes how to use **parted** and **e2fsprogs** tools to partition and format a large data disk on a Linux instance. Assume the data disk to be processed is an empty 3 TiB new disk, and the device name is  /dev/vdd.

**Prerequisites**

Your Linux instance has installed **parted**. If not, run `yum install -y parted`.

Your Linux instance has installed **e2fsprogs**. If not, run `yum install -y e2fsprogs`.

The data disk has been attached to the instance. For more information, see [Attach a cloud disk](reseller.en-US/User Guide/Cloud disks/Attach a cloud disk.md#).

**Procedure**

To partition and format a large data disk and mount the file system, follow these steps:

1.  Run `fdisk -l` to check whether the data disk exists. If the data disk is successfully mounted, the following result is returned

    ```
    Disk /dev/vdd: 3221.2 GB, 3221225472000 bytes, 6291456000 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    ```

2.  Run `parted /dev/vdd` to start partitioning:
    1.  Run `mklabel gpt`, to convert partitioning format from MBR to GPT.
    2.  Run  `mkpart primary ext4 <StartSector> <EndSector>` to partition a primary partition by using the ext4 file system, and specify a start sector and end sector for the partition. If a data disk is partitioned into one partition only, run `mkpart primary ext4 0 -1`.

        **Note:** You can also use xfs file system.

    3.  Run  `print` to check partition table.

        ```
        (parted) mkpart primary ext4 0 -1
        Warning: The resulting partition is not properly aligned for best performance.
        Ignore/Cancel? ignore
        (parted) print
        Model: Virtio Block Device (virtblk)
        Disk /dev/vdd: 3221 GB
        Sector size (logical/physical): 512B/512B
        Partition Table: gpt
        Disk Flags:
        Number Start End Size File system Name Flags
        1 17.4kB 3221GB 3221GB primary
        ```

    4.  Run `quit`to exit **parted** .
3.  Run `partprobe` to make system re-read the partition table.
4.  Run the following commands to create an ext4 file system, and make /dev/vdd1 partition use ext4.

    ```
    mke2fs -O 64bit,has_journal,extents,huge_file,flex_bg,uninit_bg,dir_nlink,extra_isize /dev/vdd1
    ```

    **Note:** 

    -   If you want to disable the lazy init function of ext4 file system to avoid its effect on data disk I/O performance, see [Appendix 2: Disable lazy  init function.](#LazyInit).
    -   If capacity of the data disk is 16 TiB, you must format it by using e2fsprogs in the designated version. See [Appendix 1: Update e2fsprogs](#e2fsprogs).
    -   If you want to create an xfs file system, run `mkfs -t xfs /dev/vdd1`.
5.  Run `mkdir /test` to create a mount point with the name /test.
6.  Run `mount /dev/vdd1 /test` to mount /dev/vdd1 to /test.
7.  Run `df -h` to check current disk space and usage. 

    If the returned result shows the newly created file system information, the mount operation was successful, and you can use the new file system directly \(that is, you do not need to restart the instance\).

    ```
    [root@izXXXXz ~]# df -h
    Filesystem Size Used Avail Use% Mounted on
    /dev/vda1 40G 6.4G 31G 18% /
    devtmpfs 487M 0 487M 0% /dev
    tmpfs 497M 0 497M 0% /dev/shm
    tmpfs 497M 364K 496M 1% /run
    tmpfs 497M 0 497M 0% /sys/fs/cgroup
    tmpfs 100M 0 100M 0% /run/user/0
    /dev/vdd1 2.9T 89M 2.8T 1% /test
    ```

8.  \(Optional\) Write new partition information to /etc/fstab  to enable automatic mount partition while the instance is started.
    1.  \(Optional\) Run `cp /etc/fstab /etc/fstab.bak` to back up  etc/fstab.
    2.  Run  `echo /dev/vdd1 /test ext4 defaults 0 0 >> /etc/fstab` to write new partition information to /etc/fstab.
    3.  Run `cat /etc/fstab` to check /etc/fstab  information.

        If the new partition information is in the returned result, the write operation was successful.


**Appendix 1: Update e2fsprogs**

If the disk capacity is 16  TiB, you must use e2fsprogs of version 1.42 or later to format its partitions to ext4 file system.  If e2fsprogs version is too low \(for example, e2fsprogs  1.41.11\), the following error occurs.

```
mkfs.ext4: Size of device /dev/vdd too big to be expressed in 32 bits using a blocksize of 4096.

```

To install e2fsprogs of later version, such as 1.42.8 in this example, follow these steps:

1.  Run `rpm -qa | grep e2fsprogs` to check the current version of e2fsprogs.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9672/15414313534439_en-US.png)

    If the current version is earlier than 1.42, update the software by following these steps.

2.  Run the following command to download e2fsprogs in version1.42.8. Go to [e2fsprogs](https://www.kernel.org/pub/linux/kernel/people/tytso/e2fsprogs/v1.42.8/?spm=a2c4g.11186623.2.14.Pb5baW) to find the latest software package.

    ```
    wget https://www.kernel.org/pub/linux/kernel/people/tytso/e2fsprogs/v1.42.8/e2fsprogs-1.42.8.tar.gz
    
    ```

3.  Run the following commands in turn to compile tools in later versions.

    ```
    tar xvzf e2fsprogs-1.42.8.tar.gz
    cd e2fsprogs-1.42.8
    ./configure
    make
    make install
    ```

4.  Run `rpm -qa | grep e2fsprogs` to check whether the software of the later version has been installed successfully.

**Appendix 2: Disable lazy init function**

The lazy init function of ext4 file system is enabled by default. While the function is enabled, it will run in the system background and initiate a thread to initialize metadata of ext4 file system continuously to delay metadata initialization. Therefore, immediately after formatting a data disk, IOPS can be affected.

If you need to test performance of data disk immediately after formatting, run the following commands to disable lazy init function while formatting the file system.

```
mke2fs -O 64bit,has_journal,extents,huge_file,flex_bg,uninit_bg,dir_nlink,extra_isize -E lazy_itable_init=0,lazy_journal_init=0   /dev/vdd1
```

If the lazy init is disabled, it may take longer time to format a partition. For example, it may take 10−30 minutes to format a 32 TiB data disk.

