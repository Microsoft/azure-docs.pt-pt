---
title: Tutorial - Carregamento equilibrar máquinas virtuais Windows em Azure
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para criar um balanceador de carga para uma aplicação segura de elevada disponibilidade em três máquinas virtuais do Windows
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d6c872ecfbad6cb8bb01ad5a7c3df8c47aadaebe
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549112"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>Tutorial: Fazer o balanceamento de carga de máquinas virtuais do Windows no Azure para criar uma aplicação de elevada disponibilidade com o Azure PowerShell
O balanceamento de carga fornece um nível mais elevado de disponibilidade ao propagar os pedidos recebidos por várias máquinas virtuais. Neste tutorial, vai conhecer os diferentes componentes do balanceador de carga do Azure que distribuem o tráfego e oferecem elevada disponibilidade. Saiba como:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma sonda de estado de funcionamento do balanceador de carga
> * Criar regras de tráfego do balanceador de carga
> * Utilizar a extensão de Script Personalizado para criar um site do IIS básico
> * Criar máquinas virtuais e anexar a um balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover VMs de um balanceador de carga

## <a name="azure-load-balancer-overview"></a>Descrição geral do balanceador de carga do Azure
Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP) que fornece elevada disponibilidade ao distribuir o tráfego de entrada entre VMs em bom estado de funcionamento. Uma sonda de estado de funcionamento do balanceador de carga monitoriza uma porta especificada em cada VM e apenas distribui o tráfego para uma VM operacional.

Pode definir uma configuração de IP de front-end com um ou mais endereços IP públicos. Esta configuração de IP de front-end permite ao balanceador de carga e às aplicações estarem acessíveis através da Internet. 

As máquinas virtuais ligam a um balanceador de carga através da respetiva placa de interface de rede virtual (NIC). Para distribuir o tráfego pelas VMs, um conjunto de endereços de back-end contém os endereços IP das placas virtuais (NICs) ligadas ao balanceador de carga.

Para controlar o fluxo de tráfego, pode definir regras de balanceador de carga para portas específicas e protocolos que mapeiam para as suas VMs.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-azure-load-balancer"></a>Criar um balanceador de carga do Azure
Esta secção descreve como pode criar e configurar cada componente do balanceador de carga. Antes de criar o seu balanceador de carga, crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupLoadBalancer* na localização *EastUS:*

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público
Para aceder à sua aplicação na Internet, precisa de um endereço IP público para o balanceador de carga. Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo seguinte cria um endereço IP público designado *myPublicIP* no grupo de recursos *myResourceGroupLoadBalancer*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Crie um pool IP frontend com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O exemplo seguinte cria um conjunto IP de front-end designado *myFrontEndPool* e anexa o endereço *myPublicIP*: 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Crie um pool de endereços backend com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). As VMs são anexadas a este conjunto de back-end nos restantes passos. O exemplo a seguir cria um pool de endereços de backend chamado *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

Agora, crie o equilibrador de carga com [o New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo seguinte cria um balanceador de carga designado *myLoadBalancer* com os conjuntos de IP de front-end e back-end criados nos passos anteriores:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento
Para permitir ao balanceador de carga monitorizar o estado da aplicação, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. Por predefinição, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Pode criar uma sonda de estado de funcionamento com base num protocolo ou numa página de verificação de estado de funcionamento específica da aplicação. 

O exemplo seguinte cria uma sonda TCP. Também pode criar sondas HTTP personalizadas para obter verificações de estado de funcionamento mais detalhadas. Quando utilizar uma sonda HTTP personalizada, tem de criar a página de verificação de estado de funcionamento, tal como *healthcheck.aspx*. A sonda tem de devolver uma resposta **HTTP 200 OK** para o balanceador de carga manter o anfitrião na rotação.

Para criar uma sonda de saúde TCP, utilize [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). O exemplo seguinte cria uma sonda de estado de funcionamento designado *myHealthProbe* que monitoriza cada VM no *TCP* porta *80*:

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Para aplicar a sonda de saúde, atualize o balançador de carga com [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Para garantir que apenas as VMs em bom estado de funcionamento recebem o tráfego, também pode definir a sonda de estado de funcionamento a utilizar.

Crie uma regra do balançador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). O exemplo seguinte cria uma regra de balanceador de carga designada *myLoadBalancerRule* e faz o balanceamento de carga do tráfego no *TCP* porta *80*:

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Atualizar o balançador de carga com [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Configurar uma rede virtual
Antes de implementar algumas VMs e testar o balanceador, crie os recursos de rede virtual. Para obter mais informações sobre redes virtuais, veja o tutorial [Gerir Redes Virtuais do Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Criar recursos de rede
Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual designada *myVnet* com *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Os NICs virtuais são criados com [o New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). O exemplo seguinte cria três NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar NICs virtuais e VMs adicionais em qualquer altura e adicioná-las ao balanceador de carga:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Criar máquinas virtuais
Para melhorar a elevada disponibilidade da aplicação, coloque as VMs num conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). O exemplo seguinte cria um conjunto de disponibilidade com o nome *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Defina um nome de utilizador e palavra-passe para as VMs com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora pode criar os VMs com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria três VMs e os componentes de rede virtual necessários, caso ainda não existam:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

O parâmetro `-AsJob` cria a VM como uma tarefa em segundo plano, para que os pedidos do PowerShell voltem para si. Pode ver os detalhes das tarefas em segundo plano com o cmdlet `Job`. Demora alguns minutos para criar e configurar as três VMs.


### <a name="install-iis-with-custom-script-extension"></a>Instalar o IIS com a Extensão de Script Personalizado
Num tutorial anterior sobre [Como personalizar uma máquina virtual do Windows](tutorial-automate-vm-deployment.md), aprendeu a automatizar a personalização de VMs com a Extensão de Script Personalizado para Windows. Pode utilizar a mesma abordagem para instalar e configurar o IIS nas suas VMs.

Utilize [a Configuração AzVMExtension](/powershell/module/az.compute/set-azvmextension) para instalar a extensão de script personalizada. A extensão executa o `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web IIS e, em seguida, atualiza a página *Default.htm* para mostrar o nome do anfitrião da VM:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Testar o balanceador de carga
Obtenha o endereço IP público do seu equilibrador de carga com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIP* criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Em seguida, pode introduzir o endereço IP público num browser. O site é apresentado, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Site do IIS em execução](./media/tutorial-load-balancer/running-iis-website.png)

Para ver o balanceador de carga a distribuir tráfego pelas três VMs que estão a executar a aplicação, pode forçar a atualização do browser.


## <a name="add-and-remove-vms"></a>Adicionar e remover VMs
Pode ter de realizar a manutenção nas VMs que estão a executar a aplicação, como instalar atualizações do SO. Para lidar com maior volume de tráfego para a aplicação, pode ter de adicionar mais VMs. Esta secção mostra como adicionar ou remover uma VM do balanceador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do balanceador de carga
Obtenha o cartão de interface de rede com [o Get-AzNetworkInterface,](/powershell/module/az.network/get-aznetworkinterface)em seguida, desembarque a propriedade *LoadBalancerBackendAddressPools* do NIC virtual para *$null*. Por fim, atualize a NIC virtual.:

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

Para ver o balanceador de carga a distribuir tráfego pelas duas VMs restantes que estão a executar a aplicação, pode forçar a atualização do browser. Pode agora efetuar a manutenção da VM, como instalar atualizações do SO ou reiniciar.

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM ao balanceador de carga
Depois de efetuar a manutenção VM, ou se precisar de expandir a capacidade, desloque a propriedade *LoadBalancerBackendAddressPools* do NIC virtual para o *BackendAddressPool* da [Get-AzLoadBalancer:](/powershell/module/az.network/get-azloadbalancer)

Obtenha o balanceador de carga:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um balanceador de carga e anexou VMs ao mesmo. Aprendeu a:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma sonda de estado de funcionamento do balanceador de carga
> * Criar regras de tráfego do balanceador de carga
> * Utilizar a extensão de Script Personalizado para criar um site do IIS básico
> * Criar máquinas virtuais e anexar a um balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover VMs de um balanceador de carga

Avance para o tutorial seguinte para aprender a gerir a rede de VMs.

> [!div class="nextstepaction"]
> [Gerir VMs e redes virtuais](./tutorial-virtual-network.md)
