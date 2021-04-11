---
title: Redimensione um disco DE que tenha uma partição GPT
description: Este artigo fornece instruções sobre como redimensionar um disco DE que tem uma divisória GUID Partition Table (GPT) em Linux.
services: virtual-machines
ms.topic: article
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 0db79728bbb963aa360743afc70aecc213bfb7bc
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011686"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Redimensione um disco DE que tenha uma partição GPT

> [!NOTE]
> Este artigo aplica-se apenas aos discos DE QUE têm uma divisória GUID Partition Table (GPT).

Este artigo descreve como aumentar o tamanho de um disco de SO que tem uma partição GPT em Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Identifique se o disco DE tem uma partição MBR ou GPT

Utilize o `parted` comando para identificar se a partição do disco foi criada com uma divisória de boot record principal (MBR) ou uma partição GPT.

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

Para aumentar o tamanho do disco DE em Ubuntu 16. *x* e 18. *x:*

1. Parar a VM.
1. Aumente o tamanho do disco de SO a partir do portal.
1. Reinicie o VM e, em seguida, inscreva-se no VM como utilizador **de raiz.**
1. Verifique se o disco DE apresenta agora um tamanho aumentado do sistema de ficheiros.

No exemplo seguinte, o disco SO foi redimensionado do portal para 100 GB. O sistema **de ficheiros /dev/sda1** montado **/** agora apresenta 97 GB.

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

Quando o VM recomeçar, complete estes passos:

1. Aceda ao seu VM como utilizador **de raiz** utilizando este comando:

   ```
   # sudo -i
   ```

1. Utilize o seguinte comando para instalar o pacote **growpart,** que utilizará para redimensionar a partição:

   ```
   # zypper install growpart
   ```

1. Utilize o `lsblk` comando para encontrar a partição montada na raiz do sistema de ficheiros ( **/** ). Neste caso, vemos que a partição 4 do dispositivo **sda** é montada em **/** :

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Redimensionar a partição necessária utilizando o `growpart` comando e o número de partição determinados na etapa anterior:

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Volte a executar o `lsblk` comando para verificar se a partição foi aumentada.

   A seguinte saída mostra que a partição **/dev/sda4** foi redimensionada para 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifique o tipo de sistema de ficheiros no disco OS utilizando o `lsblk` comando com a `-f` bandeira:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Com base no tipo de sistema de ficheiros, utilize os comandos apropriados para redimensionar o sistema de ficheiros.
   
   Para **xfs,** use este comando:
   
   ```
   #xfs_growfs /
   ```
   
   Exemplo de saída:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Para **ext4**, utilize este comando:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Verifique o tamanho do sistema de ficheiros aumentado para **df -Th** utilizando este comando:
   
   ```
   #df -Thl
   ```
   
   Exemplo de saída:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   No exemplo anterior, podemos ver que o tamanho do sistema de ficheiros para o disco de so foi aumentado.

### <a name="rhel-with-lvm"></a>RHEL com LVM

1. Aceda ao seu VM como utilizador **de raiz** utilizando este comando:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Utilize o `lsblk` comando para determinar qual o volume lógico (LV) montado na raiz do sistema de ficheiros **/** (). Neste caso, vemos que **o enraiza-raiz** é montado em **/** . Se quiser outro sistema de ficheiros, substitua o LV e o ponto de montagem ao longo deste artigo.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Verifique se há espaço livre no grupo de volume LVM (VG) que contém a partição raiz. Se houver espaço livre, salte para o passo 12.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   Neste exemplo, a linha **Free PE / Size** mostra que há 38,02 GB grátis no grupo de volume. Não é necessário redimensionar o disco antes de adicionar espaço ao grupo de volume.

1. Para aumentar o tamanho do disco DE em RHEL 7. *x* com LVM:

   1. Parar a VM.
   1. Aumente o tamanho do disco de SO a partir do portal.
   1. Inicie a VM.

1. Quando o VM tiver sido reiniciado, complete o seguinte passo:

   - Instale o pacote **de massa de cultivo em nuvem** para fornecer o comando **growpart,** que é necessário para aumentar o tamanho do disco de SO e o manipulador gdisk para os layouts de discos GPT. Estes pacotes estão pré-instalados na maioria das imagens do mercado.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Determine qual disco e partição detém o volume ou volumes físicos LVM (PV) no grupo de volume nomeado **rootvg** utilizando o `pvscan` comando. Note o tamanho e o espaço livre listado entre os suportes **(e** **]**.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Verifique o tamanho da partição utilizando `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Expanda a partição que contém este PV utilizando `growpart` o nome do dispositivo e o número de partição. Ao fazê-lo, expandirá a partição especificada para utilizar todo o espaço contíguo gratuito do dispositivo.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Verifique se a partição redimensionou para o tamanho esperado utilizando novamente o `lsblk` comando. Note que, no exemplo, o **sda4** passou de 63 GB para 95 GB.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Expandir o PV para utilizar o resto da partição recém-expandida:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Verifique se o novo tamanho do PV é o tamanho esperado, comparando-o com os valores originais **[tamanho/free]:**

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Expanda o volume lógico desejado (LV) pela quantidade desejada. A quantidade não precisa de ser todo o espaço livre no grupo de volume. No exemplo seguinte, **/dev/mapper/rootvg-rootlv** é redimensionado de 2 GB para 12 GB (um aumento de 10 GB). Este comando também irá redimensionar o sistema de ficheiros.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Exemplo de saída:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
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

1. O `lvresize` comando chama automaticamente o comando de redimensionar apropriado para o sistema de ficheiros no LV. Verifique se **/dev/mapper/rootvg-rootlv**, que é montado **/** em , tem um tamanho de sistema de ficheiros aumentado usando este comando:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Exemplo de saída:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Para utilizar o mesmo procedimento para redimensionar qualquer outro volume lógico, altere o nome LV no passo 12.


### <a name="rhel-raw"></a>RHEL RAW

Para aumentar o tamanho do disco DE numa divisória RHEL RAW:

1. Parar a VM.
1. Aumente o tamanho do disco de SO a partir do portal.
1. Inicie a VM.

Quando o VM recomeçar, complete estes passos:

1. Aceda ao seu VM como utilizador **de raiz** utilizando o seguinte comando:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Quando o VM tiver sido reiniciado, complete o seguinte passo:

   - Instale o pacote **de massa de cultivo em nuvem** para fornecer o comando **growpart,** que é necessário para aumentar o tamanho do disco de SO e o manipulador gdisk para os layouts de discos GPT. Este pacote está pré-instalado na maioria das imagens do mercado.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Utilize o comando **lsblk -f** para verificar a partição e o tipo de sistema de ficheiros que mantém a **/** raiz () partição:

   ```bash
   [root@vm-dd-cent7 ~]# lsblk -f
   NAME    FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1  xfs          2a7bb59d-6a71-4841-a3c6-cba23413a5d2 /boot
   ├─sda2  xfs          148be922-e3ec-43b5-8705-69786b522b05 /
   ├─sda14
   └─sda15 vfat         788D-DC65                            /boot/efi
   sdb
   └─sdb1  ext4         923f51ff-acbd-4b91-b01b-c56140920098 /mnt/resource
   ```

1. Para verificação, comece por listar a tabela de partição do disco sda com **gdisk**. Neste exemplo, vemos um disco de 48 GB com partição 2 a 29.0 GiB. O disco foi expandido de 30 GB para 48 GB no portal Azure.

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 62914526
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 6076 sectors (3.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048        62912511   29.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Expanda a partição para raiz, neste caso sda2 usando o comando **growpart.** A utilização deste comando expande a partição para utilizar todo o espaço contíguo no disco.

   ```bash
   [root@vm-dd-cent7 ~]# growpart /dev/sda 2
   CHANGED: partition=2 start=2050048 old: size=60862464 end=62912512 new: size=98613214 end=100663262
   ```

1. Agora imprima a nova mesa de partição com **gdisk** novamente.  Note que a partição 2 expandiu-se para 47.0 GiB:

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 100663262
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 4062 sectors (2.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048       100663261   47.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Expandir o sistema de ficheiros na partição com **xfs_growfs,** o que é apropriado para um sistema RedHat gerado pelo mercado padrão:

   ```bash
   [root@vm-dd-cent7 ~]# xfs_growfs /
   meta-data=/dev/sda2              isize=512    agcount=4, agsize=1901952 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=7607808, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3714, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7607808 to 12326651
   ```

1. Verifique se o novo tamanho é refletido utilizando o comando **df:**

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
