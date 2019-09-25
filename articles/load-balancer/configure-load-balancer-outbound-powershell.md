---
title: Configurar o balanceamento de carga e as regras de saída usando Azure PowerShell
titlesuffix: Azure Load Balancer
description: Este artigo mostra como configurar o balanceamento de carga e as regras de saída em um Standard Load Balancer usando o Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262625"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Configurar o balanceamento de carga e as regras de saída no balanceador de carga padrão usando Azure PowerShell

Este artigo mostra como configurar regras de saída no balanceador de carga padrão usando Azure PowerShell.  

Quando terminar, o recurso de balanceador de carga conterá dois front-ends e regras associados a eles: um para entrada e outro para saída.  Cada front-end tem uma referência para um endereço IP público e esta utiliza de cenário um endereço IP público diferente para entrada e saída.   A regra de balanceamento de carga fornece balanceamento de carga entrada única e a regra de saída controla o NAT de saída fornecido para a VM.  Este artigo usa dois pools de back-end separados, um para entrada e outro para saída, para ilustrar a capacidade e permitir a flexibilidade para esse cenário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Conectar-se à conta do Azure
Entre em sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos chamado *myresourcegroupoutbound* no *eualeste2* localização:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Criar a rede virtual
Crie uma rede virtual chamada *myvnetoutbound* com uma sub-rede denominada *mysubnetoutbound* no *myresourcegroupoutbound* usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) e [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0):

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Criar endereço IP público de entrada 

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. Um balanceador de carga padrão só dá suporte A endereços IP públicos padrão. Use [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) para criar um endereço IP público padrão chamado *mypublicipinbound* no *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Criar endereço IP público de saída 

Crie um endereço IP padrão para a configuração de saída front-end do balanceador de carga usando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:
  - Um IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  - Um pool de back-end onde o IP de front-end envia o tráfego de rede com balanceamento de carga.
  - Um pool de back-end para conectividade de saída. 
  - uma sonda de estado de funcionamento que determina o estado de funcionamento das instâncias de VM de back-end.
  - Uma entrada Balanceador de carga que define como o tráfego é distribuído pelas VMS.
  - Uma saída Balanceador de carga que define como o tráfego é distribuído das VMs.

### <a name="create-inbound-frontend-ip"></a>Criar IP de front-end de entrada
Crie a configuração de IP de front-end de saída para o balanceador de carga com [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) que inclui uma configuração de IP de front-end de entrada denominada *myfrontendinbound* que está associada ao endereço *IP público mypublicipinbound*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Criar endereço IP de front-end saída
Crie a configuração de IP de front-end de saída para o balanceador de carga com [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) que inclui uma configuração de IP de front-end de saída denominada *myfrontendoutbound* que está associada ao endereço *IP público mypublicipoutbound*:

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Criar pool de back-end de entrada
Crie o pool de entrada de back-end para o balanceador de carga com [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) chamado *bepoolinbound*:

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Criar pool de back-end de saída
Crie um pool de endereços de back-end adicional para definir a conectividade de saída para um pool de VMs com [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) chamado *bepooloutbound*. A criação de um pool de saída separado fornece a máxima flexibilidade, mas você pode omitir essa etapa e usar apenas o *bepoolinbound* de entrada também.  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem enviar o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Crie uma investigação de integridade com [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) para monitorar a integridade das máquinas virtuais. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Criar regra de balanceamento de carga

Uma regra de Balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o conjunto de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de balanceador de carga *myinboundlbrule* com [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) para escutar a porta 80 no pool de front-end *myfrontendinbound* e enviar o tráfego de rede com balanceamento de carga para o pool *de endereços de back-end bepoolinbound* também está usando a porta 80. 

>[!NOTE]
>Essa regra de balanceamento de carga desabilita a NAT de saída (S) automática como resultado dessa regra com o parâmetro **-DisableOutboundSNAT** . NAT de saída só é fornecido pela regra de saída.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Criar regra de saída

Uma regra de saída define o IP público de front-end, representado pelo front-end *myfrontendoutbound*, que será utilizado para todo o tráfego de saída da NAT, bem como o conjunto de back-end para o qual esta regra se aplique.  Criar uma regra de saída *myoutboundrule* tradução de rede de saída de todas as máquinas virtuais (configurações de IP de NIC) na *bepool* conjunto back-end.  O comando abaixo também altera o tempo de limite de inatividade saído de 4 para 15 minutos e aloca 10000 SNAT portas em vez de 1024.  Examine [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) para obter mais detalhes.

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Se você não quiser usar um pool de saída separado, poderá alterar o argumento do pool de endereços no comando anterior para especificar *$bepoolin* em vez disso.  É recomendável usar pools separados para flexibilidade e legibilidade da configuração resultante.

### <a name="create-load-balancer"></a>Criar balanceador de carga

Crie um balanceador de carga com o endereço IP de entrada usando [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) chamado *lb* que inclui uma configuração de IP de front-end de entrada e um *bepoolinbound* do pool de back-ends que está associado ao endereço *IP público mypublicipinbound* que você criou na etapa anterior.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Neste ponto, você pode continuar adicionando suas VMs ao pool de back-end *bepoolinbound* __e__ *bepooloutbound* atualizando a configuração de IP dos respectivos recursos de NIC usando [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você criou o balanceador de carga padrão, configurou as regras de tráfego do balanceador de carga de entrada, configuradas e a investigação de integridade para as VMs no pool de back-end. Para saber mais sobre Azure Load Balancer, continue para os tutoriais do Azure Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
