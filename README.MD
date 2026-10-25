适用于CD1000 2025年9月后设备(带浪潮LOGO) 的刷机idbloader和 uboot

使用下面命令将压缩包内文件直接写入到img后，再刷入cd1000，可以正常启动并稳定运行：
dd if=idbloader.img of=Armbian_26.11.0_rockchip_cd1000_resolute_6.1.157_server_2026.09.14.img bs=512 seek=64 conv=notrunc
dd if=1.uboot.img of=Armbian_26.11.0_rockchip_cd1000_resolute_6.1.157_server_2026.09.14.img bs=512 seek=16384 conv=notrunc

烧写后可能uboot可能会启动失败，这时候用串口线连接CD1000,手动在粘贴下面的命令可以启动：
```
setenv devtype mmc
setenv devnum 0
setenv load_addr "0x39000000"
setenv overlay_error "false"
setenv rootdev "/dev/mmcblk0p1"
setenv verbosity "1"
setenv console "both"
setenv bootlogo "false"
setenv rootfstype "ext4"
setenv rootflags "rw,errors=remount-ro"
setenv docker_optimizations "on"
setenv earlycon "off"
echo "Boot script loaded from ${devtype} ${devnum}"
load ${devtype} ${devnum} ${load_addr} ${prefix}armbianEnv.txt
env import -t ${load_addr} ${filesize}

setenv consoleargs "${consoleargs} console=tty1"

setenv consoleargs "${earlyconargs} ${consoleargs}"

part uuid mmc ${devnum}:1 partuuid

setenv bootargs "root=${rootdev} rootwait rootfstype=${rootfstype} rootflags=${rootflags} ${consoleargs} consoleblank=0 loglevel=${verbosity} usb-storage.quirks=${usbstoragequirks} ${extraargs} ${extraboardargs}"

load ${devtype} ${devnum} ${ramdisk_addr_r} ${prefix}uInitrd
load ${devtype} ${devnum} ${kernel_addr_r} ${prefix}Image

load ${devtype} ${devnum} ${fdt_addr_r} ${prefix}dtb/${fdtfile}

fdt addr ${fdt_addr_r}
fdt resize 65536
booti ${kernel_addr_r} ${ramdisk_addr_r} ${fdt_addr_r}
```
启动后重新在/boot目录下运行下面命令刷新boot.scr即可：
```
mkimage -C none -A arm -T script -n 'flatmax load script' -d /boot/boot.cmd /boot/boot.scr
```


如果这个帮到了您，请为我买杯奶茶
![打赏](https://raw.githubusercontent.com/mosmith1992/html5-qrcode-zxing-wasm/refs/heads/master/donote-qrcode.jpg)

业务介绍（这是个广告）：套打，报表，打印解决方案： https://www.xmreport.com





