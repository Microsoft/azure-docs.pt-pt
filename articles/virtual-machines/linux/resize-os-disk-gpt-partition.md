---
title: Redimensione um disco DE que tenha uma partição GPT Microsoft Docs
description: Este artigo fornece instruções sobre a redimensionamento de um disco DE que tem uma partição GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 7c408e8e29b3f9ac423a6104c40242f11f93a171
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83651085"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Redimensione um disco DE que tenha uma partição GPT

> [!NOTE]
> Este cenário aplica-se apenas aos discos DE QUE têm uma divisória GUID Partition Table (GPT).

Este artigo descreve como aumentar o tamanho de um disco de SO que tem uma partição GPT em Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Identifique se o disco DE tem uma partição MBR ou GPT

Utilize o comando **de divisão** para identificar se a partição do disco foi criada com uma divisória de boot record principal (MBR) ou uma partição GPT.

### <a name="mbr-partition"></a>Partição de MBR

Na seguinte saída, a **Tabela de Partição** mostra um valor de **msdos**. Este valor identifica uma partição MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Partição GPT

Na seguinte saída, a **Tabela de Partição** mostra um valor de **gpt**. Este valor identifica uma partição GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Se a sua máquina virtual (VM) tiver uma partição GPT no seu disco de so, aumente o tamanho do disco DE.

## <a name="increase-the-size-of-the-os-disk"></a>Aumente o tamanho do disco de so

As seguintes instruções aplicam-se às distribuições aprovadas pelo Linux.

> [!NOTE]
> Antes de prosseguir, faça uma cópia de reserva do seu VM ou tire uma foto do seu disco de so.

### <a name="ubuntu"></a>Ubuntu

Para aumentar o tamanho do disco DE em Ubuntu 16.x e 18.x:

1. Parar a VM.
1. Aumente o tamanho do disco de SO a partir do portal.
1. Reinicie o VM e, em seguida, inicie sessão no VM como utilizador **de raiz.**
1. Verifique se o disco DE apresenta agora um tamanho aumentado do sistema de ficheiros.

Como mostrado no exemplo seguinte, o disco SO foi redimensionado do portal para 100 GB. O sistema **de ficheiros /dev/sda1** montado **/** agora apresenta 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Para aumentar o tamanho do disco DE EM em SUSE 12 SP4, SUSE SLES 12 para SAP, SUSE SLES 15 e SUSE SLES 15 para SAP:

1. Parar a VM.
1. Aumente o tamanho do disco de SO a partir do portal.
1. Reinicie a VM.

Quando o VM tiver sido reiniciado, execute os seguintes passos:

   1. Aceda ao seu VM como utilizador **de raiz** utilizando o seguinte comando:
   
      `#sudo su`

   1. Utilize o seguinte comando para instalar o pacote **gptfdisk,** que é necessário para aumentar o tamanho do disco OS:

      `#zypper install gptfdisk -y`

   1. Para ver o maior sector disponível no disco, execute o seguinte comando:

      `#sgdisk -e /dev/sda`

   1. Redimensione a partição sem a apagar utilizando o seguinte comando. O comando **de separação** tem uma opção chamada **redimensionar** a parte para redimensionar a partição sem a eliminar. O número 4 após **a redimensionamento** indica a redimensionamento da quarta divisória.

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. Verifique o comando **#lsblk** para verificar se a partição foi aumentada.

      A seguinte saída mostra que a partição **/dev/sda4** foi redimensionada para 98,5 GB.

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. Identifique o tipo de sistema de ficheiros no disco OS utilizando o seguinte comando:

      `blkid`

      Exemplo de saída:

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. Com base no tipo de sistema de ficheiros, utilize os comandos apropriados para redimensionar o sistema de ficheiros.

      Para **xfs,** utilize o seguinte comando:

      ` #xfs_growfs /`

      Exemplo de saída:

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      Para **ext4**, utilize o seguinte comando:

      ```#resize2fs /dev/sda4```

   1. Verifique o tamanho do sistema de ficheiros aumentado para **df -Th,** utilizando o seguinte comando:

      `#df -Th`

      Exemplo de saída:

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

No exemplo anterior, podemos ver que o tamanho do sistema de ficheiros para o disco de so foi aumentado.

### <a name="rhel"></a>RHEL

Para aumentar o tamanho do disco DE em RHEL 7.x com LVM:

1. Parar a VM.
1. Aumente o tamanho do disco de SO a partir do portal.
1. Inicie a VM.

Quando o VM tiver sido reiniciado, execute os seguintes passos:

   1. Aceda ao seu VM como utilizador **de raiz** utilizando o seguinte comando:
   
      `#sudo su`

   1. Instale o pacote **gptfdisk,** que é necessário para aumentar o tamanho do disco DE.

      `#yum install gdisk -y`

   1. Para ver o maior sector disponível no disco, execute o seguinte comando:

      `#sgdisk -e /dev/sda`

   1. Redimensione a partição sem a apagar utilizando o seguinte comando. O comando **de separação** tem uma opção chamada **redimensionar** a parte para redimensionar a partição sem a eliminar. O número 4 após **a redimensionamento** indica a redimensionamento da quarta divisória.

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. Executar o seguinte comando para verificar se a partição foi aumentada:

      `#lsblk`

      A seguinte saída mostra que a partição **/dev/sda4** foi redimensionada para 99 GB.

      ```
      [user@myvm ~]# lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      fd0                 2:0    1    4K  0 disk
      sda                 8:0    0  100G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot/efi
      ├─sda2              8:2    0  500M  0 part /boot
      ├─sda3              8:3    0    2M  0 part
      └─sda4              8:4    0   99G  0 part
      ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
      ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
      ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
      ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
      ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
      └─rootvg-rootlv   253:5    0    2G  0 lvm  /
      sdb                 8:16   0   50G  0 disk
      └─sdb1              8:17   0   50G  0 part /mnt/resource
      ```

   1. Utilize o seguinte comando para redimensionar o volume físico (PV):

      `#pvresize /dev/sda4`

      A seguinte saída mostra que o PV foi redimensionado para 99,02 GB.

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. No exemplo seguinte, **/dev/mapper/rootvg-rootlv** está a ser redimensionado de 2 GB para 12 GB (um aumento de 10 GB) através do seguinte comando. Este comando também irá redimensionar o sistema de ficheiros.

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

      Exemplo de saída:

      ```
      [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
      Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
      Logical volume rootvg/rootlv successfully resized.
      meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
               =                       sectsz=4096  attr=2, projid32bit=1
               =                       crc=1        finobt=0 spinodes=0
      data     =                       bsize=4096   blocks=524288, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=2560, version=2
               =                       sectsz=4096  sunit=1 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 524288 to 3145728
      ```
         
   1. Verifique se **/dev/mapper/rootvg-rootlv** tem um tamanho aumentado do sistema de ficheiros utilizando o seguinte comando:

      `#df -Th /`

      Exemplo de saída:

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

   > [!NOTE]
   > Para utilizar o mesmo procedimento para redimensionar qualquer outro volume lógico, altere o nome **lv** no passo 7.

## <a name="next-steps"></a>Próximos passos

- [Disco de redimensione](expand-disks.md)