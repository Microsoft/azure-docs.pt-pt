---
title: Crie um VM com um endereço IP público estático - Azure CLI / Microsoft Docs
description: Aprenda a criar um VM com um endereço IP público estático utilizando a interface de linha de comando Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 8e3e37347c8c23ccc9746bbb98ef6a822743848b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790291"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Crie uma máquina virtual com um endereço IP público estático utilizando o Azure CLI

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar com uma máquina virtual a partir da internet. Atribuir um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca se altera. Saiba mais sobre [endereços IP públicos estáticos.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmica para estática, ou para trabalhar com endereços IP privados, ver [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Os endereços IP públicos têm uma [taxa nominal,](https://azure.microsoft.com/pricing/details/ip-addresses)e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por subscrição.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Pode completar os seguintes passos do seu computador local ou utilizando a Casca de Nuvem Azure. Para utilizar o computador local, certifique-se de que tem o [AZURI CLI instalado](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Para utilizar a Casca de Nuvem Azure, selecione **Experimente no** canto superior direito de qualquer caixa de comando que se siga. A Cloud Shell assina-te em Azure.

1. Se utilizar a Casca de Nuvem, salte para o passo 2. Abra uma sessão de comando `az login`e assine em Azure com .
2. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos na região de East US Azure:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm#az-vm-create). A `--public-ip-address-allocation=static` opção atribui um endereço IP público estático à máquina virtual. O exemplo seguinte cria uma máquina virtual Ubuntu com um endereço IP público sku estático e básico chamado *myPublicIpAddress*:

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

   Se o endereço IP público tiver de `--public-ip-sku Standard` ser um SKU padrão, adicione ao comando anterior. Saiba mais sobre o [endereço IP Público SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual for adicionada ao pool de back-end de um equilíbrio público de carga Azure, o SKU do endereço IP público da máquina virtual deve corresponder ao SKU do endereço IP público do equilibrador de carga. Para mais detalhes, consulte [O Equilíbrio de Carga Sinuoso Azure](../load-balancer/skus.md).

4. Consulte o endereço IP público atribuído e confirme que foi criado como um endereço SKU estático e básico, com [a rede az public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   O Azure atribuiu um endereço IP público a partir de endereços utilizados na região onde criou a máquina virtual. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
> Não modifique as definições de endereço IP dentro do sistema operativo da máquina virtual. O sistema operativo desconhece os endereços IP públicos do Azure. Embora possa adicionar definições privadas de endereço IP ao sistema operativo, recomendamos que não o faça a menos que seja necessário, e não até depois de ler [Adicione um endereço IP privado a um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos que contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure
- Saiba mais sobre todas as [definições de endereçoip público](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuindo um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual Azure
- Saiba mais sobre a criação de máquinas virtuais [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
