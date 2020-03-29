---
title: Expandir discos rígidos virtuais num VM Linux
description: Aprenda a expandir discos rígidos virtuais num VM Linux com o Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1295c5276f0f342323acf8d86eaaf9f785af3e9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945190"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expanda discos rígidos virtuais num VM Linux com o ClI Azure

Este artigo descreve como expandir discos geridos para uma máquina virtual Linux (VM) com o Azure CLI. Pode [adicionar discos](add-disk.md) de dados para fornecer espaço de armazenamento adicional, e também pode expandir um disco de dados existente. O tamanho do disco rígido virtual padrão para o sistema operativo (OS) é tipicamente de 30 GB num VM Linux em Azure. 

> [!WARNING]
> Certifique-se sempre de que o seu sistema de ficheiros está em estado saudável, o seu tipo de tabela de divisórias de disco irá suportar o novo tamanho e certifique-se de que os seus dados estão a ser apoiados antes de realizar as operações de redimensionamento do disco. Para mais informações, consulte [os VMs Linux em Azure.](tutorial-backup-vms.md) 

## <a name="expand-an-azure-managed-disk"></a>Expandir um disco gerido azure
Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e está inscrito numa conta Azure utilizando [login az](/cli/azure/reference-index#az-login).

Este artigo requer um VM existente em Azure com pelo menos um disco de dados ligado e preparado. Se ainda não tiver um VM que possa utilizar, consulte [Create e prepare um VM com discos](tutorial-manage-disks.md#create-and-attach-disks)de dados .

Nas seguintes amostras, substitua nomes de parâmetros de exemplo como *o myResourceGroup* e *o myVM* pelos seus próprios valores.

1. As operações em discos rígidos virtuais não podem ser realizadas com o VM a funcionar. Deslocar o seu VM com [az vm dealocar](/cli/azure/vm#az-vm-deallocate). O exemplo seguinte desafeta o VM denominado *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > O VM tem de ser transferido para expandir o disco rígido virtual. Parar o VM com `az vm stop` não liberta os recursos da computação. Para libertar recursos computacionais, use `az vm deallocate`.

1. Veja uma lista de discos geridos num grupo de recursos com lista de [discos Az](/cli/azure/disk#az-disk-list). O exemplo seguinte apresenta uma lista de discos geridos no grupo de recursos chamado *myResourceGroup:*

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expanda o disco necessário com a atualização do [disco Az](/cli/azure/disk#az-disk-update). O exemplo seguinte expande o disco gerido chamado *myDataDisk* para *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Quando expande um disco gerido, o tamanho atualizado é arredondado para o tamanho do disco gerido mais próximo. Para uma tabela dos tamanhos e níveis de disco geridos disponíveis, consulte a visão geral dos [discos geridos pelo Azure - Preços e Faturação](../windows/managed-disks-overview.md).

1. Inicie o seu VM com [início az vm](/cli/azure/vm#az-vm-start). O exemplo seguinte inicia o VM nomeado *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Expandir uma partilha de discos e sistema de ficheiros
Para utilizar um disco expandido, expanda a divisória subjacente e o sistema de ficheiros.

1. SSH para o seu VM com as credenciais apropriadas. Pode ver o endereço IP público do seu VM com [o az vm show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Expanda a divisória e o sistema de ficheiros subjacentes.

    a. Se o disco já estiver montado, desmonte-o:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Utilizar `parted` para visualizar informações sobre o disco e redimensionar a partição:

    ```bash
    sudo parted /dev/sdc
    ```

    Veja informações sobre o `print`layout de partição existente com . A saída é semelhante ao seguinte exemplo, que mostra que o disco subjacente é de 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Expandir a `resizepart`partição com . Introduza o número da partição, *1*, e um tamanho para a nova partição:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Para sair, `quit`entre.

1. Com a divisória redimensionada, `e2fsck`verifique a consistência da partição com:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Redimensionar o `resize2fs`sistema de ficheiros com:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Monte a divisória no local `/datadrive`pretendido, tais como:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Para verificar se o disco de `df -h`dados foi redimensionado, utilize. A saída de exemplo seguinte mostra que a unidade de dados */dev/sdc1* é agora de 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Passos seguintes
* Se necessitar de armazenamento adicional, também pode adicionar discos de [dados a um VM Linux](add-disk.md). 
* Para obter mais informações sobre encriptação de disco, consulte a [encriptação do disco Azure para VMs Linux](disk-encryption-overview.md).
