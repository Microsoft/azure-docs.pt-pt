---
title: Expandir discos rígidos virtuais num Linux VM
description: Aprenda a expandir discos rígidos virtuais num Linux VM com o Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d1d433c7db36a3f4fe5f528b7fbd17549bc08e4a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791498"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expandir discos rígidos virtuais num Linux VM com o Azure CLI

Este artigo descreve como expandir discos geridos para uma máquina virtual Linux (VM) com o Azure CLI. Pode [adicionar discos de dados](add-disk.md) para fornecer espaço de armazenamento adicional, e também pode expandir um disco de dados existente. O tamanho do disco rígido virtual padrão para o sistema operativo (OS) é tipicamente de 30 GB num Linux VM em Azure. 

> [!WARNING]
> Certifique-se sempre de que o seu sistema de ficheiros está num estado saudável, o tipo de tabela de partição de disco irá suportar o novo tamanho e certifique-se de que os seus dados são suportados antes de realizar operações de redimensionação do disco. Para obter mais informações, consulte o [arranque rápido da Azure Backup](../../backup/quick-backup-vm-portal.md). 

## <a name="expand-an-azure-managed-disk"></a>Expandir um disco gerido azure
Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e está inscrito numa conta Azure utilizando [o login az](/cli/azure/reference-index#az-login).

Este artigo requer um VM existente em Azure com pelo menos um disco de dados ligado e preparado. Se ainda não tiver um VM que possa utilizar, consulte [Criar e preparar um VM com discos de dados.](tutorial-manage-disks.md#create-and-attach-disks)

Nas seguintes amostras, substitua nomes de parâmetros de exemplo como *myResourceGroup* e *myVM* pelos seus próprios valores.

1. As operações em discos rígidos virtuais não podem ser realizadas com o VM em funcionamento. Translocar o seu VM com [az vm deallocate](/cli/azure/vm#az-vm-deallocate). O exemplo seguinte é o que a VM nomeou *myVM* no grupo de recursos denominado *myResourceGroup:*

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > O VM tem de ser transatado para expandir o disco rígido virtual. Parar o VM com `az vm stop` não liberta os recursos computacional. Para libertar recursos computacional, use `az vm deallocate` .

1. Ver uma lista de discos geridos num grupo de recursos com [lista de discos az](/cli/azure/disk#az-disk-list). O exemplo a seguir apresenta uma lista de discos geridos no grupo de recursos denominado *myResourceGroup:*

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expandir o disco necessário com [a atualização do disco az](/cli/azure/disk#az-disk-update). O exemplo a seguir expande o disco gerido chamado *myDataDisk* para *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Quando expande um disco gerido, o tamanho atualizado é arredondado para o tamanho do disco gerido mais próximo. Para obter uma tabela dos tamanhos e níveis de discos geridos disponíveis, consulte [a Visão Geral dos Discos Geridos Azure - Preços e Faturação](../managed-disks-overview.md).

1. Inicie o seu VM com [arranque az vm](/cli/azure/vm#az-vm-start). O exemplo a seguir começa o VM nomeado *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Expandir uma partição de disco e sistema de ficheiros
Para utilizar um disco expandido, expanda a partição subjacente e o sistema de ficheiros.

1. SSH para o seu VM com as credenciais apropriadas. Pode ver o endereço IP público do seu VM com [a az vm show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Expandir a partição subjacente e o sistema de ficheiros.

    a. Se o disco já estiver montado, desmonte-o:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Utilize `parted` para visualizar informações de disco e redimensionar a partição:

    ```bash
    sudo parted /dev/sdc
    ```

    Ver informações sobre o layout de partição existente com `print` . A saída é semelhante ao exemplo seguinte, que mostra que o disco subjacente é de 215 GB:

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

    c. Expandir a partição com `resizepart` . Introduza o número de partição, *1*, e um tamanho para a nova partição:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Para sair, `quit` entrar.

1. Com a partição redimensionada, verifique a consistência da partição `e2fsck` com:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Redimensionar o sistema de ficheiros `resize2fs` com:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Monte a partição até à localização desejada, tais `/datadrive` como:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Para verificar se o disco de dados foi redimensionado, utilize `df -h` . A saída de exemplo a seguir mostra que a unidade de dados */dev/sdc1* é agora de 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Próximos passos
* Se precisar de armazenamento adicional, também pode [adicionar discos de dados a um Linux VM](add-disk.md). 
* Para obter mais informações sobre encriptação de disco, consulte [Azure Disk Encryption para Linux VMs](disk-encryption-overview.md).
