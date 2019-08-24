---
title: Configurar o RAID de software em uma máquina virtual que executa o Linux | Microsoft Docs
description: Saiba como usar o mdadm para configurar o RAID no Linux no Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: gwallace
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
ms.openlocfilehash: d194f4d883063c27da05c9ddf63de2b225a8c10a
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980975"
---
# <a name="configure-software-raid-on-linux"></a>Configurar o RAID de Software no Linux
É um cenário comum usar o RAID de software em máquinas virtuais Linux no Azure para apresentar vários discos de dados anexados como um único dispositivo RAID. Normalmente, isso pode ser usado para melhorar o desempenho e permitir uma melhor taxa de transferência em comparação com o uso de apenas um único disco.

## <a name="attaching-data-disks"></a>Anexando discos de dados
Dois ou mais discos de dados vazios são necessários para configurar um dispositivo RAID.  O principal motivo para criar um dispositivo RAID é melhorar o desempenho da e/s do disco.  Com base nas suas necessidades de e/s, você pode optar por anexar discos que são armazenados em nosso armazenamento Standard, com até 500 IO/PS por disco ou nosso armazenamento Premium com até 5000 IO/PS por disco. Este artigo não se aprofunda em detalhes sobre como provisionar e anexar discos de dados a uma máquina virtual do Linux.  Consulte o artigo Microsoft Azure [anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter instruções detalhadas sobre como anexar um disco de dados vazio a uma máquina virtual Linux no Azure.

## <a name="install-the-mdadm-utility"></a>Instalar o utilitário mdadm
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **Oracle Linux CentOS &**
  ```bash
  sudo yum install mdadm
  ```

* **SLES e openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Criar as partições de disco
Neste exemplo, criamos uma única partição de disco em/dev/sdc. A nova partição de disco será chamada de/dev/sdc1.

1. Comece `fdisk` a começar a criar partições

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

1. Pressione ' n' no prompt para criar uma partição **n**ova:

    ```bash
    Command (m for help): n
    ```

1. Em seguida, pressione ' p ' para criar uma partição **p**rimária:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Pressione ' 1 ' para selecionar a partição número 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Selecione o ponto de partida da nova partição ou pressione `<enter>` para aceitar o padrão para posicionar a partição no início do espaço livre na unidade:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Selecione o tamanho da partição, por exemplo, digite ' + 10G ' para criar uma partição de 10 gigabytes. Ou então, `<enter>` pressione criar uma única partição que abranja toda a unidade:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Em seguida, altere a ID e o tipo **t**da partição da ID padrão ' 83 ' (Linux) para a ID ' fd ' (Linux RAID Auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Por fim, grave a tabela de partição na unidade e saia do fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Criar a matriz RAID
1. O exemplo a seguir irá "distribuir" (RAID nível 0) três partições localizadas em três discos de dados separados (sdc1, sdd1, sde1).  Depois de executar esse comando, um novo dispositivo RAID chamado **/dev/md127** será criado. Observe também que, se esses discos de dados fizerem parte de outra matriz RAID expirada, poderá ser necessário `--force` adicionar o parâmetro `mdadm` ao comando:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Criar o sistema de arquivos no novo dispositivo RAID
   
    **CentOS, Oracle Linux, SLES 12, openSUSE e Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** -habilitar boot.MD e criar mdadm. conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Uma reinicialização pode ser necessária depois de fazer essas alterações em sistemas SUSE. Esta etapa *não* é necessária no SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de arquivos a/etc/fstab
> [!IMPORTANT]
> A edição inadequada do arquivo/etc/fstab pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente esse arquivo. Também é recomendável que um backup do arquivo/etc/fstab seja criado antes da edição.

1. Crie o ponto de montagem desejado para o novo sistema de arquivos, por exemplo:

    ```bash
    sudo mkdir /data
    ```
1. Ao editar/etc/fstab, o **UUID** deve ser usado para fazer referência ao sistema de arquivos em vez do nome do dispositivo.  Use o `blkid` utilitário para determinar o UUID para o novo sistema de arquivos:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Abra o/etc/fstab em um editor de texto e adicione uma entrada para o novo sistema de arquivos, por exemplo:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Ou no **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Em seguida, salve e feche o/etc/fstab.

1. Teste se a entrada/etc/fstab está correta:

    ```bash  
    sudo mount -a
    ```

    Se esse comando resultar em uma mensagem de erro, verifique a sintaxe no arquivo/etc/fstab.
   
    Em seguida, `mount` execute o comando para garantir que o sistema de arquivos esteja montado:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. Adicional Parâmetros de inicialização failsafe
   
    **configuração do fstab**
   
    Muitas distribuições incluem os parâmetros `nobootwait` de `nofail` montagem ou que podem ser adicionados ao arquivo/etc/fstab. Esses parâmetros permitem falhas durante a montagem de um sistema de arquivos específico e permitem que o sistema Linux continue a inicialização mesmo que não seja possível montar corretamente o sistema de arquivos RAID. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.
   
    Exemplo (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parâmetros de inicialização do Linux**
   
    Além dos parâmetros acima, o parâmetro de kernel "`bootdegraded=true`" pode permitir que o sistema seja inicializado mesmo que o RAID seja percebido como danificado ou degradado, por exemplo, se uma unidade de dados for removida inadvertidamente da máquina virtual. Por padrão, isso também pode resultar em um sistema não inicializável.
   
    Consulte a documentação da sua distribuição sobre como editar corretamente os parâmetros do kernel. Por exemplo, em muitas distribuições (CentOS, Oracle Linux, SLES 11), esses parâmetros podem ser adicionados manualmente ao arquivo`/boot/grub/menu.lst`"".  No Ubuntu, esse parâmetro pode ser adicionado à `GRUB_CMDLINE_LINUX_DEFAULT` variável em "/etc/default/grub".


## <a name="trimunmap-support"></a>Suporte para APARAr/cancelar mapeamento
Alguns kernels do Linux dão suporte a operações de corte/desmapeamento para descartar blocos não utilizados no disco. Essas operações são úteis principalmente no armazenamento Standard para informar ao Azure que as páginas excluídas não são mais válidas e podem ser descartadas. A descartação de páginas pode economizar custos se você criar arquivos grandes e, em seguida, excluí-los.

> [!NOTE]
> O RAID não poderá emitir comandos de descarte se o tamanho da parte da matriz for definido como menor que o padrão (512KB). Isso ocorre porque a granularidade do desmapeamento no host também é 512KB. Se você tiver modificado o tamanho da parte da matriz por `--chunk=` meio do parâmetro de mdadm, as solicitações de corte/desmapeamento poderão ser ignoradas pelo kernel.

Há duas maneiras de habilitar o suporte a corte em sua VM Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

- Use a `discard` opção de montagem `/etc/fstab`em, por exemplo:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos, `discard` a opção pode ter implicações de desempenho. Como alternativa, você pode executar o `fstrim` comando manualmente na linha de comando ou adicioná-lo ao crontab para ser executado regularmente:

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
