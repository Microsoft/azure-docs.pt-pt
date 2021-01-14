---
title: Copie um Linux VM usando Azure CLI
description: Saiba como criar uma cópia do seu VM Azure Linux utilizando discos Azure CLI e Managed Disks.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.openlocfilehash: 7f9ac0ab9eacb90bde70c85ea06bc19a18aa0c05
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201149"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Crie uma cópia de um Linux VM utilizando discos Azure CLI e Managed

Este artigo mostra-lhe como criar uma cópia da sua máquina virtual Azure (VM) executando o Linux utilizando o CLI Azure. Para copiar, criar, armazenar e partilhar imagens VM em escala, consulte [Galerias de Imagem Partilhadas.](../shared-images-cli.md)

Também pode [fazer upload e criar um VM a partir de um VHD](upload-vhd.md).

## <a name="prerequisites"></a>Pré-requisitos

-   Instale o [Azure CLI](/cli/azure/install-az-cli2).

-   Inscreva-se numa conta Azure com [login az](/cli/azure/reference-index#az-login).

-   Tenha um Azure VM para usar como fonte para a sua cópia.

## <a name="stop-the-source-vm"></a>Pare a fonte VM

Translocar a fonte VM utilizando [o deallocato az vm](/cli/azure/vm#az-vm-deallocate).
O exemplo seguinte é o que a VM nomeou *myVM* no grupo de recursos *myResourceGroup:*

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Copiar a fonte VM

Para copiar um VM, cria-se uma cópia do disco rígido virtual subjacente. Este processo cria um disco rígido virtual especializado (VHD) como um Disco Gerido que contém a mesma configuração e configurações que o VM de origem.

Para mais informações sobre Managed Disks do Azure, veja [Azure Managed Disks Overview (Descrição geral dos Managed Disks do Azure)](../managed-disks-overview.md). 

1.  Listar cada VM e o nome do seu disco de oss com [a lista az vm](/cli/azure/vm#az-vm-list). O exemplo a seguir lista todos os VMs do grupo de recursos denominado *myResourceGroup:*
    
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

1.  Copie o disco criando um novo disco gerido e utilizando [o disco az create](/cli/azure/disk#az-disk-create). O exemplo a seguir cria um disco chamado *myCopiedDisk* a partir do disco gerido chamado *myDisk:*

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verifique agora os discos geridos no seu grupo de recursos utilizando [a lista de discos az](/cli/azure/disk#az-disk-list). O exemplo a seguir lista os discos geridos no grupo de recursos denominado *myResourceGroup:*

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Criar uma rede virtual

Os seguintes passos opcionais criam uma nova rede virtual, sub-rede, endereço IP público e cartão de interface de rede virtual (NIC).

Se estiver a copiar um VM para efeitos de resolução de problemas ou implementações adicionais, poderá não querer utilizar um VM numa rede virtual existente.

Se pretender criar uma infraestrutura de rede virtual para os seus VMs copiados, siga os próximos passos. Se não quiser criar uma rede virtual, salte para [criar um VM](#create-a-vm).

1.  Crie a rede virtual utilizando [a rede Az vnet create](/cli/azure/network/vnet#az-network-vnet-create). O exemplo a seguir cria uma rede virtual chamada *myVnet* e uma sub-rede chamada *mySubnet:*

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Crie um IP público utilizando [a rede az public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). O exemplo a seguir cria um IP público chamado *myPublicIP* com o nome DNS de *mypublicdns*. (Porque o nome DNS deve ser único, fornecer um nome único.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Crie o NIC utilizando [a az network nic create](/cli/azure/network/nic#az-network-nic-create).
    O exemplo a seguir cria um NIC chamado *myNic* que está ligado à *sub-rede mySubnet:*

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Criar uma VM

Criar um VM utilizando [az vm criar](/cli/azure/vm#az-vm-create).

Especifique o disco gerido copiado para usar como disco de so ( `--attach-os-disk` ), da seguinte forma:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar uma [galeria de imagens partilhada](../shared-images-cli.md) para gerir imagens VM.
