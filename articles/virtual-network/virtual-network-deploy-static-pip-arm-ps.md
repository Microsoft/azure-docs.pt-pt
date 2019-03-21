---
title: Criar uma VM com um endereço IP público estático - PowerShell | Documentos da Microsoft
description: Saiba como criar uma VM com um endereço IP público estático através do PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 422a48a3671974248e9e3258be16d3537713f762
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090003"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Criar uma máquina virtual com um endereço IP público estático através do PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar a uma máquina virtual a partir da internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca é alterado. Saiba mais sobre [endereços IP públicos estáticos](virtual-network-ip-addresses-overview-arm.md#allocation-method). Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmico para estático, ou para trabalhar com endereços IP privados, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Endereços IP públicos têm um [encargo nominal](https://azure.microsoft.com/pricing/details/ip-addresses)e há um [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ao número de endereços IP públicos, que podem ser utilizados por subscrição.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Pode concluir as etapas a seguir do computador local ou com o Azure Cloud Shell. Para utilizar o seu computador local, certifique-se de que tem o [Azure PowerShell instalada](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Para utilizar o Azure Cloud Shell, selecione **experimentar** no canto superior direito da caixa qualquer comando que se segue. O Cloud Shell iniciada para o Azure.

1. Se utilizar o Cloud Shell, avance para o passo 2. Abra uma sessão de comandos e o início de sessão para o Azure `Connect-AzAccount`.
2. Criar um grupo de recursos com o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) comando. O exemplo seguinte cria um grupo de recursos na região Leste e.u.a. centro-Azure:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Criar uma máquina virtual com o [New-AzVM](/powershell/module/az.Compute/New-azVM) comando. O `-AllocationMethod "Static"` opção atribui um endereço IP público estático para a máquina virtual. O exemplo seguinte cria uma máquina virtual do Windows Server com um estático, básico SKU endereço IP público com o nome *myPublicIpAddress*. Quando solicitado, forneça um nome de utilizador e palavra-passe a utilizar como credenciais de início de sessão para a máquina virtual:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Se o endereço IP público tem de ser um SKU standard, tem de [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address), [criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface), [atribuir o endereço IP público à interface de rede](virtual-network-network-interface-addresses.md#add-ip-addresses)e, em seguida [criar uma máquina virtual com a interface de rede](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm), na separar passos. Saiba mais sobre [endereço IP público SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual será adicionada ao agrupamento de back-end de um balanceador de carga do Azure público, o SKU de endereço IP público da máquina virtual tem de corresponder ao SKU do endereço IP público do Balanceador de carga. Para obter detalhes, consulte [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Ver o endereço IP público atribuído e confirme que tenha sido criado como um endereço estático, com [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure atribuído um endereço IP público a partir de endereços utilizados na região que criou a máquina virtual numa. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
> Não modifique as definições do endereço IP no sistema de operativo da máquina virtual. Desconhece o sistema operativo de endereços IP públicos do Azure. Embora possa adicionar definições de endereço IP privadas para o sistema operativo, recomendamos que não se o fizer, a menos que necessário e não até depois de ler [adicionar um endereço IP privado para um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure
- Saiba mais sobre todas as [definições do endereço IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e a atribuição de um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) para uma máquina virtual do Azure
- Saiba mais sobre a criação [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais
