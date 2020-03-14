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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
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

* **CentOS e Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES e openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Criar as divisórias do disco
Neste exemplo, criamos uma única divisória de disco em /dev/sdc. A nova divisória do disco será chamada /dev/sdc1.

1. Comece `fdisk` começar a criar divisórias

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

1. Selecione o ponto de partida da nova divisória ou pressione `<enter>` para aceitar o padrão para colocar a divisória no início do espaço livre na unidade:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Selecione o tamanho da partição, por exemplo, escreva '+10G' para criar uma partição de 10 gigabytes. Ou, pressione `<enter>` criar uma única divisória que se estende por toda a unidade:

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
1. O exemplo seguinte irá "riscar" (nível RAID 0) três divisórias localizadas em três discos de dados separados (sdc1, sdd1, sde1).  Depois de executar este comando é criado um novo dispositivo RAID chamado **/dev/md127.** Note também que se estes discos de dados fizermos parte de outra matriz RAID extinta, pode ser necessário adicionar o parâmetro `--force` ao comando `mdadm`:

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
> A edição inadequada do ficheiro /etc/fstab pode resultar num sistema inabitável. Se não tiver a certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente este ficheiro. Recomenda-se também que seja criada uma cópia de segurança do ficheiro /etc/fstab antes da edição.

1. Crie o ponto de montagem desejado para o seu novo sistema de ficheiros, por exemplo:

    ```bash
    sudo mkdir /data
    ```
1. Ao editar /etc/fstab, o **UUID** deve ser utilizado para fazer referência ao sistema de ficheiros em vez do nome do dispositivo.  Utilize o utilitário `blkid` para determinar o UUID para o novo sistema de ficheiros:

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
   
    Em seguida, executar o comando `mount` para garantir que o sistema de ficheiros está montado:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Opcional) Parâmetros de arranque de segurança
   
    **configuração fstab**
   
    Muitas distribuições incluem os parâmetros de montagem `nobootwait` ou `nofail` que podem ser adicionados ao ficheiro /etc/fstab. Estes parâmetros permitem falhas na montagem de um determinado sistema de ficheiros e permitem que o sistema Linux continue a arrancar mesmo que não seja capaz de montar corretamente o sistema de ficheiros RAID. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
   
    Exemplo (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parâmetros de arranque de Linux**
   
    Além dos parâmetros acima referidos, o parâmetro de kernel "`bootdegraded=true`" pode permitir que o sistema arranque mesmo que o RAID seja visto como danificado ou degradado, por exemplo, se uma unidade de dados for removida inadvertidamente da máquina virtual. Por padrão, isto também pode resultar num sistema não-sabotável.
   
    Consulte a documentação da sua distribuição sobre como editar adequadamente os parâmetros de kernel. Por exemplo, em muitas distribuições (CentOS, Oracle Linux, SLES 11) estes parâmetros podem ser adicionados manualmente ao ficheiro "`/boot/grub/menu.lst`".  Em Ubuntu este parâmetro pode ser adicionado à variável `GRUB_CMDLINE_LINUX_DEFAULT` em "/etc/padrão/grub".


## <a name="trimunmap-support"></a>Suporte TRIM/UNMAP
Alguns núcleos linux suportam operações TRIM/UNMAP para descartar blocos não utilizados no disco. Estas operações são principalmente úteis no armazenamento padrão para informar o Azure de que as páginas apagadas já não são válidas e podem ser descartadas. As páginas de devoluções podem economizar custos se criar ficheiros grandes e depois eliminá-las.

> [!NOTE]
> Raid pode não emitir comandos de devoluções se o tamanho do pedaço para a matriz estiver definido para menos do que o padrão (512KB). Isto porque a granularidade não map no hospedeiro também é de 512KB. Se modificar o tamanho do pedaço da matriz através do parâmetro `--chunk=` do Mdadm, os pedidos trim/unmap podem ser ignorados pelo núcleo.

Existem duas formas de permitir o suporte trim no seu VM Linux. Como de costume, consulte a sua distribuição para obter a abordagem recomendada:

- Utilize a opção `discard` montagem em `/etc/fstab`, por exemplo:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos, a opção `discard` pode ter implicações no desempenho. Em alternativa, pode executar o comando `fstrim` manualmente a partir da linha de comando, ou adicioná-lo ao seu crontab para executar regularmente:

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
