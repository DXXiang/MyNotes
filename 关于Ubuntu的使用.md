# 关于Ubuntu的使用

## 卸载软件

```shell
# 删除软件及其配置文件
apt-get --purge remove 软件名
# 删除没用的依赖包
apt-get autoremove
# 此时dpkg的列表中有“rc”状态的软件包，可以执行如下命令做最后清理：
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

## 下载、更新软件和包

* 添加源

  ```shell
  sudo add-apt-repository ppa:.....
  ```

* 删除源

  ```shell
  sudo add-apt-repository -r ppa:.....
  ```

  

* 更新源

  ```shell
  sudo apt update
  ```

* 下载

  ```shell
  sudo apt install 软件名
  ```

* 更新所有软件

  ```shell
  sudo apt upgrade
  ```

## 添加、删除源

**添加 PPA 源** 
 　　添加 PPA 源的命令为：`sudo add-apt-repository ppa:user/ppa-name` 
 　　添加好记得要更新一下： `sudo apt-get update`

**删除 PPA 源** 
 　　删除 PPA 源的命令格式则为：`sudo add-apt-repository -r ppa:user/ppa-name` 
 　　然后进入 /etc/apt/sources.list.d 目录，将相应 ppa 源的保存文件删除。 
 　　最后同样更新一下：`sudo apt-get update`



# 已安装的软件

* Typora
* Guake
* Mysql
* MysqlWorkbench
* 网易云音乐
* IDEA
* BitWarden
* Docky
* htop
* synaptic
* indicate-sysmonitor
* audatious
* JD-GUI
* notepad++
* shutter
* Teamviewer
* shadowsocks
* lolcat
* cmatrix

# 设置开机启动桌面应用程序

* press Alt+F2 and run the gnome-session-propertiescommand.
* 按下 Alt+F2打开命令框，输入gnome-session-propertiescommand
* 在打开的窗口单击添加并输入启动程序命令（名称和注释是可选的）。例如，添加火狐为自启动程序，在 命令 输入框内输入firefox 并点击 添加确认。