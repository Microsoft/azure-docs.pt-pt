---
title: Configure as regras de equilíbrio e saída de carga utilizando a Azure PowerShell
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar as regras de equilíbrio e saída de carga no Balanceador de Carga Padrão utilizando a Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: bd429ee5fc5564c55e6a9c758e1c53e821060f0e
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84809535"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Configure as regras de equilíbrio e saída de carga no Balanceador de Carga Padrão utilizando a Azure PowerShell

Este artigo mostra-lhe como configurar as regras de saída no Balancer de Carga Padrão utilizando a Azure PowerShell.  

Quando você termina o cenário deste artigo, o recurso do balanceador de carga contém duas extremidades dianteiras e as suas regras associadas. Tem uma frente para o tráfego de entrada e outra frente para o tráfego de saída.  

Cada frontal refere um endereço IP público. Neste cenário, o endereço IP público para o tráfego de entrada é diferente do endereço para tráfego de saída.   A regra de equilíbrio de carga proporciona apenas o equilíbrio da carga de entrada. A regra de saída controla a tradução de endereço de rede de saída (NAT) para o VM.  

O cenário usa duas piscinas traseiras: uma para o tráfego de entrada e outra para o tráfego de saída. Estas piscinas ilustram a capacidade e proporcionam flexibilidade para o cenário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure
Inscreva-se na sua subscrição Azure utilizando o comando [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Em seguida, siga as instruções no ecrã.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos utilizando [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Um grupo de recursos Azure é um recipiente lógico no qual os recursos da Azure são implantados. Os recursos são então geridos pelo grupo.

O exemplo a seguir cria um grupo de recursos chamado *myresourcegroupoutbound* na localização *eastus2:*

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Criar uma rede virtual chamada *myvnetoutbound*. Nomeie a sua sub-rede *mysubnetoutbound*. Coloque-o no *myresourcegroupoutbound* utilizando [a New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) e [a New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Criar um endereço IP público de entrada 

Para aceder à sua aplicação web na internet, precisa de um endereço IP público para o equilibrador de carga. O Balancer de Carga Padrão suporta apenas endereços IP públicos padrão. 

Utilize [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) para criar um endereço IP público padrão chamado *mypublicipinbound* in *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Criar um endereço IP público de saída 

Crie um endereço IP padrão para a configuração frontal do balanceador de carga utilizando [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Esta secção explica como criar e configurar os seguintes componentes do equilibrador de carga:
  - Um IP frontal que recebe o tráfego de rede de entrada no equilibrador de carga
  - Uma piscina traseira onde o IP frontal envia o tráfego de rede equilibrado de carga
  - Uma piscina traseira para conectividade de saída
  - Uma sonda de saúde que determina a saúde dos casos de VM back-end
  - Uma regra de entrada de balançadores de carga que define como o tráfego é distribuído para os VMs
  - Uma regra de saída de saldo de carga que define como o tráfego é distribuído a partir dos VMs

### <a name="create-an-inbound-front-end-ip"></a>Criar um IP frontal de entrada
Crie a configuração IP frontal de entrada para o equilibrador de carga utilizando [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). O equilibrador de carga deve incluir uma configuração IP frontal de entrada chamada *myfrontendinbound*. Associe esta configuração ao endereço IP público *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Criar um IP frontal de saída
Crie a configuração IP frontal de saída para o equilibrador de carga utilizando [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Este equilibrador de carga deve incluir uma configuração IP frontal de saída chamada *myfrontendoutbound*. Associe esta configuração ao endereço IP público *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Criar uma piscina traseira de entrada
Crie a piscina de entrada traseira para o balançador de carga utilizando [o New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nomeie a piscina *na entrada.*

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Criar uma piscina traseira de saída
Utilize o seguinte comando para criar outro conjunto de endereços back-end para definir a conectividade de saída para um conjunto de VMs utilizando [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nomeie esta piscina *bepooloutbound*. 

Ao criar uma piscina separada de saída, você fornece a máxima flexibilidade. Mas pode omitir este passo e usar apenas a entrada *de entrada,* se preferir.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias VM para se certificar de que podem enviar tráfego de rede. A instância VM que falha as verificações da sonda é removida do equilibrador de carga até que volte a funcionar e uma verificação da sonda determina que é saudável. 

Para monitorizar a saúde dos VMs, crie uma sonda de saúde utilizando [o New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Criar uma regra de equilíbrio de carga

Uma regra de equilíbrio de carga define a configuração IP frontal para o tráfego de entrada e a piscina traseira para receber o tráfego. Define também a fonte e o porto de destino necessários. 

Crie uma regra de equilíbrio de carga chamada *myinboundlbrule* utilizando [New-AzLoadBalanceruleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Esta regra ouvirá a porta 80 na piscina frontal *myfrontendinbound*. Também utilizará a porta 80 para enviar tráfego de rede equilibrado para a piscina de endereços de *fundo.* 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Esta regra de equilíbrio de carga desativa o NAT seguro de saída automática (SNAT) devido ao parâmetro **-DisableOutboundSNAT.** O NAT de saída é fornecido apenas pela regra de saída.

### <a name="create-an-outbound-rule"></a>Criar uma regra de saída

Uma regra de saída define o IP público frontal, que é representado pela linha frontal *da minha frente.* Esta extremidade frontal será utilizada para todo o tráfego NAT de saída, bem como para a piscina traseira a que a regra se aplica.  

Utilize o seguinte comando para criar uma regra de saída *myoutboundrule* para tradução de rede de saída de todos os VMs (em configurações NIC IP) na piscina traseira de *bepool.*  O comando muda o tempo de saída de 4 a 15 minutos. Atribui 10.000 portas SNAT em vez de 1.024. Para mais informações, consulte [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Se não quiser utilizar uma piscina separada, pode alterar o argumento do pool de endereços no comando anterior para especificar *$bepoolin.*  Recomendamos a utilização de piscinas separadas para tornar a configuração resultante flexível e legível.

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Utilize o seguinte comando para criar um equilibrador de carga para o endereço IP de entrada utilizando [o New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Nomeie o equilibrador *de carga lb*. Deve incluir uma configuração IP frontal de entrada. A sua piscina traseira *deve* ser associada ao endereço IP público *que* criou no passo anterior.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Neste ponto, pode continuar a adicionar os seus VMs tanto a piscinas de back-end *desajustada* e *desapaculação,* atualizando a configuração IP dos respetivos recursos NIC. Atualize a configuração do recurso utilizando [o Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não necessitar do grupo de recursos, do balanceador de carga e dos recursos conexas, pode removê-los utilizando [o Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um equilibrador de carga padrão, configurado tanto as regras de tráfego de carregamento de entrada como de saída, e configura uma sonda de saúde para os VMs na piscina traseira. 

Para saber mais, continue os [tutoriais para O Balancer de Carga Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
