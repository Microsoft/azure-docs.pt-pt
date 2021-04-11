---
title: Use powerShell para implantar máquinas virtuais Azure Spot
description: Aprenda a usar o Azure PowerShell para implementar máquinas virtuais Azure Spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802748"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Implementar máquinas virtuais Azure Spot usando Azure PowerShell


A utilização de [Máquinas Virtuais Azure Spot](../spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma economia de custos significativa. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure irá despejar máquinas virtuais Azure Spot. Portanto, as máquinas virtuais Azure Spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços das máquinas virtuais Azure Spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços em VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre a fixação do preço máximo, consulte [máquinas virtuais Azure Spot - Preços](../spot-vms.md#pricing).

Tem a opção de definir um preço máximo que está disposto a pagar, por hora, pelo VM. O preço máximo de uma Máquina Virtual Azure Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765` seria um preço máximo de $0.98765 USD por hora. Se definir o preço `-1` máximo, o VM não será despejado com base no preço. O preço do VM será o preço atual para o spot ou o preço para um VM padrão, que sempre é menor, desde que haja capacidade e quota disponível.


## <a name="create-the-vm"></a>Criar a VM

Crie um spotVM utilizando [o New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) para criar a configuração. Incluir `-Priority Spot` e definir para qualquer um dos `-MaxPrice` dois:
- `-1` para que o VM não seja despejado com base no preço.
- uma quantia em dólares, até 5 dígitos. Por exemplo, `-MaxPrice .98765` significa que o VM será transabilitado uma vez que o preço de um spotVM seja de cerca de $9.98765 por hora.


Este exemplo cria um spotVM que não será transabilitado com base nos preços (apenas quando o Azure precisar da capacidade de volta). A política de despejo está definida para negociar o VM, para que possa ser reiniciada mais tarde. Se pretender eliminar o VM e o disco subjacente quando o VM for despejado, definido `-EvictionPolicy` `Delete` para `New-AzVMConfig` dentro


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

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Após a criação do VM, pode consultar para ver o preço máximo de todos os VMs no grupo de recursos.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Simular um despejo

Pode simular um despejo de uma Máquina Virtual Azure Spot usando REST, PowerShell ou o CLI, para testar quão bem a sua aplicação irá responder a um despejo súbito.

Na maioria dos casos, irá querer utilizar as Máquinas Virtuais REST API [- Simular o Despejo](/rest/api/compute/virtualmachines/simulateeviction) para ajudar no teste automatizado de aplicações. Para rest, um `Response Code: 204` meio que o despejo simulado foi bem sucedido. Pode combinar despejos simulados com o [serviço Evento Agendado,](scheduled-events.md)para automatizar como a sua aplicação irá responder quando o VM for despejado.

Para ver eventos agendados em ação, assista [Azure Friday - Usando Azure Eventos Agendados para preparar a manutenção de VM](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Teste rápido

Para um teste rápido para mostrar como um despejo simulado vai funcionar, vamos passar por consultas ao serviço de eventos programado para ver como é quando simula um despejo usando o PowerShell.

O serviço De Evento Agendado está habilitado para o seu serviço na primeira vez que faz um pedido de eventos. 

Remoto para o seu VM e, em seguida, abra um pedido de comando. 

A partir do pedido de comando no seu VM, escreva:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Esta primeira resposta pode demorar até 2 minutos. A partir de agora, devem exibir a saída quase imediatamente.

A partir de um computador que tem o módulo Az PowerShell instalado (como a sua máquina local), simular um despejo usando [Set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). Substitua o nome do grupo de recursos e o nome VM pelo seu próprio. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

A saída de resposta terá `Status: Succeeded` se o pedido tiver sido feito com sucesso.

Volte rapidamente à sua ligação remota à sua Máquina Virtual Spot e volte a consultar o ponto de partida de Eventos Agendados. Repita o seguinte comando até obter uma saída que contenha mais informações:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Quando o Serviço de Eventos Agendados receber a notificação de despejo, receberá uma resposta semelhante a esta:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Pode ver `"EventType":"Preempt"` isso, e o recurso é o recurso VM `"Resources":["myspotvm"]` . 

Também pode ver quando o VM será despejado verificando o `"NotBefore"` valor. O VM não será despejado antes do momento dado, por isso esta é a `NotBefore` sua janela para a sua aplicação fechar graciosamente.


## <a name="next-steps"></a>Passos seguintes

Também pode criar uma Máquina Virtual Azure Spot utilizando o [Azure CLI,](../linux/spot-cli.md) [portal](../spot-portal.md) ou [um modelo](../linux/spot-template.md).

Consultar informações sobre preços correntes utilizando os preços de [retalho Azure API](/rest/api/cost-management/retail-prices/azure-retail-prices) para obter informações sobre os preços da Máquina Virtual Azure Spot. O `meterName` e vai conter `skuName` `Spot` ambos.

Se encontrar um erro, consulte [códigos de erro](../error-codes-spot.md).
