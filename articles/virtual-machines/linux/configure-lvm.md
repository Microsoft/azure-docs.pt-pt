---
title: Configurar o LVM em uma máquina virtual que executa o Linux
description: Saiba como configurar o LVM no Linux no Azure.
services: virtual-machines-linux
documentationcenter: na
author: MicahMcKittrick-MSFT
manager: gwallace
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: 38e460138fde1de6d8fd57dcab5a88238f0981fe
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751024"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurar o LVM em uma VM do Linux no Azure
Este documento explicará como configurar o LVM (Gerenciador de volumes lógicos) em sua máquina virtual do Azure. O LVM pode ser usado no disco do sistema operacional ou nos discos de dados em VMs do Azure, no entanto, por padrão, a maioria das imagens de nuvem não terá o LVM configurado no disco do sistema operacional. As etapas a seguir se concentrarão na configuração do LVM para seus discos de dados.

## <a name="linear-vs-striped-logical-volumes"></a>Volumes lógicos lineares versus distribuídos
O LVM pode ser usado para combinar vários discos físicos em um único volume de armazenamento. Por padrão, o LVM normalmente criará volumes lógicos lineares, o que significa que o armazenamento físico é concatenado. Nesse caso, as operações de leitura/gravação normalmente serão enviadas para um único disco. Por outro lado, também podemos criar volumes lógicos distribuídos em que leituras e gravações são distribuídas para vários discos contidos no grupo de volumes (semelhante a RAID0). Por motivos de desempenho, é provável que você queira distribuir seus volumes lógicos para que as leituras e gravações utilizem todos os discos de dados anexados.

Este documento descreverá como combinar vários discos de dados em um único grupo de volumes e, em seguida, criar um volume lógico distribuído. As etapas a seguir são generalizadas para trabalhar com a maioria das distribuições. Na maioria dos casos, os utilitários e fluxos de trabalho para gerenciar o LVM no Azure não são fundamentalmente diferentes dos outros ambientes. Como de costume, consulte também o fornecedor do Linux para obter a documentação e as práticas recomendadas para usar o LVM com sua distribuição específica.

## <a name="attaching-data-disks"></a>Anexando discos de dados
Normalmente, você desejará começar com dois ou mais discos de dados vazios ao usar o LVM. Com base nas suas necessidades de e/s, você pode optar por anexar discos que são armazenados em nosso armazenamento Standard, com até 500 IO/PS por disco ou nosso armazenamento Premium com até 5000 IO/PS por disco. Este artigo não entrará em detalhes sobre como provisionar e anexar discos de dados a uma máquina virtual do Linux. Consulte o artigo Microsoft Azure [anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter instruções detalhadas sobre como anexar um disco de dados vazio a uma máquina virtual Linux no Azure.

## <a name="install-the-lvm-utilities"></a>Instalar os utilitários LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

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

    No SLES11, você também deve editar `/etc/sysconfig/lvm` e definir `LVM_ACTIVATED_ON_DISCOVERED` como "habilitar":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Configurar LVM
Neste guia, vamos supor que você tenha anexado três discos de dados, aos quais vamos nos referir como `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Esses caminhos podem não corresponder aos nomes de caminho de disco em sua VM. Você pode executar '`sudo fdisk -l`' ou um comando semelhante para listar os discos disponíveis.

1. Preparar os volumes físicos:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Crie um grupo de volumes. Neste exemplo, estamos chamando o grupo de volumes `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Crie os volumes lógicos. O comando a seguir criará um único volume lógico chamado `data-lv01` para abranger todo o grupo de volumes, mas observe que também é possível criar vários volumes lógicos no grupo de volumes.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatar o volume lógico

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Com SLES11, use `-t ext3` em vez de ext4. O SLES11 só dá suporte ao acesso somente leitura a sistemas de ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de arquivos a/etc/fstab
> [!IMPORTANT]
> A edição imprópria do ficheiro `/etc/fstab` poderá resultar num sistema não inicializável. Se não tiver a certeza, consulte a documentação de distribuição para obter mais informações sobre como editar corretamente este ficheiro. Também é recomendável que um backup do arquivo de `/etc/fstab` seja criado antes da edição.

1. Crie o ponto de montagem desejado para o novo sistema de arquivos, por exemplo:

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

3. Abra `/etc/fstab` em um editor de texto e adicione uma entrada para o novo sistema de arquivos, por exemplo:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Em seguida, salve e feche `/etc/fstab`.

4. Teste se a entrada de `/etc/fstab` está correta:

    ```bash    
    sudo mount -a
    ```

    Se esse comando resultar em uma mensagem de erro, verifique a sintaxe no arquivo `/etc/fstab`.
   
    Em seguida, execute o comando `mount` para garantir que o sistema de arquivos esteja montado:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. Adicional Parâmetros de inicialização FailSafe no `/etc/fstab`
   
    Muitas distribuições incluem os parâmetros de montagem `nobootwait` ou `nofail` que podem ser adicionados ao arquivo `/etc/fstab`. Esses parâmetros permitem falhas durante a montagem de um sistema de arquivos específico e permitem que o sistema Linux continue a inicialização mesmo que não seja possível montar corretamente o sistema de arquivos RAID. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.
   
    Exemplo (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Suporte para APARAr/cancelar mapeamento
Alguns kernels do Linux dão suporte a operações de corte/desmapeamento para descartar blocos não utilizados no disco. Essas operações são úteis principalmente no armazenamento Standard para informar ao Azure que as páginas excluídas não são mais válidas e podem ser descartadas. A descartação de páginas pode economizar custos se você criar arquivos grandes e, em seguida, excluí-los.

Há duas maneiras de habilitar o suporte a corte em sua VM Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

- Use a opção de montagem `discard` em `/etc/fstab`, por exemplo:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos, a opção `discard` pode ter implicações de desempenho. Como alternativa, você pode executar o comando `fstrim` manualmente na linha de comando ou adicioná-lo ao crontab para ser executado regularmente:

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
