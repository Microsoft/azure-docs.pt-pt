---
title: Tutorial - Criar um conjunto de dimensionamento de máquinas virtuais para Windows no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para criar e implementar uma aplicação de elevada disponibilidade em VMs do Windows com um conjunto de dimensionamento de máquinas virtuais
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f4641125d15f159c6f50d2889e13b06ba954401b
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886826"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Tutorial: Criar um conjunto de dimensionamento de máquinas virtuais e implementar uma aplicação de elevada disponibilidade no Windows com o Azure PowerShell
Um conjunto de dimensionamento de máquina virtual permite-lhe implementar e gerir um conjunto de máquinas de virtuais idênticas, do dimensionamento automático. Pode dimensionar o número de VMs no conjunto manualmente de dimensionamento. Também pode definir regras para dimensionar automaticamente com base na utilização de recursos, tais como CPU, exigência de memória ou tráfego de rede. Neste tutorial, vai implementar um conjunto de dimensionamento de máquinas virtuais no Azure e saiba como:

> [!div class="checklist"]
> * Utilizar a Extensão de Script Personalizado para definir um site do IIS para dimensionamento
> * Criar um balanceador de carga para o conjunto de dimensionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Aumentar ou reduzir o número de instâncias num conjunto de dimensionamento
> * Criar regras de dimensionamento automático

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial precisará da versão 6.0.0 ou posterior do módulo do Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps). Se estiver executando o PowerShell localmente, terá também de executar `Connect-AzureRmAccount` para criar uma ligação com o Azure.


## <a name="scale-set-overview"></a>Descrição geral de Conjunto de Dimensionamento
Um conjunto de dimensionamento de máquina virtual permite-lhe implementar e gerir um conjunto de máquinas de virtuais idênticas, do dimensionamento automático. As VMs num conjunto de dimensionamento estão distribuídas por domínios de atualização e de falha lógicos num ou mais *grupos de posicionamento*. Grupos de colocação são grupos de VMs configuradas de forma semelhante, semelhantes à [conjuntos de disponibilidade](tutorial-availability-sets.md).

As VMs são criadas conforme necessário num conjunto de dimensionamento. Pode definir regras de dimensionamento automático para controlar como e quando as VMs são adicionadas ou removidas do conjunto de dimensionamento. Estas regras podem ser acionadas com base em métricas, como a carga da CPU, a utilização da memória ou o tráfego de rede.

Os conjuntos de dimensionamento suportam até 1000 VMs quando utiliza uma imagem da plataforma Azure. Para cargas de trabalho com requisitos de instalação ou de personalização de VM significativos, poderá querer [Criar uma imagem de VM personalizada](tutorial-custom-images.md). Pode criar até 300 VMs num conjunto de dimensionamento quando utiliza uma imagem personalizada.


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Crie um conjunto de dimensionamento de máquinas virtuais com [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). O exemplo seguinte cria um conjunto de dimensionamento com o nome *myScaleSet* que utiliza a imagem de plataforma do *Windows Server 2016 Datacenter*. Os recursos de rede do Azure para a rede virtual, o endereço IP público e o balanceador de carga são criados automaticamente. Quando lhe for pedido, pode definir suas próprias credenciais administrativas para as instâncias VM no conjunto de dimensionamento:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.


## <a name="deploy-sample-application"></a>Implementar aplicação de exemplo
Para testar o conjunto de dimensionamento, instale uma aplicação Web básica. A Extensão de Script Personalizado do Azure serve para transferir e executar um script que instala o IIS nas instâncias de VM. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Para obter mais informações, veja a [Descrição geral da Extensão de Script Personalizado](extensions-customscript.md).

Utilize a Extensão de Script Personalizado para instalar um servidor Web do IIS básico. Aplique a Extensão de Script Personalizado que instala o IIS da seguinte forma:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Permitir o tráfego para a aplicação

Para permitir o acesso à aplicação web básica, crie um grupo de segurança de rede com [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) e [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Para obter mais informações, consulte [redes para conjuntos de dimensionamento de máquina virtual do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>Testar o seu conjunto de dimensionamento
Para ver o seu conjunto de dimensionamento em ação, obtenha o endereço IP público do seu Balanceador de carga com [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). O exemplo seguinte apresenta o endereço IP para *myPublicIP* criado como parte do conjunto de dimensionamento:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Introduza o endereço IP público num browser. A aplicação Web é apresentada, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu o tráfego:

![Site do IIS em execução](./media/tutorial-create-vmss/running-iis-site.png)

Para ver o conjunto de dimensionamento em ação, pode forçar a atualização do browser para ver o balanceador de carga distribuir o tráfego por todas as VMs que têm a sua aplicação em execução.


## <a name="management-tasks"></a>Tarefas de gestão
Ao longo do ciclo de vida do conjunto de dimensionamento, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. O Azure PowerShell fornece uma maneira rápida de realizar essas tarefas. Seguem-se algumas tarefas comuns.

### <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para ver uma lista das instâncias da VM num conjunto de dimensionamento, utilize [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) da seguinte forma:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

O seguinte resultado de exemplo mostra duas instâncias da VM no conjunto de dimensionamento:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Para ver informações adicionais sobre uma instância específica da VM, adicione o parâmetro `-InstanceId` a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). O seguinte exemplo mostra informações sobre a instância da VM *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Aumentar ou reduzir as instâncias de VM
Para ver o número de instâncias atualmente existentes num conjunto de dimensionamento, utilize [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) e consulte *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Em seguida, pode aumentar ou diminuir manualmente o número de máquinas virtuais existentes no conjunto de dimensionamento com [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). O exemplo seguinte define o número de VMs no seu conjunto de dimensionamento como *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

A atualização do número especificado de instâncias no seu conjunto de dimensionamento demora alguns minutos.


### <a name="configure-autoscale-rules"></a>Configurar regras de dimensionamento automático
Em vez de dimensionar manualmente o número de instâncias no seu conjunto de dimensionamento, defina regras de dimensionamento automático. Estas regras monitorizaram as instâncias no seu conjunto de dimensionamento e respondem em conformidade com base nas métricas e nos limiares definidos por si. O exemplo seguinte aumenta horizontalmente o número de instâncias em uma instância quando a carga média da CPU é superior a 60% ao longo de um período de 5 minutos. Se, em seguida, a carga média da CPU descer abaixo dos 30% ao longo de um período de 5 minutos, as instâncias são reduzidas horizontalmente em uma instância:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

Para obter mais informações de estrutura sobre a utilização do dimensionamento automático, consulte [melhores práticas de dimensionamento automático](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, criou um conjunto de dimensionamento de máquinas virtuais. Aprendeu a:

> [!div class="checklist"]
> * Utilizar a Extensão de Script Personalizado para definir um site do IIS para dimensionamento
> * Criar um balanceador de carga para o conjunto de dimensionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Aumentar ou reduzir o número de instâncias num conjunto de dimensionamento
> * Criar regras de dimensionamento automático

Avance para o próximo tutorial para saber mais sobre conceitos de balanceamento de carga para máquinas virtuais.

> [!div class="nextstepaction"]
> [Fazer o balanceamento de carga de máquinas virtuais](tutorial-load-balancer.md)
