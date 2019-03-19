---
title: Configurar o RAID de software numa máquina virtual com Linux | Documentos da Microsoft
description: Saiba como utilizar mdadm para configurar o RAID no Linux no Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: e773fdcb031f0f8f896ea40d76231fd54a603dc4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089034"
---
# <a name="configure-software-raid-on-linux"></a>Configurar o RAID de Software no Linux
É um cenário comum utilizar RAID de software no máquinas virtuais do Linux no Azure para apresentar vários discos de dados anexados como um único dispositivo RAID. Normalmente, isso pode ser usado para melhorar o desempenho e permitir a melhor débito em comparação ao uso apenas um único disco.

## <a name="attaching-data-disks"></a>Anexar discos de dados
Dois ou mais discos de dados vazios são necessárias para configurar um dispositivo RAID.  É a principal razão para a criação de um dispositivo RAID melhorar o desempenho de sua e/s de disco.  Com base nas suas necessidades de e/s, pode optar por anexar discos que estão armazenados no nosso armazenamento Standard, com até 500 e/s/ps por disco ou o nosso armazenamento Premium com e/s/ps até 5000 por disco. Este artigo não entrar em detalhes sobre como aprovisionar e anexar discos de dados para uma máquina virtual Linux.  Consulte o artigo do Microsoft Azure [anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter instruções detalhadas sobre como anexar um disco de dados vazia para uma máquina virtual do Linux no Azure.

## <a name="install-the-mdadm-utility"></a>Instalar o utilitário de mdadm
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES e openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Criar as partições de disco
Neste exemplo, vamos criar uma partição de disco único no /dev/sdc. A nova partição de disco será chamada /dev/sdc1.

1. Iniciar `fdisk` para começar a criar partições

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Prima "n" na linha de comandos para criar uma **n**partição de novo:

    ```bash
    Command (m for help): n
    ```

1. Em seguida, prima 'p' para criar uma **p**o partição:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Prima '1' para selecionar o número de partição 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Selecione o ponto de partida da nova partição ou prima `<enter>` para aceitar a predefinição para colocar a partição no início do espaço livre no disco:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Selecione o tamanho da partição, por exemplo, digite +10G para criar uma partição de 10 gigabytes. Ou, prima `<enter>` criar uma partição única que abrange todo o disco:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Em seguida, alterar o ID e **t**ipo da partição da ID do padrão "83" (Linux) para o ID 'fd"(Linux automaticamente de raid):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Por fim, escreva a tabela de partições para a unidade e sair fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Criar a matriz RAID
1. Terá de exemplo seguintes "stripe" (nível RAID 0) localizadas de partições de três em três discos de dados separado (sdc1, sdd1, sde1).  Depois de executar este comando um novo dispositivo RAID chamado **/desenvolvimento/md127** é criado. Tenha também em atenção que se estes discos de dados anteriormente parte de outra matriz RAID extinto poderá ser necessário adicionar os `--force` parâmetro para o `mdadm` comando:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Criar o sistema de arquivos no novo dispositivo RAID
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE, and Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **11 de SLES** - ativar boot.md e criar mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Um reinício poderá ser necessário após efetuar estas alterações em sistemas SUSE. Este passo é *não* necessário no SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de ficheiros para /etc/fstab.
> [!IMPORTANT]
> Editar incorretamente o ficheiro de /etc/fstab. pode resultar num sistema não inicializável. Se não souber, consulte a documentação da distribuição para obter informações sobre como editar corretamente esse arquivo. Também é recomendável que uma cópia de segurança do ficheiro /etc/fstab. é criada antes de editar.

1. Crie o ponto de montagem pretendido para o seu novo sistema de ficheiros, por exemplo:

    ```bash
    sudo mkdir /data
    ```
1. Ao editar /etc/fstab., o **UUID** deve ser utilizado para referenciar o sistema de ficheiros em vez do nome do dispositivo.  Utilize o `blkid` utilitário para determinar o UUID para o novo sistema de ficheiros:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Abra o /etc/fstab. num editor de texto e adicione uma entrada para o novo sistema de ficheiros, por exemplo:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Ou no **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Em seguida, guarde e feche /etc/fstab.

1. Teste se a entrada/etc/fstab está correta:

    ```bash  
    sudo mount -a
    ```

    Se este comando resulta numa mensagem de erro, verifique a sintaxe no ficheiro /etc/fstab.
   
    Em seguida execute o `mount` comando para garantir que o sistema de ficheiros está montado:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Opcional) Parâmetros de segurança contra falhas de arranque
   
    **configuração de fstab**
   
    Número de distribuições incluir qualquer um a `nobootwait` ou `nofail` montar os parâmetros que podem ser adicionados para o ficheiro/etc/fstab. Estes parâmetros permitem para falhas, ao montar um sistema de ficheiros específica e permitir que o sistema Linux continuar a inicializar o mesmo se não conseguir corretamente montar o sistema de ficheiros RAID. Consulte a documentação de sua distribuição para obter mais informações sobre estes parâmetros.
   
    Exemplo (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parâmetros de inicialização do Linux**
   
    Além dos parâmetros acima, o parâmetro de kernel "`bootdegraded=true`" pode permitir que o sistema, mesmo que o RAID é percebida como exemplo de diminuição, para ou danificado se uma unidade de dados inadvertidamente for removida da máquina virtual de arranque. Por predefinição isso poderia resultar também num sistema não inicializável.
   
    Consulte a documentação de sua distribuição sobre como editar corretamente os parâmetros de kernel. Por exemplo, em muitas distribuições (CentOS, Oracle Linux, 11 de SLES) esses parâmetros podem ser adicionados manualmente para o "`/boot/grub/menu.lst`" ficheiro.  No Ubuntu, este parâmetro pode ser adicionado para o `GRUB_CMDLINE_LINUX_DEFAULT` variável em "/ predefinido/etc/grub".


## <a name="trimunmap-support"></a>Suporte de cortar/UNMAP
Alguns kernels de Linux suportam operações de cortar/UNMAP descartar blocos não utilizados no disco. Essas operações são principalmente útil para armazenamento standard para informar o Azure eliminado páginas já não são válido e podem ser descartados. Descarte páginas pode poupar no custo de se criar ficheiros grandes e, em seguida, eliminá-los.

> [!NOTE]
> RAID não pode enviar comandos da rejeição se o tamanho do segmento para a matriz estiver definido como inferior à predefinição de (512KB). Isto acontece porque a granularidade de unmap no anfitrião também é 512KB. Se modificou o tamanho do segmento da matriz por meio do mdadm `--chunk=` parâmetro, em seguida, Anular mapeamento TRIM/pedidos podem ser ignorados pelo kernel.

Existem duas formas de ativar a limitação de suporte na sua VM do Linux. Como sempre, consulte sua distribuição para a abordagem recomendada:

- Utilize o `discard` montar opção na `/etc/fstab`, por exemplo:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos o `discard` opção pode ter implicações de desempenho. Em alternativa, pode executar o `fstrim` comando manualmente a partir da linha de comandos, ou adicione-o para seu crontab para executar regularmente:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
