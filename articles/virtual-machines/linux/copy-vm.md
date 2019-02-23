---
title: Copiar uma VM do Linux com a CLI do Azure | Documentos da Microsoft
description: Saiba como criar uma cópia da sua VM do Linux do Azure com a CLI do Azure e o Managed Disks.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: abc8c09a51104c81b827afb7055531df98691714
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731349"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Criar uma cópia de uma VM do Linux com a CLI do Azure e o Managed Disks

Este artigo mostra-lhe como criar uma cópia da sua máquina virtual do Azure (VM) em execução no Linux com a CLI do Azure e o modelo de implementação Azure Resource Manager. 

Também pode [carregar e criar uma VM a partir de um VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos

-   Instale a [CLI do Azure](/cli/azure/install-az-cli2).

-   Iniciar sessão numa conta do Azure com [início de sessão az](/cli/azure/reference-index#az-login).

-   Tem uma VM do Azure para utilizar como origem para obter uma cópia.

## <a name="stop-the-source-vm"></a>Parar a VM de origem

Desaloque a VM de origem, utilizando [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
O exemplo seguinte desaloca a VM com o nome *myVM* no grupo de recursos *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Copiar a VM de origem

Para copiar uma VM, crie uma cópia do disco rígido virtual subjacente. Este processo cria um disco de rígido virtual (VHD) especializado, como um disco gerido, que contém a mesma configuração e definições da VM de origem.

Para mais informações sobre Managed Disks do Azure, veja [Azure Managed Disks Overview (Descrição geral dos Managed Disks do Azure)](../windows/managed-disks-overview.md). 

1.  Lista de cada VM e o nome do seu sistema operativo do disco com [lista de VMS de az](/cli/azure/vm#az-vm-list). O exemplo seguinte lista todas as VMs no grupo de recursos chamado *myResourceGroup*:
    
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

1.  Copie o disco ao criar um novo disco gerido e utilizando [criar disco de az](/cli/azure/disk#az-disk-create). O exemplo seguinte cria um disco chamado *myCopiedDisk* do disco gerido com o nome *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verifique se os discos geridos agora no grupo de recursos, utilizando [lista de disco de az](/cli/azure/disk#az-disk-list). O exemplo seguinte lista os discos geridos no grupo de recursos com o nome *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Configurar uma rede virtual

Os seguintes passos opcionais criam uma nova rede virtual, sub-rede, endereço IP público e cartão de interface de rede virtual (NIC).

Se estiver a copiar uma VM para fins de implementações adicionais de resolução de problemas, pode não querer utilizar uma VM numa rede virtual existente.

Se quiser criar uma infraestrutura de rede virtual para as suas VMs copiados, siga os passos seguintes. Se não quiser criar uma rede virtual, avance para o [criar uma VM](#create-a-vm).

1.  Criar a rede virtual, utilizando [vnet de rede de az criar](/cli/azure/network/vnet#az-network-vnet-create). O exemplo seguinte cria uma rede virtual denominada *myVnet* e uma sub-rede denominada *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Criar um IP público utilizando [criar a rede de az public-ip](/cli/azure/network/public-ip#az-network-public-ip-create). O exemplo seguinte cria um IP público com o nome *myPublicIP* com o nome DNS *mypublicdns*. (Como o nome DNS tem de ser exclusivo, fornecer um nome exclusivo.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Criar com a NIC [nic da rede de az criar](/cli/azure/network/nic#az-network-nic-create).
    O exemplo seguinte cria um NIC com o nome *myNic* que está anexada a *mySubnet* sub-rede:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Criar uma VM

Criar uma VM utilizando [az vm criar](/cli/azure/vm#az-vm-create).

Especifique o disco gerido copiado para utilizar como o disco do SO (`--attach-os-disk`), da seguinte forma:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Passos Seguintes

Para saber como utilizar a CLI do Azure para gerir a sua nova VM, veja [comandos da CLI do Azure para o Azure Resource Manager](../azure-cli-arm-commands.md).
