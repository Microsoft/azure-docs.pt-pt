---
title: Configurar o balanceamento de carga e as regras de saída usando Azure PowerShell
titlesuffix: Azure Load Balancer
description: Este artigo mostra como configurar o balanceamento de carga e as regras de saída no Standard Load Balancer usando Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b621d56a76a51f9ce9df10f88f5c3f2f4babfc03
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816032"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Configurar o balanceamento de carga e as regras de saída no Standard Load Balancer usando Azure PowerShell

Este artigo mostra como configurar regras de saída no Standard Load Balancer usando Azure PowerShell.  

Quando você terminar o cenário deste artigo, o recurso de balanceador de carga conterá dois front-ends e suas regras associadas. Você tem um front-end para tráfego de entrada e outro front-end para tráfego de saída.  

Cada front-end faz referência a um endereço IP público. Nesse cenário, o endereço IP público para o tráfego de entrada é diferente do endereço para o tráfego de saída.   A regra de balanceamento de carga fornece apenas o balanceamento de carga de entrada. A regra de saída controla a NAT (conversão de endereços de rede) de saída para a VM.  

O cenário usa dois pools de back-end: um para o tráfego de entrada e outro para o tráfego de saída. Esses pools ilustram a funcionalidade e fornecem flexibilidade para o cenário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure
Entre em sua assinatura do Azure usando o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . Em seguida, siga as instruções na tela.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados. Os recursos são então gerenciados do grupo.

O exemplo seguinte cria um grupo de recursos chamado *myresourcegroupoutbound* no *eualeste2* localização:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual chamada *myvnetoutbound*. Nomeie sua sub-rede *mysubnetoutbound*. Coloque-o em *myresourcegroupoutbound* usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) e [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Criar um endereço IP público de entrada 

Para acessar seu aplicativo Web na Internet, você precisa de um endereço IP público para o balanceador de carga. Standard Load Balancer dá suporte apenas a endereços IP públicos padrão. 

Use [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) para criar um endereço IP público padrão chamado *mypublicipinbound* no *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Criar um endereço IP público de saída 

Crie um endereço IP padrão para a configuração de saída de front-end do balanceador de carga usando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Esta seção explica como criar e configurar os seguintes componentes do balanceador de carga:
  - Um IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga
  - Um pool de back-end em que o IP de front-end envia o tráfego de rede com balanceamento de carga
  - Um pool de back-ends para conectividade de saída
  - Uma investigação de integridade que determina a integridade das instâncias de VM de back-end
  - Uma regra de entrada do balanceador de carga que define como o tráfego é distribuído para as VMs
  - Uma regra de saída do balanceador de carga que define como o tráfego é distribuído das VMs

### <a name="create-an-inbound-front-end-ip"></a>Criar um IP de front-end de entrada
Crie a configuração de IP de front-end de entrada para o balanceador de carga usando [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). O balanceador de carga deve incluir uma configuração de IP de front-end de entrada chamada *myfrontendinbound*. Associe essa configuração ao endereço IP público *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Criar um IP de front-end de saída
Crie a configuração de IP de front-end de saída para o balanceador de carga usando [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Esse balanceador de carga deve incluir uma configuração de IP de front-end de saída chamada *myfrontendoutbound*. Associe essa configuração ao endereço IP público *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Criar um pool de back-ends de entrada
Crie o pool de entrada de back-end para o balanceador de carga usando [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nomeie o pool *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Criar um pool de back-end de saída
Use o comando a seguir para criar outro pool de endereços de back-end para definir a conectividade de saída para um pool de VMs usando [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nomeie este pool *bepooloutbound*. 

Ao criar um pool de saída separado, você fornece a flexibilidade máxima. Mas você pode omitir essa etapa e usar apenas o *bepoolinbound* de entrada se preferir.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Uma investigação de integridade verifica todas as instâncias de VM para garantir que elas possam enviar tráfego de rede. A instância de VM que falha as verificações de investigação é removida do balanceador de carga até que ela volte a ficar online e uma verificação de investigação determine que ela está íntegra. 

Para monitorar a integridade das VMs, crie uma investigação de integridade usando [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o pool de back-end para receber o tráfego. Ele também define a origem necessária e a porta de destino. 

Crie uma regra de balanceador de carga chamada *myinboundlbrule* usando [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Essa regra escutará a porta 80 no pool de front-end *myfrontendinbound*. Ele também usará a porta 80 para enviar o tráfego de rede com balanceamento de carga para o pool de endereços de back-end *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Essa regra de balanceamento de carga desabilita o NAT seguro de saída automático (SNAT) por causa do parâmetro **-DisableOutboundSNAT** . O NAT de saída é fornecido somente pela regra de saída.

### <a name="create-an-outbound-rule"></a>Criar uma regra de saída

Uma regra de saída define o IP público de front-end, que é representado pelo *myfrontendoutbound*de front-end. Esse front-end será usado para todo o tráfego de saída de NAT, bem como para o pool de back-ends ao qual a regra se aplica.  

Use o comando a seguir para criar uma regra de saída *myoutboundrule* para a tradução de rede de saída de todas as VMs (em configurações de IP de NIC) no pool de back-end do *pool de objetos* .  O comando altera o tempo limite de saída ocioso de 4 para 15 minutos. Ela aloca 10.000 portas SNAT em vez de 1.024. Para obter mais informações, consulte [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Se você não quiser usar um pool de saída separado, poderá alterar o argumento do pool de endereços no comando anterior para especificar *$bepoolin* em vez disso.  É recomendável usar pools separados para tornar a configuração resultante flexível e legível.

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Use o comando a seguir para criar um balanceador de carga para o endereço IP de entrada usando [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Nomeie o balanceador de carga *lb*. Ele deve incluir uma configuração de IP de front-end de entrada. Seu pool de back-end *bepoolinbound* deve ser associado ao endereço IP público *mypublicipinbound* que você criou na etapa anterior.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Neste ponto, você pode continuar adicionando suas VMs a pools de back-ends *bepoolinbound* e *bepooloutbound* atualizando a configuração de IP dos respectivos recursos de NIC. Atualize a configuração de recurso usando [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais do grupo de recursos, do balanceador de carga e dos recursos relacionados, poderá removê-los usando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você criou um balanceador de carga padrão, configurou as regras de tráfego de balanceador de carga de entrada e saída e configurou uma investigação de integridade para as VMs no pool de back-ends. 

Para saber mais, continue nos [tutoriais para Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
