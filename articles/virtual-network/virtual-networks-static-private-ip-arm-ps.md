---
title: Criar um VM com um endereço IP estático privado - Azure PowerShell
description: Saiba como criar uma máquina virtual com um endereço IP privado utilizando o PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 43c2e2c8905ca60e59caed3fdf1b4920f509fd88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98783789"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Crie uma máquina virtual com um endereço IP estático privado usando PowerShell

Pode criar uma máquina virtual (VM) com um endereço IP estático privado. Atribua um endereço IP estático privado, em vez de um endereço dinâmico, se pretender selecionar qual endereço de uma sub-rede é atribuído a um VM. Saiba mais sobre [endereços IP privados estáticos](./public-ip-addresses.md#allocation-method). Para alterar um endereço IP privado atribuído a um VM existente de dinâmico para estático, ou para trabalhar com endereços IP públicos, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Pode completar os seguintes passos a partir do seu computador local ou utilizando a Azure Cloud Shell. Para utilizar o computador local, certifique-se de que tem o [Azure PowerShell instalado](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Para utilizar a Azure Cloud Shell, selecione **Try It** no canto superior direito de qualquer caixa de comando que se segue. A Cloud Shell inscreve-te no Azure.

1. Se utilizar a Cloud Shell, salte para o passo 2. Abra uma sessão de comando e assine em Azure com `Connect-AzAccount` .
2. Criar um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) O exemplo a seguir cria um grupo de recursos na região de Azure oriental dos EUA:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Crie uma configuração de sub-rede e rede virtual com os comandos [New-AzVirtualNetSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) e [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

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

4. Crie uma interface de rede na rede virtual e atribua um endereço IP privado da sub-rede à interface de rede com os comandos [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) e [New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

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

5. Crie uma configuração VM com [New-AzVMConfig,](/powershell/module/Az.Compute/New-AzVMConfig)e, em seguida, crie o VM com [New-AzVM](/powershell/module/az.Compute/New-azVM). Quando solicitado, forneça um nome de utilizador e palavra-passe para ser usado como o sinal em credenciais para o VM:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Embora possa adicionar definições privadas de endereço IP ao sistema operativo, recomendamos que não o faça até depois de ler [Adicione um endereço IP privado a um sistema operativo](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Para aceder ao VM a partir da internet, tem de atribuir um endereço IP público ao VM. Também pode alterar uma atribuição dinâmica de endereço IP privado para uma atribuição estática. Para mais informações, consulte [adicionar ou alterar endereços IP](virtual-network-network-interface-addresses.md). Além disso, recomenda-se que limite o tráfego de rede ao seu VM associando um grupo de segurança de rede à interface de rede, a sub-rede em que criou a interface de rede, ou ambos. Para mais informações, consulte [Gerir grupos de segurança de rede](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [endereços IP privados](./private-ip-addresses.md) e atribuindo um [endereço IP estático privado](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual Azure.
- Saiba mais sobre a criação de máquinas virtuais [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows.](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
