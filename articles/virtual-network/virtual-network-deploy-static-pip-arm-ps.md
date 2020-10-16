---
title: Criar um VM com um endereço IP público estático - PowerShell Microsoft Docs
description: Crie uma máquina virtual (VM) com um endereço IP público estático utilizando o PowerShell. Endereços IP públicos estáticos são endereços que nunca mudam.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 3ca83836771af8448f6510ab27d0ac5f2973b35c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287711"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Criar uma máquina virtual com um endereço IP público estático usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar a uma máquina virtual a partir da internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca se altera. Saiba mais sobre [endereços IP públicos estáticos.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmica para estática, ou para trabalhar com endereços IP privados, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Os endereços IP públicos têm uma [taxa nominal](https://azure.microsoft.com/pricing/details/ip-addresses), e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que pode utilizar por subscrição.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Pode completar os seguintes passos a partir do seu computador local ou utilizando a Azure Cloud Shell. Para utilizar o computador local, certifique-se de que tem o [Azure PowerShell instalado](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Para utilizar a Azure Cloud Shell, selecione **Try It** no canto superior direito de qualquer caixa de comando que se segue. A Cloud Shell inscreve-te no Azure.

1. Se utilizar a Cloud Shell, salte para o passo 2. Abra uma sessão de comando e assine em Azure com `Connect-AzAccount` .
2. Criar um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) O exemplo a seguir cria um grupo de recursos na região de Azure oriental dos EUA:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Crie uma máquina virtual com o comando [New-AzVM.](/powershell/module/az.Compute/New-azVM) A `-AllocationMethod "Static"` opção atribui um endereço IP público estático à máquina virtual. O exemplo a seguir cria uma máquina virtual do Windows Server com um endereço IP público SKU estático e básico chamado *myPublicIpAddress*. Quando solicitado, forneça um nome de utilizador e palavra-passe para ser usado como o sinal em credenciais para a máquina virtual:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Se o endereço IP público tiver de ser um SKU padrão, tem de [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address), criar uma interface de [rede](virtual-network-network-interface.md#create-a-network-interface), atribuir o endereço IP público à interface [de rede](virtual-network-network-interface-addresses.md#add-ip-addresses), e, em seguida, criar uma máquina virtual com a interface [de rede](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm), em etapas separadas. Saiba mais sobre [o endereço IP PÚBLICO SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual for adicionada ao pool traseiro de um Balançador de Carga Azure público, o SKU do endereço IP público da máquina virtual deve corresponder ao SKU do endereço IP público do balançador de carga. Para mais detalhes, consulte [o Balançador de Carga Azure](../load-balancer/skus.md).

4. Ver o endereço IP público atribuído e confirmar que foi criado como um endereço estático, com [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   O Azure atribuiu um endereço IP público a partir de endereços utilizados na região em que criou a máquina virtual. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
> Não modifique as definições do endereço IP dentro do sistema operativo da máquina virtual. O sistema operativo desconhece os endereços IP públicos do Azure. Embora possa adicionar definições privadas de endereço IP ao sistema operativo, recomendamos que não o faça a menos que seja necessário, e só depois de ler [Adicione um endereço IP privado a um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuindo um [endereço IP estático privado](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual Azure
- Saiba mais sobre a criação de máquinas virtuais [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
