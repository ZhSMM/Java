# mysql 环境搭建

1. 下载地址：

   > MySQL官方下载地址：[https://dev.mysql.com/downloads/](https://dev.mysql.com/downloads/)
   >
   > 可视化工具Navicat下载地址：[https://navicat.com/en/download/navicat-premium](https://navicat.com/en/download/navicat-premium)
   >
   > Navicat为试用版本，激活方法地址：[https://github.com/DoubleLabyrinth/navicat-keygen/blob/windows/doc/how-to-use.windows.md](https://github.com/DoubleLabyrinth/navicat-keygen/blob/windows/doc/how-to-use.windows.md)

2. 环境变量设置：将安装目录写入path；同时，默认端口号：3306；

3. 环境变量：把MySql的安装变量写入path变量；

4. Linux安装：rpm -rvh  RPM软件名（--force --nodoeps,如果报错加上）；

   如果有冲突：yum -y remove +冲突文件； 

   启动：service mysql start;

   关闭：service mysql stop;

   重启：service mysql restart;

   
