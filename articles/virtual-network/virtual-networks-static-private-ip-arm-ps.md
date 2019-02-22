---
title: Criar uma VM com um endereço IP privado estático - Azure PowerShell | Documentos da Microsoft
description: Saiba como criar uma máquina virtual com um endereço IP privado com o PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ee990258be5ac6396754d5c42beb2ffd78ad7aca
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649236"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Criar uma máquina virtual com um endereço IP privado estático com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode criar uma máquina virtual (VM) com um endereço IP privado estático. Atribua um endereço IP privado estático, em vez de um endereço dinâmico, para garantir que o endereço nunca é alterado. Saiba mais sobre [endereços IP privados estáticos](virtual-network-ip-addresses-overview-arm.md#allocation-method). Para alterar um endereço IP privado atribuído a uma VM existente de dinâmico para estático, ou para trabalhar com endereços IP públicos, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Pode concluir as etapas a seguir do computador local ou com o Azure Cloud Shell. Para utilizar o seu computador local, certifique-se de que tem o [Azure PowerShell instalada](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Para utilizar o Azure Cloud Shell, selecione **experimentar** no canto superior direito da caixa qualquer comando que se segue. O Cloud Shell iniciada para o Azure.

1. Se utilizar o Cloud Shell, avance para o passo 2. Abra uma sessão de comandos e o início de sessão para o Azure `Connect-AzAccount`.
2. Criar um grupo de recursos com o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) comando. O exemplo seguinte cria um grupo de recursos na região Leste e.u.a. centro-Azure:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Criar uma configuração de sub-rede e rede virtual com o [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) e [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) comandos:

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Criar uma interface de rede na rede virtual e atribuir um endereço IP privado a partir da sub-rede para a interface de rede com o [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) e [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) comandos:

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Crie uma configuração de VM com [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)e, em seguida, crie a VM com [New-AzVM](/powershell/module/az.Compute/New-azVM). Quando solicitado, forneça um nome de utilizador e palavra-passe a utilizar como credenciais de início de sessão para a VM:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Embora possa adicionar definições de endereço IP privadas para o sistema operativo, recomendamos que não se o fizer, até depois de ler [adicionar um endereço IP privado para um sistema operativo](virtual-network-network-interface-addresses.md#private).
>
>
<a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>

> [!IMPORTANT]
> Para aceder à VM a partir da internet, tem de atribuir um endereço IP público à VM. Também pode alterar a atribuição de endereços IP privada dinâmica para uma atribuição estática. Para obter detalhes, consulte [endereços IP de adicionar ou alterar](virtual-network-network-interface-addresses.md). Além disso, é recomendável que limite o tráfego de rede para a VM ao associar um grupo de segurança de rede para a interface de rede, a sub-rede que criou a interface de rede no ou ambos. Para obter detalhes, consulte [gerir grupos de segurança de rede](manage-network-security-group.md).
>
>

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e a atribuição de um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) para uma máquina virtual do Azure
- Saiba mais sobre a criação [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais