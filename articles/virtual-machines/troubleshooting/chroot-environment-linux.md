---
title: Ambiente de cromo num VM de resgate linux.
description: Este artigo descreve como resolver problemas no ambiente de cromos na máquina virtual Rescue (VM) em Linux.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: 0d8a030061ef6aa848344152edaa3267ad916e2a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377943"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Ambiente cromo em um VM de resgate linux

Este artigo descreve como resolver problemas no ambiente de cromos na máquina virtual Rescue (VM) em Linux.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16.x && Ubuntu 18.x

1. Parar ou desatribuer o VM afetado.
1. Crie uma imagem VM de resgate da mesma versão OS, no mesmo grupo de recursos (RSG) e localização utilizando o disco gerido.
1. Utilize o portal Azure para tirar uma fotografia do disco de oss da máquina virtual afetada.
1. Crie um disco a partir da imagem do disco de so e prenda-o ao VM de resgate.
1. Uma vez criado o disco, resolva o ambiente de cromo no VM de resgate.

   1. Aceda ao seu VM como utilizador raiz utilizando o seguinte comando:

      `sudo su -`

   1. Encontre o disco utilizando `dmesg` (o método que utiliza para descobrir o seu novo disco pode variar). O exemplo a seguir utiliza **o dmesg** para filtrar em discos **SCSI:**

      `dmesg | grep SCSI`

      A sua saída será semelhante ao seguinte exemplo. Neste exemplo, queremos o disco **sdc:**

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Utilize os seguintes comandos para aceder ao ambiente cromoso:

      ```
      mkdir /rescue
      mount /dev/sdc1 /rescue
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Resolver problemas no ambiente de cromos.

   1. Utilize os seguintes comandos para sair do ambiente croâmado:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue
      ```

      > [!NOTE]
      > Se receber o `unable to unmount /rescue` erro, adicione a opção -l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Retire o disco do VM de resgate e efetue uma troca de disco com o VM original.
1. Inicie o VM original e verifique a sua conectividade.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/Centos/Oráculo 6.x && Oráculo 8.x && RHEL/Centos 7.x com Partições RAW

1. Parar ou desatribuer o VM afetado.
1. Crie uma imagem de Rescue VM da mesma versão SO, no mesmo grupo de recursos (RSG) e localização utilizando o disco gerido.
1. Utilize o portal Azure para tirar uma fotografia do disco de oss da máquina virtual afetada.
1. Crie um disco a partir da imagem do disco de so e prenda-o ao VM de resgate.
1. Uma vez criado o disco, resolva o ambiente de cromo no VM de resgate.

   1. Aceda ao seu VM como utilizador raiz utilizando o seguinte comando:

      `sudo su -`

   1. Encontre o disco utilizando `dmesg` (o método que utiliza para descobrir o seu novo disco pode variar). O exemplo a seguir utiliza **o dmesg** para filtrar em discos **SCSI:**

      `dmesg | grep SCSI`

      A sua saída será semelhante ao seguinte exemplo. Neste exemplo, queremos o disco **sdc:**

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Utilize os seguintes comandos para aceder ao ambiente cromoso:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Resolver problemas no ambiente de cromos.

   1. Utilize os seguintes comandos para sair do ambiente croâmado:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/
      umount /rescue
      ```

      > [!NOTE]
      > Se receber o `unable to unmount /rescue` erro, adicione a opção -l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Retire o disco do VM de resgate e efetue uma troca de disco com o VM original.
1. Inicie o VM original e verifique a sua conectividade.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/Centos 7.x com LVM

   > [!NOTE]
   > Se o seu VM original incluir o Gestor de Volume Lógico (LVM) no disco oss, crie o Rescue VM utilizando a imagem com divisórias cruas no disco OS.

1. Parar ou desatribuer o VM afetado.
1. Crie uma imagem de Rescue VM da mesma versão SO, no mesmo grupo de recursos (RSG) e localização utilizando o disco gerido.
1. Utilize o portal Azure para tirar uma fotografia do disco de oss da máquina virtual afetada.
1. Crie um disco a partir da imagem do disco de so e prenda-o ao VM de resgate.
1. Uma vez criado o disco, resolva o ambiente de cromo no VM de resgate.

   1. Aceda ao seu VM como utilizador raiz utilizando o seguinte comando:

      `sudo su -`

   1. Encontre o disco utilizando `dmesg` (o método que utiliza para descobrir o seu novo disco pode variar). O exemplo a seguir utiliza **o dmesg** para filtrar em discos **SCSI:**

      `dmesg | grep SCSI`

      A sua saída será semelhante ao seguinte exemplo. Neste exemplo, queremos o disco **sdc:**

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Utilize o seguinte comando para ativar o grupo de volume lógico:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Use o `lsblk` comando para recuperar os nomes lvm:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Utilize os seguintes comandos para aceder ao ambiente cromoso:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/mapper/rootvg-optlv /rescue/opt
      mount /dev/sdc2 /rescue/boot/
      mount /dev/sdc1 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Resolver problemas no ambiente de cromos.

   1. Utilize os seguintes comandos para sair do ambiente croâmado:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue/opt
      umount /rescue
      ```

      > [!NOTE]
      > Se receber o `unable to unmount /rescue` erro, adicione a opção -l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Retire o disco do VM de resgate e efetue uma troca de disco com o VM original.
1. Inicie o VM original e verifique a sua conectividade.

## <a name="rhel-8x-with-lvm"></a>RHEL 8.x com LVM

   > [!NOTE]
   > Se o seu VM original incluir o Gestor de Volume Lógico (LVM) no disco oss, crie o Rescue VM utilizando a imagem com divisórias cruas no disco OS.

1. Parar ou desatribuer o VM afetado.
1. Crie uma imagem de Rescue VM da mesma versão SO, no mesmo grupo de recursos (RSG) e localização utilizando o disco gerido.
1. Utilize o portal Azure para tirar uma fotografia do disco de oss da máquina virtual afetada.
1. Crie um disco a partir da imagem do disco de so e prenda-o ao VM de resgate.
1. Uma vez criado o disco, resolva o ambiente de cromo no VM de resgate.

   1. Aceda ao seu VM como utilizador raiz utilizando o seguinte comando:

      `sudo su -`

   1. Encontre o disco utilizando `dmesg` (o método que utiliza para descobrir o seu novo disco pode variar). O exemplo a seguir utiliza **o dmesg** para filtrar em discos **SCSI:**

      `dmesg | grep SCSI`

      A sua saída será semelhante ao seguinte exemplo. Neste exemplo, queremos o disco **sdc:**

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Utilize o seguinte comando para ativar o grupo de volume lógico:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Use o `lsblk` comando para recuperar os nomes lvm:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Utilize os seguintes comandos para aceder ao ambiente cromoso:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Resolver problemas no ambiente de cromos.

   1. Utilize os seguintes comandos para sair do ambiente croâmado:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue
      ```

      > [!NOTE]
      > Se receber o `unable to unmount /rescue` erro, adicione a opção -l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Retire o disco do VM de resgate e efetue uma troca de disco com o VM original.
1. Inicie o VM original e verifique a sua conectividade.

## <a name="oracle-7x"></a>Oráculo 7.x

1. Parar ou desatribuer o VM afetado.
1. Crie uma imagem de Rescue VM da mesma versão SO, no mesmo grupo de recursos (RSG) e localização utilizando o disco gerido.
1. Utilize o portal Azure para tirar uma fotografia do disco de oss da máquina virtual afetada.
1. Crie um disco a partir da imagem do disco de so e prenda-o ao VM de resgate.
1. Uma vez criado o disco, resolva o ambiente de cromo no VM de resgate.

   1. Aceda ao seu VM como utilizador raiz utilizando o seguinte comando:

      `sudo su -`

   1. Encontre o disco utilizando `dmesg` (o método que utiliza para descobrir o seu novo disco pode variar). O exemplo a seguir utiliza **o dmesg** para filtrar em discos **SCSI:**

      `dmesg | grep SCSI`

      A sua saída será semelhante ao seguinte exemplo. Neste exemplo, queremos o disco **sdc:**

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Utilize os seguintes comandos para aceder ao ambiente cromoso:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Resolver problemas no ambiente de cromos.

   1. Utilize os seguintes comandos para sair do ambiente croâmado:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Se receber o `unable to unmount /rescue` erro, adicione a opção -l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Retire o disco do VM de resgate e efetue uma troca de disco com o VM original.
1. Inicie o VM original e verifique a sua conectividade.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 Para SAP && ## SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 PARA SAP

1. Parar ou desatribuer o VM afetado.
1. Crie uma imagem de Rescue VM da mesma versão SO, no mesmo grupo de recursos (RSG) e localização utilizando o disco gerido.
1. Utilize o portal Azure para tirar uma fotografia do disco de oss da máquina virtual afetada.
1. Crie um disco a partir da imagem do disco de so e prenda-o ao VM de resgate.
1. Uma vez criado o disco, resolva o ambiente de cromo no VM de resgate.

   1. Aceda ao seu VM como utilizador raiz utilizando o seguinte comando:

      `sudo su -`

   1. Encontre o disco utilizando `dmesg` (o método que utiliza para descobrir o seu novo disco pode variar). O exemplo a seguir utiliza **o dmesg** para filtrar em discos **SCSI:**

      `dmesg | grep SCSI`

      A sua saída será semelhante ao seguinte exemplo. Neste exemplo, queremos o disco **sdc:**

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Utilize os seguintes comandos para aceder ao ambiente cromoso:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc4 /rescue
      mount -o nouuid /dev/sdc3 /rescue/boot/
      mount /dev/sdc2 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Resolver problemas no ambiente de cromos.

   1. Utilize os seguintes comandos para sair do ambiente croâmado:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Se receber o `unable to unmount /rescue` erro, adicione a opção -l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Retire o disco do VM de resgate e efetue uma troca de disco com o VM original.
1. Inicie o VM original e verifique a sua conectividade.

## <a name="next-steps"></a>Passos Seguintes

- [Resolução de problemas ligação ssh](troubleshoot-ssh-connection.md)
