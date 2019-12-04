---
title: Usar o PowerShell para implantar VMs de ponto do Azure
description: Saiba como usar Azure PowerShell para implantar VMs pontuais para economizar em custos.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/14/2019
ms.author: cynthn
ms.openlocfilehash: 8752522e4b5a7b91778d6eb2cd8e4ba3bac95da0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782128"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Versão prévia: implantar VMs especiais usando Azure PowerShell


O uso de [VMs pontuais](spot-vms.md) permite que você tire proveito de nossa capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuais. Portanto, as VMs pontuais são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e muito mais.

Os preços para VMs pontuais são variáveis, com base na região e SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre como definir o preço máximo, consulte [VMs pontuais – preços](spot-vms.md#pricing).

Você tem a opção de definir um preço máximo que está disposto a pagar, por hora, para a VM. O preço máximo de uma VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0.98765 USD por hora. Se você definir o preço máximo como `-1`, a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível.

> [!IMPORTANT]
> Atualmente, as instâncias especiais estão em visualização pública.
> Esta versão de visualização não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Para a parte inicial da visualização pública, as instâncias Spot terão um preço fixo, portanto, não haverá nenhuma remoção baseada em preço.


## <a name="create-the-vm"></a>Crie a VM

Crie um spotVM usando [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) para criar a configuração. Inclua `-Priority Spot` e defina `-MaxPrice` como:
- `-1` para que a VM não seja removida com base no preço.
- um valor de dólar, até 5 dígitos. Por exemplo, `-MaxPrice .98765` significa que a VM será desalocada quando o preço de um spotVM vai cerca de US $98765 por hora.

> [!IMPORTANT]
> Para a parte inicial da visualização pública, você pode definir um preço máximo, mas ele será ignorado. As VMs pontuais terão um preço fixo, portanto, não haverá nenhuma remoção baseada em preço.


Este exemplo cria um spotVM que não será desalocado com base no preço (somente quando o Azure precisar da capacidade de volta).

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

Depois que a VM for criada, você poderá consultar para ver o preço máximo de todas as VMs no grupo de recursos.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Passos seguintes

Você também pode criar uma VM Spot usando o [CLI do Azure](../linux/spot-cli.md) ou um [modelo](../linux/spot-template.md).

Se você encontrar um erro, consulte [códigos de erro](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).