---
title: Copiar um Linux VM usando o Azure CLI
description: Aprenda a criar uma cópia do seu VM Azure Linux utilizando discos Azure CLI e Geridos.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 406eda6902ae451c7fdf79e1bd1215c035a66750
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792229"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Crie uma cópia de um VM Linux utilizando discos Azure CLI e Geridos

Este artigo mostra-lhe como criar uma cópia da sua máquina virtual Azure (VM) executando o Linux utilizando o Azure CLI. Para copiar, criar, armazenar e partilhar imagens VM em escala, ver Galerias de [Imagem Partilhada](shared-images.md).

Também pode [fazer o upload e criar um VM a partir de um VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos

-   Instale a [CLI do Azure](/cli/azure/install-az-cli2).

-   Inicie sessão numa conta Azure com [login az](/cli/azure/reference-index#az-login).

-   Tenha um VM Azure para usar como fonte para a sua cópia.

## <a name="stop-the-source-vm"></a>Pare a fonte VM

Deslocar a fonte VM utilizando [adesalações az vm](/cli/azure/vm#az-vm-deallocate).
O exemplo seguinte desafeta o VM nomeado *myVM* no grupo de recursos *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Copiar a fonte VM

Para copiar um VM, cria-se uma cópia do disco rígido virtual subjacente. Este processo cria um disco rígido virtual especializado (VHD) como um Disco Gerido que contém a mesma configuração e configurações que o VM de origem.

Para mais informações sobre Managed Disks do Azure, veja [Azure Managed Disks Overview (Descrição geral dos Managed Disks do Azure)](../windows/managed-disks-overview.md). 

1.  Enumerar cada VM e o nome do seu disco OS com [lista az vm](/cli/azure/vm#az-vm-list). O exemplo seguinte lista todos os VMs do grupo de recursos chamado *myResourceGroup:*
    
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

1.  Copiar o disco criando um novo disco gerido e utilizando o [disco az criar](/cli/azure/disk#az-disk-create). O exemplo seguinte cria um disco chamado *myCopiedDisk* a partir do disco gerido chamado *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verifique os discos geridos agora no seu grupo de recursos utilizando a lista de [discos Az](/cli/azure/disk#az-disk-list). O exemplo seguinte lista os discos geridos no grupo de recursos denominado *myResourceGroup:*

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Criar uma rede virtual

Os seguintes passos opcionais criam uma nova rede virtual, subnet, endereço IP público e cartão de interface de rede virtual (NIC).

Se estiver a copiar um VM para fins de resolução de problemas ou implementações adicionais, pode não querer utilizar um VM numa rede virtual existente.

Se quiser criar uma infraestrutura de rede virtual para os seus VMs copiados, siga os próximos passos. Se não quiser criar uma rede virtual, salte para [Criar um VM](#create-a-vm).

1.  Criar a rede virtual utilizando a [rede az vnet criar](/cli/azure/network/vnet#az-network-vnet-create). O exemplo seguinte cria uma rede virtual chamada *myVnet* e uma subnet chamada *mySubnet:*

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Criar um IP público utilizando a [rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create). O exemplo seguinte cria um IP público chamado *myPublicIP* com o nome DNS dos *meus publicdns*. (Como o nome DNS deve ser único, forneça um nome único.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Crie o NIC utilizando a [criação de nic de rede az](/cli/azure/network/nic#az-network-nic-create).
    O exemplo seguinte cria um NIC chamado *myNic* que está ligado à subnet *da mySubnet:*

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Criar uma VM

Crie um VM utilizando [az vm criar](/cli/azure/vm#az-vm-create).

Especifique o disco copiado gerido`--attach-os-disk`para utilizar como o disco OS (), da seguinte forma:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar uma galeria de [imagens partilhada](shared-images.md) para gerir imagens VM.
