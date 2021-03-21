---
title: Criar um VM com um endereço IP público estático - Azure CLI | Microsoft Docs
description: Saiba como criar um VM com um endereço IP público estático utilizando a interface de linha de comando Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: c4062dd086eeee712376a402da2792352fa3c3ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221347"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Criar uma máquina virtual com um endereço IP público estático usando o Azure CLI

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar a uma máquina virtual a partir da internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca se altera. Saiba mais sobre [endereços IP públicos estáticos.](./public-ip-addresses.md#allocation-method) Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmica para estática, ou para trabalhar com endereços IP privados, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Os endereços IP públicos têm uma [taxa nominal](https://azure.microsoft.com/pricing/details/ip-addresses), e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que pode utilizar por subscrição.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Pode completar os seguintes passos a partir do seu computador local ou utilizando a Azure Cloud Shell. Para utilizar o computador local, certifique-se de que tem o [CLI Azure instalado](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Para utilizar a Azure Cloud Shell, selecione **Try It** no canto superior direito de qualquer caixa de comando que se segue. A Cloud Shell inscreve-te no Azure.

1. Se utilizar a Cloud Shell, salte para o passo 2. Abra uma sessão de comando e assine em Azure com `az login` .
2. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos na região de Azure oriental dos EUA:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm#az-vm-create). A `--public-ip-address-allocation=static` opção atribui um endereço IP público estático à máquina virtual. O exemplo a seguir cria uma máquina virtual Ubuntu com um endereço IP público SKU estático e básico chamado *myPublicIpAddress*:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Se o endereço IP público tiver de ser um SKU padrão, adicione `--public-ip-sku Standard` ao comando anterior. Saiba mais sobre [o endereço IP PÚBLICO SKUs](./public-ip-addresses.md#sku). Se a máquina virtual for adicionada ao pool traseiro de um Balançador de Carga Azure público, o SKU do endereço IP público da máquina virtual deve corresponder ao SKU do endereço IP público do balançador de carga. Para mais detalhes, consulte [o Balançador de Carga Azure](../load-balancer/skus.md).

4. Ver o endereço IP público atribuído e confirmar que foi criado como um endereço SKU estático e básico, com [show público-ip da rede AZ](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   O Azure atribuiu um endereço IP público a partir de endereços utilizados na região em que criou a máquina virtual. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
> Não modifique as definições do endereço IP dentro do sistema operativo da máquina virtual. O sistema operativo desconhece os endereços IP públicos do Azure. Embora possa adicionar definições privadas de endereço IP ao sistema operativo, recomendamos que não o faça a menos que seja necessário, e só depois de ler [Adicione um endereço IP privado a um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos que contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](./private-ip-addresses.md) e atribuindo um [endereço IP estático privado](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual Azure
- Saiba mais sobre a criação de máquinas virtuais [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
