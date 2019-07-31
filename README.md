# CentOS-Kernel-415  

(fork from https://github.com/liberal-boy/tcp_tsunami)  
CentOS kernel mainline v4.15 backup  
Use this repo for new version of BBR (tsunami)  


# tcp_tsunami  
tcp_tsunami adjust for kernel 4.13+（魔改版bbr，解决内核4.13+编译问题）  
## How to use  

### Update your kernel to 4.15 and make the default boot order (KVM CentOS7)  
1. Remove previous headers  

  `rpm -e --nodeps kernel-ml-headers`  
  
2. Install kernel 4.15  
```  
  yum install git -y  
  git clone -b master https://www.github.com/stardock/CentOS-Kernel-415  
  cd CentOS-Kernel-415  
  rpm -ivh kernel-ml-4.15.4-1.el7.elrepo.x86_64.rpm  
  rpm -ivh kernel-ml-devel-4.15.4-1.el7.elrepo.x86_64.rpm  
  rpm -ivh kernel-ml-headers-4.15.4-1.el7.elrepo.x86_64.rpm  
```  
3. Check the boot order and make default boot  

  `awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg`  
  
  `grub2-set-default 0`  

### Install tsunami  

1. Install `make gcc`. 安装`make gcc`。  
2. Install `elfutils-libelf-devel libelf-dev libelf-devel`. Centos7中需要安装  
3. Run 执行:  
```
wget https://raw.githubusercontent.com/liberal-boy/tcp_tsunami/master/tcp_tsunami.c  
echo "obj-m:=tcp_tsunami.o" > Makefile  
make -C /lib/modules/$(uname -r)/build M=`pwd` modules CC=/usr/bin/gcc  
insmod tcp_tsunami.ko  
cp -rf ./tcp_tsunami.ko /lib/modules/$(uname -r)/kernel/net/ipv4  
depmod -a  
modprobe tcp_tsunami  
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf  
echo "net.ipv4.tcp_congestion_control=tsunami" >> /etc/sysctl.conf  
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf  
sysctl -p  
```  

4. Check 检查 `sysctl net.ipv4.tcp_congestion_control`  

