# FreeZIO router firmware

OpenWRT Recovery method for ZIO Freezio 

```
U-boot default config for ZIO Freezio

bootcmd=tftp
bootdelay=5
baudrate=57600
ethaddr="00:AA:BB:CC:DD:10"
ipaddr=10.10.10.123
serverip=10.10.10.3
stdin=serial
stdout=serial
stderr=serial
```

## Information

* https://deviwiki.com/wiki/ZIO_FreeZIO (It can run WeVO W2914NS v2 firmware without any modification)
* https://openwrt.org/toh/hwdata/zio/zio_freezio (Be careful. 2021-03-27 Development Snapshot build bricked the router. Infinite reboot)


## Pinout

![pinout](/img/pinout.jpg)

Identical to WeVO W2914NS v2

```
# CH340G USB to TTL converter
brew tap adrianmihalko/ch340g-ch34g-ch34x-mac-os-x-driver https://github.com/adrianmihalko/ch340g-ch34g-ch34x-mac-os-x-driver
brew cask install wch-ch34x-usb-serial-driver
# Wire up and connect
ls /dev/tty.*
screen /dev/tty.usbserial-146130 57600 8N1
```

## Wiring 

I set up TFTP server with WeVO W2914NS v2 stable release. (https://downloads.openwrt.org/releases/19.07.7/targets/ramips/mt7621/openwrt-19.07.7-ramips-mt7621-w2914nsv2-squashfs-sysupgrade.bin)

![TFTP64](/img/tftp64.jpg)

* LAN port #4 was used to connect TFTP server
* serverip is 192.168.1.2 in my case, but you may use **10.10.10.3**


(But )

## Flashing

### 1. Enter 4(boot command line interface) and configure ip. reset.
```
   Load Address: 80001000
   Entry Point:  80001000
   Verifying Checksum ... OK
   Uncompressing Kernel Image ... LZMA ERROR 1 - must RESET board to recover

===================================================================
                MT7621   stage1 code 10:33:55 (ASIC)
                CPU=500000000 HZ BUS=166666666 HZ
==================================================================
Change MPLL source from XTAL to CR...
do MEMPLL setting..

...

Please choose the operation:
   1: Load system code to SDRAM via TFTP.
   2: Load system code then write to Flash via TFTP.
   3: Boot system code via Flash (default).
   4: Entr boot command line interface.
   7: Load Boot Loader code then write to Flash via Serial.
   9: Load Boot Loader code then write to Flash via TFTP.

You choosed 4


4: System Enter Boot Command Line Interface.

U-Boot 1.1.3 (May 25 2017 - 13:23:21)
MT7621 # printenv
bootcmd=tftp
bootdelay=5
baudrate=57600
ethaddr="00:AA:BB:CC:DD:10"
ipaddr=10.10.10.123
serverip=10.10.10.3
stdin=serial
stdout=serial
stderr=serial

Environment size: 149/4092 bytes
MT7621 # set ipaddr 192.168.1.1
MT7621 # set serverip 192.168.1.2
MT7621 # saveenv
Saving Environment to SPI Flash...

MT7621 # reset
```

### 2. Enter 2(Load system code then write to Flash via TFTP)

```
Please choose the operation:
   1: Load system code to SDRAM via TFTP.
   2: Load system code then write to Flash via TFTP.
   3: Boot system code via Flash (default).
   4: Entr boot command line interface.
   7: Load Boot Loader code then write to Flash via Serial.
   9: Load Boot Loader code then write to Flash via TFTP.                                                                                                                                                        4
You choosed 2
                                                                                                                                                                                                                 0


2: System Load Linux Kernel then write to Flash via TFTP.
 Warning!! Erase Linux in Flash then burn new one. Are you sure?(Y/N)
 Please Input new ones /or Ctrl-C to discard
        Input device IP (192.168.1.1) ==:192.168.1.1
        Input server IP (192.168.1.2) ==:192.168.1.2
        Input Linux Kernel filename (openwrt.bin) ==:openwrt.bin

 NetTxPacket = 0x87FE49C0

 KSEG1ADDR(NetTxPacket) = 0xA7FE49C0

 NetLoop,call eth_halt !

 NetLoop,call eth_init !
Trying Eth0 (10/100-M)

 Waitting for RX_DMA_BUSY status Start... done


 ETH_STATE_ACTIVE!!
TFTP from server 192.168.1.2; our IP address is 192.168.1.1
Filename 'openwrt.bin'.

 TIMEOUT_COUNT=10,Load address: 0x80100000
Loading: checksum bad
checksum bad
checksum bad
checksum bad
checksum bad
checksum bad
checksum bad
checksum bad
checksum bad
checksum bad
checksum bad
Got ARP REPLY, set server/gtwy eth addr (98:83:89:48:18:5a)
Got it
#################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         ############
done
Bytes transferred = 4718858 (48010a hex)
LoadAddr=80100000 NetBootFileXferSize= 0048010a
.....
```

Router boots. Press enter to get OpenWrt shell.
