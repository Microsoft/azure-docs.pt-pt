---
title: Configure o software RAID num Linux VM
description: Aprenda a usar mdadm para configurar raid no Linux em Azure.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 122abda51b907491b322908c3c2c689bc1723e87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79250260"
---
# <a name="configure-software-raid-on-linux"></a>Configure Software RAID em Linux
É um cenário comum usar o software RAID em máquinas virtuais Linux em Azure para apresentar vários discos de dados anexados como um único dispositivo RAID. Normalmente isto pode ser usado para melhorar o desempenho e permitir uma melhor entrada em comparação com a utilização de apenas um disco.

## <a name="attaching-data-disks"></a>Anexação de discos de dados
São necessários dois ou mais discos de dados vazios para configurar um dispositivo RAID.  A principal razão para criar um dispositivo RAID é melhorar o desempenho do seu IO do disco.  Com base nas suas necessidades de IO, pode optar por anexar discos que são armazenados no nosso Armazenamento Standard, com até 500 IO/ps por disco ou o nosso armazenamento Premium com até 5000 IO/ps por disco. Este artigo não entra em detalhes sobre como fornecer e anexar discos de dados a uma máquina virtual Linux.  Consulte o artigo do Microsoft Azure [para obter](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) instruções detalhadas sobre como anexar um disco de dados vazio a uma máquina virtual Linux no Azure.

> [!IMPORTANT]
>Não misture discos de diferentes tamanhos, fazendo-o resultaria na realização do raidset para se limitar ao do disco mais lento. 

## <a name="install-the-mdadm-utility"></a>Instale o utilitário mdadm
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

## <a name="create-the-disk-partitions"></a>Criar as divisórias do disco
Neste exemplo, criamos uma única divisória de disco em /dev/sdc. A nova divisória do disco será chamada /dev/sdc1.

1. Comece `fdisk` a criar divisórias

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

1. Prima 'n' na solicitação para criar uma partição **n**ew:

    ```bash
    Command (m for help): n
    ```

1. Em seguida, prima 'p' para criar uma partição **p**rimary:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Prima '1' para selecionar a partição número 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Selecione o ponto de partida `<enter>` da nova partição ou prima para aceitar o padrão para colocar a divisória no início do espaço livre na unidade:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Selecione o tamanho da partição, por exemplo, escreva '+10G' para criar uma partição de 10 gigabytes. Ou, `<enter>` pressione criar uma única divisória que se estende por toda a unidade:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Em seguida, altere o ID e **t**ype da partição do ID padrão '83' (Linux) para ID 'fd' (Raid auto Linux):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Finalmente, escreva a mesa de partição para o disco de acionamento e saída:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Criar a matriz RAID
1. O exemplo seguinte irá "riscar" (nível RAID 0) três divisórias localizadas em três discos de dados separados (sdc1, sdd1, sde1).  Depois de executar este comando é criado um novo dispositivo RAID chamado **/dev/md127.** Note também que se estes discos de dados fizermos parte de outra `--force` matriz `mdadm` RAID extinta, pode ser necessário adicionar o parâmetro ao comando:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Criar o sistema de ficheiros no novo dispositivo RAID
   
    **CentOS, Oracle Linux, SLES 12, abre SUSE e Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** - permita boot.md e crie mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Pode ser necessário reiniciar após a eificação destas alterações nos sistemas SUSE. Este passo *não* é necessário na SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicione o novo sistema de ficheiros a /etc/fstab
> [!IMPORTANT]
> A edição inadequada do ficheiro /etc/fstab pode resultar num sistema inabitável. Se não tiver a certeza, consulte a documentação de distribuição para obter mais informações sobre como editar corretamente este ficheiro. Recomenda-se também que seja criada uma cópia de segurança do ficheiro /etc/fstab antes da edição.

1. Crie o ponto de montagem desejado para o seu novo sistema de ficheiros, por exemplo:

    ```bash
    sudo mkdir /data
    ```
1. Ao editar /etc/fstab, o **UUID** deve ser utilizado para fazer referência ao sistema de ficheiros em vez do nome do dispositivo.  Utilize `blkid` o utilitário para determinar o UUID para o novo sistema de ficheiros:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Abra/etc/fstab num editor de texto e adicione uma entrada para o novo sistema de ficheiros, por exemplo:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Ou na **SLES 11:**

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Em seguida, guarde e feche /etc/fstab.

1. Teste se a entrada /etc/fstab está correta:

    ```bash  
    sudo mount -a
    ```

    Se este comando resultar numa mensagem de erro, verifique a sintaxe no ficheiro /etc/fstab.
   
    Em seguida, executar o `mount` comando para garantir que o sistema de ficheiros está montado:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Opcional) Parâmetros de arranque de segurança
   
    **configuração fstab**
   
    Muitas distribuições `nobootwait` incluem os parâmetros ou `nofail` montagem que podem ser adicionados ao ficheiro /etc/fstab. Estes parâmetros permitem falhas na montagem de um determinado sistema de ficheiros e permitem que o sistema Linux continue a arrancar mesmo que não seja capaz de montar corretamente o sistema de ficheiros RAID. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
   
    Exemplo (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parâmetros de arranque de Linux**
   
    Além dos parâmetros acima referidos,`bootdegraded=true`o parâmetro do núcleo " pode permitir que o sistema arranque mesmo que o RAID seja visto como danificado ou degradado, por exemplo, se uma unidade de dados for removida inadvertidamente da máquina virtual. Por padrão, isto também pode resultar num sistema não-sabotável.
   
    Consulte a documentação da sua distribuição sobre como editar adequadamente os parâmetros de kernel. Por exemplo, em muitas distribuições (CentOS, Oracle Linux, SLES 11)`/boot/grub/menu.lst`estes parâmetros podem ser adicionados manualmente ao ficheiro " .  Em Ubuntu este parâmetro pode `GRUB_CMDLINE_LINUX_DEFAULT` ser adicionado à variável em "/etc/default/grub".


## <a name="trimunmap-support"></a>Suporte TRIM/UNMAP
Alguns núcleos linux suportam operações TRIM/UNMAP para descartar blocos não utilizados no disco. Estas operações são principalmente úteis no armazenamento padrão para informar o Azure de que as páginas apagadas já não são válidas e podem ser descartadas. As páginas de devoluções podem economizar custos se criar ficheiros grandes e depois eliminá-las.

> [!NOTE]
> Raid pode não emitir comandos de devoluções se o tamanho do pedaço para a matriz estiver definido para menos do que o padrão (512KB). Isto porque a granularidade não map no hospedeiro também é de 512KB. Se modificar o tamanho do pedaço da matriz `--chunk=` através do parâmetro do mdadm, os pedidos TRIM/unmap podem ser ignorados pelo núcleo.

Existem duas formas de permitir o suporte trim no seu VM Linux. Como de costume, consulte a sua distribuição para obter a abordagem recomendada:

- Utilize `discard` a opção montagem em, `/etc/fstab`por exemplo:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Em alguns `discard` casos, a opção pode ter implicações no desempenho. Em alternativa, pode `fstrim` executar o comando manualmente a partir da linha de comando, ou adicioná-lo ao seu crontab para executar regularmente:

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
