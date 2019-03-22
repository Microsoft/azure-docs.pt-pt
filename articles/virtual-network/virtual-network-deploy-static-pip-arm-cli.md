---
title: Criar uma VM com um endereço IP público estático - CLI do Azure | Documentos da Microsoft
description: Saiba como criar uma VM com um endereço IP público estático usando a interface de linha de comandos (CLI) do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: eafdbf731ce6ae37c321712d7574ce578e704cc0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108431"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Criar uma máquina virtual com um endereço IP público estático, com a CLI do Azure

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar a uma máquina virtual a partir da internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca é alterado. Saiba mais sobre [endereços IP públicos estáticos](virtual-network-ip-addresses-overview-arm.md#allocation-method). Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmico para estático, ou para trabalhar com endereços IP privados, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Endereços IP públicos têm um [encargo nominal](https://azure.microsoft.com/pricing/details/ip-addresses)e há um [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ao número de endereços IP públicos, que podem ser utilizados por subscrição.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Pode concluir as etapas a seguir do computador local ou com o Azure Cloud Shell. Para utilizar o seu computador local, certifique-se de que tem o [CLI do Azure instalada](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Para utilizar o Azure Cloud Shell, selecione **experimentar** no canto superior direito da caixa qualquer comando que se segue. O Cloud Shell iniciada para o Azure.

1. Se utilizar o Cloud Shell, avance para o passo 2. Abra uma sessão de comandos e o início de sessão para o Azure `az login`.
2. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos na região Leste e.u.a. centro-Azure:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm#az-vm-create). O `--public-ip-address-allocation=static` opção atribui um endereço IP público estático para a máquina virtual. O exemplo seguinte cria uma máquina virtual do Ubuntu com um estático, básico SKU endereço IP público com o nome *myPublicIpAddress*:

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

   Se o endereço IP público tem de ser um SKU standard, adicione `--public-ip-sku Standard` para o comando anterior. Saiba mais sobre [endereço IP público SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual será adicionada ao agrupamento de back-end de um balanceador de carga do Azure público, o SKU de endereço IP público da máquina virtual tem de corresponder ao SKU do endereço IP público do Balanceador de carga. Para obter detalhes, consulte [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Ver o endereço IP público atribuído e confirme que tenha sido criado como um endereço SKU estático, básico, com [show de public-ip de rede de az](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure atribuído um endereço IP público a partir de endereços utilizados na região que criou a máquina virtual numa. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
> Não modifique as definições do endereço IP no sistema de operativo da máquina virtual. Desconhece o sistema operativo de endereços IP públicos do Azure. Embora possa adicionar definições de endereço IP privadas para o sistema operativo, recomendamos que não se o fizer, a menos que necessário e não até depois de ler [adicionar um endereço IP privado para um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos que contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure
- Saiba mais sobre todas as [definições do endereço IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e a atribuição de um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) para uma máquina virtual do Azure
- Saiba mais sobre a criação [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais
