---
title: Use powerShell para implantar VMs Spot Azure
description: Aprenda a usar o Azure PowerShell para implementar VMs spot para economizar em custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 234cf3f51173c53ef8ca15af4ca6f24881be3109
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547273"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Implementar VMs spot usando Azure PowerShell


A utilização de [VMs spot](spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma poupança significativa de custos. A qualquer momento em que o Azure precise de capacidade de volta, a infraestrutura Azure despejará VMs spot. Por isso, os VMs spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes de v/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços dos VMs spot são variáveis, com base na região e no SKU. Para mais informações, consulte os preços vm para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre a fixação do preço máximo, consulte [Spot VMs - Preços](spot-vms.md#pricing).

Tem opção de definir um preço máximo que está disposto a pagar, por hora, para o VM. O preço máximo de um Spot VM pode ser fixado em dólares americanos (USD), utilizando até 5 lugares decimais. Por exemplo, `0.98765`o valor seria um preço máximo de $0.98765 USD por hora. Se fixar o preço `-1`máximo para ser, o VM não será despejado com base no preço. O preço do VM será o preço atual para o local ou o preço de um VM padrão, o que sempre é menor, desde que haja capacidade e quota disponíveis.


## <a name="create-the-vm"></a>Crie a VM

Crie um spotVM utilizando [o New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) para criar a configuração. Incluir `-Priority Spot` e `-MaxPrice` definir para qualquer um:
- `-1`para que o VM não seja despejado com base no preço.
- uma quantia em dólares, até 5 dígitos. Por exemplo, `-MaxPrice .98765` significa que o VM será transferido uma vez que o preço de um spotVM seja de $98765 por hora.


Este exemplo cria um spotVM que não será deallo com base nos preços (apenas quando o Azure precisa de voltar a ter capacidade).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Após a criação do VM, pode consultar para ver o preço máximo para todos os VMs do grupo de recursos.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Passos seguintes

Também pode criar um Spot VM utilizando o [Azure CLI](../linux/spot-cli.md) ou um [modelo](../linux/spot-template.md).

Se encontrar um erro, consulte [códigos de erro](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).