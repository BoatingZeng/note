## Linux命令
1.  查看进程：ps -ef | grep mysql
2.  杀进程：kill -s 9 1827
3.  查看文件尾：tail file.log -n 300

## unbuntu开机启动sh
svn为例：
1.  在/etc/init.d写个脚本startsvn.sh
```
    #!/bin/bash
    svnserve -d -r /home/svn
```
2. chmod 777 startsvn.sh  设置启动权限
3. update-rc.d startsvn.sh defaults

## 亮度
sudo su
echo 50 > /sys/class/backlight/intel_backlight/brightness
