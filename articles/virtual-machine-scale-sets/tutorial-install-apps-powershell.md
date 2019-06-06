---
title: Tutorial - Instalar aplicações num conjunto de dimensionamento com o Azure PowerShell | Microsoft Docs
description: Saiba como utilizar o Azure PowerShell para instalar aplicações em conjuntos de dimensionamento de máquinas virtuais com a Extensão de Script Personalizado
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4c6216f3dff3cbf4c8d838810c4dd786f1c34ec1
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728660"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: Instalar aplicações em conjuntos de dimensionamento de máquina virtual com o Azure PowerShell

Para executar aplicações em instâncias de máquina virtual (VM) num conjunto de dimensionamento, primeiro tem de instalar os componentes da aplicação e os ficheiros necessários. Num tutorial anterior, aprendeu a criar e utilizar uma imagem de VM personalizada para implementar as suas instâncias de VM. Esta imagem personalizada inclui configurações e instalações de aplicações manuais. Pode também automatizar a instalação de aplicações num conjunto de dimensionamento após cada instância de VM ser implementada ou atualizar uma aplicação que já é executada num conjunto de dimensionamento. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar automaticamente aplicações no seu conjunto de dimensionamento
> * Utilizar a Extensão de Script Personalizado do Azure
> * Atualizar uma aplicação em execução num conjunto de dimensionamento

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>O que é a Extensão de Script Personalizado do Azure?
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão.

A extensão de Script personalizado integra-se com modelos Azure Resource Manager. Também pode ser utilizado com a CLI do Azure, Azure PowerShell, portal do Azure ou a API REST. Para obter mais informações, veja a [Descrição geral da Extensão de Script Personalizado](../virtual-machines/windows/extensions-customscript.md).

Para ver a Extensão de Script Personalizado em ação, crie um conjunto de dimensionamento que instala o servidor Web IIS e dá como resultado o nome de anfitrião da instância de VM do conjunto de dimensionamento. A definição da Extensão de Script Personalizado transfere um script de exemplo do GitHub, instala os pacotes necessários e escreve o nome de anfitrião da instância de VM numa página HTML simples.


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Agora, crie um conjunto com de dimensionamento de máquina virtual [New-AzVmss](/powershell/module/az.compute/new-azvmss). Para distribuir o tráfego para instâncias de VM individuais, é também criado um balanceador de carga. O Balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80. Ela também permite o tráfego de ambiente de trabalho remoto na porta TCP 3389 e a comunicação remota do PowerShell na porta TCP 5985. Quando lhe for pedido, pode definir suas próprias credenciais administrativas para as instâncias VM no conjunto de dimensionamento:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.


## <a name="create-custom-script-extension-definition"></a>Criar a definição da Extensão de Script Personalizado
O Azure PowerShell utiliza uma tabela hash para armazenar o ficheiro a transferir e o comando a executar. No exemplo seguinte, é utilizado um script de exemplo do GitHub. Em primeiro lugar, crie este objeto de configuração da seguinte forma:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```


Agora, aplique a extensão de Script personalizado com [Add-AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension). O objeto de configuração definido anteriormente é transmitido para a extensão. Atualizar e executar a extensão nas instâncias de VM com [AzVmss atualização](/powershell/module/az.compute/update-azvmss).


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Cada instância de VM no conjunto de dimensionamento transfere e executa o script do GitHub. Num exemplo mais complexo, podem ser instalados vários ficheiros e componentes de aplicações. Se o conjunto de dimensionamento for aumentado verticalmente, as novas instâncias de VM aplicam automaticamente a mesma definição da Extensão de Script Personalizado e instalam a aplicação necessária.


## <a name="allow-traffic-to-application"></a>Permitir o tráfego para a aplicação

Para permitir o acesso à aplicação web básica, crie um grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) e [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Para obter mais informações, consulte [redes para conjuntos de dimensionamento de máquina virtual do Azure](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
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
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```



## <a name="test-your-scale-set"></a>Testar o seu conjunto de dimensionamento
Para ver o seu servidor web em ação, obtenha o endereço IP público do seu Balanceador de carga com [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte apresenta o endereço IP criado no *myResourceGroup* grupo de recursos:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Introduza o endereço IP público do balanceador de carga num browser. O balanceador de carga distribui o tráfego para uma das suas instâncias de VM, conforme mostra o exemplo seguinte:

![Página Web de base no IIS](media/tutorial-install-apps-powershell/running-iis.png)

Deixe o browser aberto para que possa ver uma versão atualizada no próximo passo.


## <a name="update-app-deployment"></a>Atualizar a implementação da aplicação
Ao longo do ciclo de vida de um conjunto de dimensionamento, poderá precisar de implementar uma versão atualizada da sua aplicação. Com a Extensão de Script Personalizado, pode referenciar um script de implementação atualizado e, em seguida, voltar a aplicar a extensão ao seu conjunto de dimensionamento. Quando o conjunto de dimensionamento foi criado num passo anterior, o `-UpgradePolicyMode` foi definido como *Automático*. Esta definição permite que as instâncias de VM no conjunto de dimensionamento atualizem e apliquem automaticamente a versão mais recente da sua aplicação.

Crie uma nova definição de configuração com o nome *customConfigv2*. Esta definição executa uma versão *v2* atualizada do script de instalação da aplicação:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Atualize a configuração de extensão de Script personalizado às instâncias de VM no conjunto de dimensionamento. A definição *customConfigv2* é utilizada para aplicar a versão atualizada da aplicação:

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Todas as instâncias de VM no conjunto de dimensionamento são automaticamente atualizadas com a versão mais recente da página Web de exemplo. Para ver a versão atualizada, atualize o site no seu browser:

![Página Web atualizada no IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o seu conjunto de dimensionamento e recursos adicionais, elimine o grupo de recursos e todos os respetivos recursos com [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). O parâmetro `-Force` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo. O parâmetro `-AsJob` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a instalar e atualizar automaticamente aplicações no seu conjunto de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Instalar automaticamente aplicações no seu conjunto de dimensionamento
> * Utilizar a Extensão de Script Personalizado do Azure
> * Atualizar uma aplicação em execução num conjunto de dimensionamento

Prossiga para o próximo tutorial para saber como dimensionar automaticamente o seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Dimensionar automaticamente os seus conjuntos de dimensionamento](tutorial-autoscale-powershell.md)
