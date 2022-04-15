# Synok3s

## Gotchas

DSM 6.2.4 has iptables 1.6.0 installed. K3s does not like this version and it does not have the xt_comment capability.
Will need to rebuild iptables and several kernel modules. Would be nice to rebuild the kernel itself to add 

DSM 7.0 uses iptables 1.8.3 (much better)

## Prereqs


## K3S on Synology DSxxx NAS

Based on: https://medium.com/@marco.mezzaro/k3s-on-synology-what-if-it-works-e980b4b09fcb

## Compile missing kernel modules

Download from sourceforge kernel source and dsm toolchain (my ds218 has rtd129x)
- https://sourceforge.net/projects/dsgpl/files/Synology%20NAS%20GPL%20Source/22259branch/rtd1296-source/
- https://sourceforge.net/projects/dsgpl/files/DSM%206.2.2%20Tool%20Chains/Realtek%20RTD129x%20Linux%204.4.59/

from a brand new debian system:
create a folder:

```
sudo apt update
sudo apt install wget 
mkdir -p ~/dsm/archives 
cd ~/dsm
```
put:
- rtd1296-gcc494_glibc220_armv8-GPL.txz
- linux-4.4.x.txz

install all build deps:

```
sudo apt-get install mc make gcc build-essential kernel-wedge libncurses5 libncurses5-dev libelf-dev binutils-dev kexec-tools makedumpfile fakeroot lzma bc libssl-dev
```

extract and move txz archives away:

```
tar Jxvf linux-4.4.x.txz 
tar Jxvf rtd1296-gcc494_glibc220_armv8-GPL.txz 
mv linux-4.4.x.txz rtd1296-gcc494_glibc220_armv8-GPL.txz archives/
```

enter in kernel source folder and copy kernel config in place

```
cd linux-4.4.x/
cp synoconfigs/$codename .config
```

Export magic number version (if not exported could cause headaches!) and alias for dsm make


If your diskstation uses an Intel/AMD processor:
```
export LOCALVERSION=+
alias dsm6make='make ARCH=x86_64 CROSS_COMPILE=~/dsm/x86_64-pc-linux-gnu/bin/x86_64-pc-linux-gnu-'
```

If your diskstation uses an ARM processor:
```
export LOCALVERSION=+1
alias dsm6make='make ARCH=arm64 CROSS_COMPILE=~/dsm/aarch64-unknown-linux-gnueabi/bin/aarch64-unknown-linux-gnueabi-'
```


config the kernel, select all modules for iptables and make modules:

```
dsm6make menuconfig
dsm6make modules
find . -iname "*.ko" -type f
```

copy the kernel modules in /lib/modules on synology machine. `DO NOT OVERWRITE` already present modules.
use insmod command to load.
for troubleshooting check dmesg for errors.


## Links

## Specific practice sharing

* [v2rayA transparent proxy mode](https://github.com/sjtuross/syno-iptables/wiki/v2rayA transparent proxy mode)
* [Docker install OpenWrt bypass by](https://github.com/sjtuross/syno-iptables/wiki/Docker install OpenWrt bypass)
* [Native Docker IPv6 NAT Mode (DSM 6)](https://github.com/sjtuross/syno-iptables/wiki/Native Docker-IPv6-NAT Mode-(DSM-6))
* [Native Docker IPv6 NAT Mode (DSM 7)](https://github.com/sjtuross/syno-iptables/wiki/Native Docker-IPv6-NAT Mode-(DSM-7))

## grateful

* [Deploying a bypass transparent proxy for IPv6, Fullcone NAT in Synology](https://blog.kaaass.net/archives/1576)
* [spksrc - a cross compilation framework](https://github.com/SynoCommunity/spksrc)
* [fix synology docker ipv6 issue](https://github.com/wangliangliang2/fix_synology_docker_ipv6)

- https://github.com/sjtuross/syno-iptables/wiki/v2rayA%E9%80%8F%E6%98%8E%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F
- https://github.com/sjtuross/syno-iptables/wiki/Docker%E5%AE%89%E8%A3%85OpenWrt%E6%97%81%E8%B7%AF%E7%94%B1
- https://github.com/sjtuross/syno-iptables/wiki/%E5%8E%9F%E7%94%9FDocker-IPv6-NAT%E6%A8%A1%E5%BC%8F-(DSM-6)
- https://github.com/sjtuross/syno-iptables/wiki/%E5%8E%9F%E7%94%9FDocker-IPv6-NAT%E6%A8%A1%E5%BC%8F-(DSM-7)
- https://blog.kaaass.net/archives/1576
- https://github.com/SynoCommunity/spksrc
- https://github.com/wangliangliang2/fix_synology_docker_ipv6
- http://billauer.co.il/blog/2013/10/version-magic-insmod-modprobe-force/
- https://stackoverflow.com/questions/9341701/cross-compiling-a-kernel-module-invalid-module-format
