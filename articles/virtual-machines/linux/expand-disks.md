---
title: Expandir discos rígidos virtuais em uma VM Linux no Azure | Microsoft Docs
description: Saiba como expandir discos rígidos virtuais em uma VM Linux com o CLI do Azure
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f892857d74150ee42cc2ea4b5c996feac3d1cfa2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695618"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expanda discos rígidos virtuais em uma VM Linux com o CLI do Azure

Este artigo descreve como expandir discos gerenciados para uma VM (máquina virtual) do Linux com o CLI do Azure. Você pode [adicionar discos de dados](add-disk.md) para fornecer espaço de armazenamento adicional e também pode expandir um disco de dados existente. O tamanho do disco rígido virtual padrão para o sistema operacional (SO) normalmente é de 30 GB em uma VM do Linux no Azure. 

> [!WARNING]
> Sempre certifique-se de que seu sistema de arquivos está em um estado íntegro, o tipo de tabela de partição de disco dará suporte ao novo tamanho e certifique-se de que o backup dos dados seja feito antes de executar operações de redimensionamento de disco. Para obter mais informações, consulte [fazer backup de VMs do Linux no Azure](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Expandir um disco gerenciado do Azure
Verifique se você tem as [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e se está conectado a uma conta do Azure usando [AZ login](/cli/azure/reference-index#az-login).

Este artigo requer uma VM existente no Azure com pelo menos um disco de dados anexado e preparado. Se você ainda não tiver uma VM que possa usar, consulte [criar e preparar uma VM com discos de dados](tutorial-manage-disks.md#create-and-attach-disks).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo, como MyResource e *myVM* por seus próprios valores.

1. As operações em discos rígidos virtuais não podem ser executadas com a VM em execução. Desaloque sua VM com [AZ VM DEALLOCATE](/cli/azure/vm#az-vm-deallocate). O exemplo a seguir Desaloca a VM denominada *myVM* no grupo de recursoschamado MyResource Group:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A VM deve ser desalocada para expandir o disco rígido virtual. Parar a VM com `az vm stop` o não libera os recursos de computação. Para liberar recursos de computação, `az vm deallocate`use.

1. Exiba uma lista de discos gerenciados em um grupo de recursos com a [lista de discos AZ](/cli/azure/disk#az-disk-list). O exemplo a seguir exibe uma lista de discos gerenciados no grupo derecursos chamado MyResource Group:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expanda o disco necessário com [AZ Disk Update](/cli/azure/disk#az-disk-update). O exemplo a seguir expande o disco gerenciado chamado *myDataDisk* para *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Quando você expande um disco gerenciado, o tamanho atualizado é arredondado para o tamanho do disco gerenciado mais próximo. Para obter uma tabela dos tamanhos e camadas do disco gerenciado disponíveis, confira [visão geral do Azure Managed disks-preços e cobrança](../windows/managed-disks-overview.md).

1. Inicie sua VM com [AZ VM Start](/cli/azure/vm#az-vm-start). O exemplo a seguir inicia a VM chamada *myVM* no grupo de recursoschamado MyResource Group:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Expandir uma partição de disco e um sistema de arquivos
Para usar um disco expandido, expanda a partição subjacente e o sistema de arquivos.

1. SSH para sua VM com as credenciais apropriadas. Você pode ver o endereço IP público de sua VM com [AZ VM show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Expanda a partição subjacente e o sistema de arquivos.

    a. Se o disco já estiver montado, desmonte-o:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Use `parted` para exibir informações de disco e redimensionar a partição:

    ```bash
    sudo parted /dev/sdc
    ```

    Exiba informações sobre o layout de partição existente `print`com. A saída é semelhante ao exemplo a seguir, que mostra que o disco subjacente é 215 GB:

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

    c. Expanda a partição `resizepart`com. Insira o número da partição, *1*e um tamanho para a nova partição:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Para sair, insira `quit`.

1. Com a partição redimensionada, verifique a consistência da partição `e2fsck`com:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Redimensione o `resize2fs`sistema de arquivos com:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Monte a partição no local desejado, como `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Para verificar se o disco de dados foi redimensionado, `df -h`use. A saída de exemplo a seguir mostra a unidade de dados */dev/sdc1* agora é 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Passos Seguintes
* Se você precisar de armazenamento adicional, também poderá [adicionar discos de dados a uma VM do Linux](add-disk.md). 
* Para obter mais informações sobre criptografia de disco, consulte [criptografar discos em uma VM Linux usando o CLI do Azure](encrypt-disks.md).
