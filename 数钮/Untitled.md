## Linux 安装 jdk

​	可参考：https://www.yuque.com/maju/nft0bu/qz184v

​	（由于权限问题，使用wget 直接下载可能出现无法解压的问题，需要提前下载好，然后将jdk文件拷贝到服务器上，U盘挂载可参考下面的Linux挂载）



## linux挂载

* **查看u disk 位置**

  ```
  fdisk -l 
  ```

  一般为 sdb1 #若无反应，申请root权限

* **挂载u disk 到/mnt**

  ```
  mount  /dev/sdb1 /mnt  
  ```

* **查看是否加载**

  ```
  ls /mnt
  ```





