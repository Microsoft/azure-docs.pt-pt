---
title: Copiar uma VM do Linux usando CLI do Azure
description: Saiba como criar uma cópia de sua VM Linux do Azure usando CLI do Azure e Managed Disks.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: a6a8b766efdc781df1fea29da81dc48090875ad7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036579"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Criar uma cópia de uma VM do Linux usando CLI do Azure e Managed Disks

Este artigo mostra como criar uma cópia de sua VM (máquina virtual) do Azure executando o Linux usando o CLI do Azure e o modelo de implantação de Azure Resource Manager. 

Você também pode [carregar e criar uma VM de um VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos

-   Instale a [CLI do Azure](/cli/azure/install-az-cli2).

-   Entre em uma conta do Azure com [AZ login](/cli/azure/reference-index#az-login).

-   Ter uma VM do Azure para usar como a origem da sua cópia.

## <a name="stop-the-source-vm"></a>Parar a VM de origem

Desaloque a VM de origem usando [AZ VM DEALLOCATE](/cli/azure/vm#az-vm-deallocate).
O exemplo a seguir Desaloca a VM chamada *myVM* no grupo de recursos *MyResource*Group:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Copiar a VM de origem

Para copiar uma VM, você cria uma cópia do disco rígido virtual subjacente. Esse processo cria um VHD (disco rígido virtual) especializado como um disco gerenciado que contém a mesma configuração e configurações que a VM de origem.

Para mais informações sobre Managed Disks do Azure, veja [Azure Managed Disks Overview (Descrição geral dos Managed Disks do Azure)](../windows/managed-disks-overview.md). 

1.  Liste cada VM e o nome de seu disco do sistema operacional com [AZ VM List](/cli/azure/vm#az-vm-list). O exemplo a seguir lista todas as VMs no grupo de recursos chamado *MyResource*Group:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Copie o disco criando um novo disco gerenciado e usando [AZ Disk Create](/cli/azure/disk#az-disk-create). O exemplo a seguir cria um disco chamado *myCopiedDisk* do disco gerenciado chamado *MyDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verifique os discos gerenciados agora em seu grupo de recursos usando [AZ Disk List](/cli/azure/disk#az-disk-list). O exemplo a seguir lista os discos gerenciados no grupo de recursos chamado *MyResource*Group:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Configurar uma rede virtual

As etapas opcionais a seguir criam uma nova rede virtual, sub-rede, endereço IP público e NIC (placa de interface de rede virtual).

Se você estiver copiando uma VM para fins de solução de problemas ou implantações adicionais, talvez não queira usar uma VM em uma rede virtual existente.

Se você quiser criar uma infraestrutura de rede virtual para suas VMs copiadas, siga as próximas etapas. Se você não quiser criar uma rede virtual, pule para [criar uma VM](#create-a-vm).

1.  Crie a rede virtual usando [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). O exemplo a seguir cria uma rede virtual chamada *myVnet* e uma sub-rede chamada *mysubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Crie um IP público usando [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). O exemplo a seguir cria um IP público chamado *myPublicIP* com o nome DNS de *mypublicdns*. (Como o nome DNS deve ser exclusivo, forneça um nome exclusivo.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Crie a NIC usando [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create).
    O exemplo a seguir cria uma NIC chamada *myNic* que está anexada à sub-rede *mysubnet* :

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Criar uma VM

Crie uma VM usando [AZ VM Create](/cli/azure/vm#az-vm-create).

Especifique o disco gerenciado copiado para usar como o disco do sistema operacional (`--attach-os-disk`), da seguinte maneira:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Passos seguintes

Para saber como usar CLI do Azure para gerenciar sua nova VM, consulte [CLI do Azure comandos para o Azure Resource Manager](../azure-cli-arm-commands.md).
