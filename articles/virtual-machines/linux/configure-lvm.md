---
title: Configurar LVM numa máquina virtual em execução no Linux | Documentos da Microsoft
description: Saiba como configurar LVM no Linux no Azure.
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.subservice: disks
ms.openlocfilehash: 08f98775360b8c0a82f68f322053cb71f0e79af3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739085"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurar LVM numa VM do Linux no Azure
Este documento irá debater sobre como configurar o Gestor de volumes lógicos (LVM) na sua máquina virtual do Azure. LVM pode ser utilizado no disco do SO ou discos de dados em VMs do Azure, no entanto, por predefinição a maioria das imagens de cloud não terá LVM configurado no disco do SO. Os passos abaixo irão focar-se sobre como configurar LVM para os discos de dados.

## <a name="linear-vs-striped-logical-volumes"></a>Linear versus volumes repartidos de lógicos
LVM pode ser usado para combinar um número de discos físicos num único volume de armazenamento. Por predefinição LVM normalmente criará lineares volumes lógicos, que significa que o armazenamento físico é concatenado em conjunto. Neste caso as operações de leitura/gravação serão normalmente apenas enviadas para um único disco. Por outro lado, também podemos criar volumes repartidos de lógicos em que as leituras e gravações são distribuídas por vários discos contidos no grupo de volume (semelhante ao RAID0). Por motivos de desempenho, é provável que vai querer que os volumes de lógicos do stripe para que as leituras e gravações utilizam todos os seus discos de dados anexados.

Este documento descreverá como combinar vários discos de dados num grupo de único volume e, em seguida, criar um volume lógico repartido. Os passos abaixo estão generalizados para trabalhar com a maioria das distribuições. Na maioria dos casos os utilitários e fluxos de trabalho para o gerenciamento de LVM no Azure não são fundamentalmente diferentes de outros ambientes. Como sempre, consulte também o fornecedor do Linux para documentação e melhores práticas para utilizar LVM com sua distribuição específica.

## <a name="attaching-data-disks"></a>Anexar discos de dados
Um geral, deve começar com duas ou mais discos de dados vazio quando utilizar LVM. Com base nas suas necessidades de e/s, pode optar por anexar discos que estão armazenados no nosso armazenamento Standard, com até 500 e/s/ps por disco ou o nosso armazenamento Premium com e/s/ps até 5000 por disco. Este artigo não irá entrar em detalhes sobre como aprovisionar e anexar discos de dados para uma máquina virtual Linux. Consulte o artigo do Microsoft Azure [anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter instruções detalhadas sobre como anexar um disco de dados vazia para uma máquina virtual do Linux no Azure.

## <a name="install-the-lvm-utilities"></a>Instale os utilitários LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS e Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 e openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    No SLES11, tem de editar também `/etc/sysconfig/lvm` e defina `LVM_ACTIVATED_ON_DISCOVERED` para "ativar":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Configurar LVM
Neste guia vamos supor que tenha vinculado três discos de dados, o que faremos referência a como `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Estes caminhos não podem corresponder aos nomes de caminho do disco na sua VM. Pode executar "`sudo fdisk -l`' ou um comando semelhante para listar os discos disponíveis.

1. Prepare os volumes físicos:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Crie um grupo de volume. Neste exemplo estamos a ligar para o grupo de volume `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Crie os volumes lógicos. O comando abaixo, irá criar um volume lógico único chamado `data-lv01` para abranger o grupo de todo o volume, mas tenha em atenção que também é possível criar vários volumes lógicos no grupo de volume.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatar o volume de lógico

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Com o uso de SLES11 `-t ext3` em vez de ext4. SLES11 só suporta acesso só de leitura para sistemas de ficheiros de ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de ficheiros para /etc/fstab.
> [!IMPORTANT]
> Editar incorretamente o `/etc/fstab` ficheiros poderiam resultar num sistema não inicializável. Se não souber, consulte a documentação da distribuição para obter informações sobre como editar corretamente esse arquivo. Também é recomendado que uma cópia de segurança a `/etc/fstab` é criado um ficheiro antes de editar.

1. Crie o ponto de montagem pretendido para o seu novo sistema de ficheiros, por exemplo:

    ```bash  
    sudo mkdir /data
    ```

2. Localizar o caminho do volume lógico

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Abra `/etc/fstab` num editor de texto e adicione uma entrada para o novo sistema de ficheiros, por exemplo:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Em seguida, guarde e feche `/etc/fstab`.

4. Testar o `/etc/fstab` entrada está correta:

    ```bash    
    sudo mount -a
    ```

    Se este comando resulta numa mensagem de erro Verifique a sintaxe no `/etc/fstab` ficheiro.
   
    Em seguida execute o `mount` comando para garantir que o sistema de ficheiros está montado:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Opcional) Parâmetros de arranque de segurança contra falhas em `/etc/fstab`
   
    Número de distribuições incluir qualquer um a `nobootwait` ou `nofail` montar os parâmetros que podem ser adicionados ao `/etc/fstab` ficheiro. Estes parâmetros permitem para falhas, ao montar um sistema de ficheiros específica e permitir que o sistema Linux continuar a inicializar o mesmo se não conseguir corretamente montar o sistema de ficheiros RAID. Consulte a documentação de sua distribuição para obter mais informações sobre estes parâmetros.
   
    Exemplo (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Suporte de cortar/UNMAP
Alguns kernels de Linux suportam operações de cortar/UNMAP descartar blocos não utilizados no disco. Essas operações são principalmente útil para armazenamento standard para informar o Azure eliminado páginas já não são válido e podem ser descartados. Descarte páginas pode poupar no custo de se criar ficheiros grandes e, em seguida, eliminá-los.

Existem duas formas de ativar a limitação de suporte na sua VM do Linux. Como sempre, consulte sua distribuição para a abordagem recomendada:

- Utilize o `discard` montar opção na `/etc/fstab`, por exemplo:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos o `discard` opção pode ter implicações de desempenho. Em alternativa, pode executar o `fstrim` comando manualmente a partir da linha de comandos, ou adicione-o para seu crontab para executar regularmente:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
