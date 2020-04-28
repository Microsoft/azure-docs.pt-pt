---
title: Configure regras de equilíbrio de carga e saída utilizando o Azure PowerShell
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar regras de equilíbrio de carga e saída no Balancer Standard Load, utilizando o Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74225432"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Configure regras de equilíbrio de carga e saída no Balancer de Carga Padrão utilizando o Azure PowerShell

Este artigo mostra-lhe como configurar regras de saída no Standard Load Balancer utilizando o Azure PowerShell.  

Quando terminar o cenário deste artigo, o recurso de equilíbrio de carga contém duas extremidades dianteiras e as suas regras associadas. Tens uma frente para o trânsito de entrada e outra frente para o trânsito de saída.  

Cada extremidade frontal faz referência a um endereço IP público. Neste cenário, o endereço IP público para o tráfego de entrada é diferente do endereço para o tráfego de saída.   A regra de equilíbrio de carga proporciona apenas o equilíbrio da carga de entrada. A regra de saída controla a tradução de endereços de rede de saída (NAT) para o VM.  

O cenário utiliza duas piscinas de back-end: uma para tráfego de entrada e outra para tráfego de saída. Estas piscinas ilustram a capacidade e proporcionam flexibilidade para o cenário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure
Inscreva-se na subscrição do Azure utilizando o comando [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Em seguida, siga as instruções no ecrã.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos utilizando o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Um grupo de recursos Azure é um recipiente lógico no qual os recursos azure são implantados. Os recursos são então geridos pelo grupo.

O exemplo seguinte cria um grupo de recursos chamado *myresourcegroupoutbound* no local *eastus2:*

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Criar uma rede virtual chamada *myvnetoutbound*. Nomeie a sua subnet *mysubnetoutbound*. Coloque-o no *myresourcegroupoutbound* utilizando [new-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) e [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Criar um endereço IP público de entrada 

Para aceder à sua aplicação web na internet, precisa de um endereço IP público para o equilibrador de carga. O Standard Load Balancer suporta apenas endereços IP públicos padrão. 

Utilize [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) para criar um endereço IP público padrão chamado *mypublicipinbound* in *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Criar um endereço IP público de saída 

Crie um endereço IP padrão para a configuração de saída frontal do equilibrador de carga utilizando [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Esta secção explica como criar e configurar os seguintes componentes do equilibrador de carga:
  - Um IP frontal que recebe o tráfego de rede de entrada no equilibrador de carga
  - Uma piscina traseira onde o IP frontal envia o tráfego de rede equilibrado
  - Um pool de back-end para conectividade de saída
  - Uma sonda de saúde que determina a saúde dos casos de VM back-end
  - Uma regra de entrada de equilibrista de carga que define como o tráfego é distribuído aos VMs
  - Uma regra de saída do equilibrador de carga que define como o tráfego é distribuído a partir dos VMs

### <a name="create-an-inbound-front-end-ip"></a>Criar um IP frontal de entrada
Crie a configuração IP frontal de entrada para o equilíbrio de carga utilizando [o New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). O equilibrador de carga deve incluir uma configuração IP frontal de entrada chamada *myfrontendinbound*. Associe esta configuração com o endereço IP público *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Criar um IP frontal de saída
Crie a configuração IP frontal de saída para o equilíbrio de carga utilizando [o New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Este equilíbrio de carga deve incluir uma configuração IP frontal de saída chamada *myfrontendoutbound*. Associe esta configuração com o endereço IP público *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Crie uma piscina de entrada na parte de trás
Crie o pool de entrada de volta para o equilíbrio de carga utilizando [o New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Diga o nome da piscina *encadernada.*

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Crie uma piscina de back-end de saída
Utilize o seguinte comando para criar outro conjunto de endereços traseiros para definir a conectividade de saída para um conjunto de VMs utilizando [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nomeie esta piscina *bepooloutbound*. 

Ao criar uma piscina separada de saída, você fornece a máxima flexibilidade. Mas pode omitir este passo e usar apenas a entrada *de entrada,* se preferir.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias vm para garantir que podem enviar tráfego de rede. A instância VM que falha nas verificações da sonda é removida do equilibrador de carga até que volte a funcionar e uma verificação de sonda determina que é saudável. 

Para monitorizar a saúde dos VMs, crie uma sonda de saúde utilizando [new-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Criar uma regra de equilíbrio de carga

Uma regra do equilibrador de carga define a configuração IP frontal para o tráfego de entrada e a piscina traseira para receber o tráfego. Também define a fonte e a porta de destino necessárias. 

Crie uma regra de equilíbrio de carga chamada *myinboundlbrule* utilizando [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Esta regra vai ouvir o porto 80 na piscina frontal *myfrontendinbound*. Utilizará igualmente a porta 80 para enviar tráfego de rede equilibrado para a *piscina*de endereços de fundo. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Esta regra de equilíbrio de carga desativa o NAT (SNAT) de saída automático devido ao parâmetro **-DisableOutboundSNAT.** O NAT de saída é fornecido apenas pela regra de saída.

### <a name="create-an-outbound-rule"></a>Criar uma regra de saída

Uma regra de saída define o IP público frontal, que é representado pela frente *myfrontendoutbound*. Esta extremidade frontal será utilizada para todo o tráfego nat de saída, bem como para a piscina traseira a que a regra se aplica.  

Utilize o seguinte comando para criar uma *regra* de saída para a tradução de rede de saída de todos os VMs (em configurações IP NIC) na piscina traseira *bepool.*  O comando altera o tempo de saída de 4 para 15 minutos. Aloca 10.000 portas SNAT em vez de 1.024. Para mais informações, consulte [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Se não quiser utilizar uma piscina separada de saída, pode alterar o argumento da piscina de endereços no comando anterior para especificar *$bepoolin* em vez disso.  Recomendamos a utilização de piscinas separadas para tornar a configuração resultante flexível e legível.

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Utilize o seguinte comando para criar um equilíbrio de carga para o endereço IP de entrada utilizando [o New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Nomeie o equilibrador de carga *lb*. Deve incluir uma configuração IP frontal de entrada. A sua piscina de back-end *bepoolinbound* deve ser associada ao endereço IP público *mypublicipinbound* que criou no passo anterior.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Neste ponto, pode continuar a adicionar os seus VMs a piscinas traseiras *bepoolinbound* e *bepooloutbound,* atualizando a configuração IP dos respetivos recursos NIC. Atualize a configuração do recurso utilizando [add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa do grupo de recursos, do equilibrador de carga e dos recursos conexos, pode removê-los utilizando o [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um equilibrador de carga padrão, configurou as regras de tráfego de equilíbrio de carga de entrada e saída, e configurou uma sonda de saúde para os VMs na piscina traseira. 

Para saber mais, continue aos [tutoriais do Azure Load Balancer.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
