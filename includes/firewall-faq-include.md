---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b8842ab4bcaf16b7345b25fa9ac4998981d9c458
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713595"
---
### <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É um totalmente com monitoração de estado firewall-como-serviço com elevada disponibilidade incorporada e a escalabilidade da cloud sem restrições. Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Que capacidades são suportadas no Firewall do Azure?

* Firewall com monitorização de estado como serviço
* Elevada disponibilidade incorporada com escalabilidade da cloud sem restrições
* Filtragem FQDN
* Etiquetas FQDN
* Regras de filtragem de tráfego de rede
* Suporte SNAT de saída
* Suporte DNAT de entrada
* Criar centralmente, impor e políticas de conectividade de rede e aplicativos de registo em VNETs e subscrições do Azure
* Integração total com o Azure Monitor para fins de registo e análise

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>O que é o modelo de implementação típica para o Firewall do Azure?

Pode implantar o Firewall do Azure em qualquer rede virtual, mas os clientes normalmente implementação-la numa rede virtual central e configurar o peering entre outras redes virtuais ao mesmo num modelo de hub-and-spoke. Em seguida, pode definir a rota padrão das redes virtuais em modo de peering para apontar para esta rede virtual de central firewall. Global VNet peering é suportado, mas não é recomendado devido a problemas de latência e de desempenho potencial em várias regiões. Para obter melhor desempenho, implemente uma firewall por região.

A vantagem desse modelo é a capacidade de forma centralizada exercer controlo no spoke várias VNETs em subscrições diferentes. Também existem poupanças de custos que não precisa de implementar uma firewall em cada VNet separadamente. As poupanças de custos devem ser medidas versus o custo de peering associar com base nos padrões de tráfego de cliente.

### <a name="how-can-i-install-the-azure-firewall"></a>Como posso instalar o Firewall do Azure?

Pode configurar Firewall do Azure com o portal do Azure, PowerShell, REST API, ou com os modelos. Consulte [Tutorial: Implementar e configurar a Firewall do Azure no portal do Azure](../articles/firewall/tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

### <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos de Firewall do Azure?

Firewall do Azure oferece suporte a regras e coleções de regras. Uma coleção de regras é um conjunto de regras que partilham a mesma ordem e a prioridade. Coleções de regras são executadas na ordem de sua prioridade. Coleções de regras de rede são de prioridade superior à coleções de regras de aplicação e todas as regras são de terminação.

Existem três tipos de coleções de regras:

* *Regras de aplicações*: Configure nomes de domínio completamente qualificado (FQDN) que podem ser acedidos a partir de uma sub-rede.
* *Regras de rede*: Configure as regras que contêm os endereços de origem, protocolos, portas de destino e endereços de destino.
* *Regras NAT*: Configure regras DNAT para permitir ligações de entrada.

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Firewall do Azure suporta a filtragem de tráfego de entrada?

Firewall do Azure suporta a filtragem de entrada e saída. Proteção de entrada é para os protocolos não-HTTP/S. Para protocolos FTP, RDP e SSH de exemplo.

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Os serviços de registo e análise são suportados pela Firewall do Azure?

Firewall do Azure está integrado com o Azure Monitor para visualizar e analisar os registos de firewall. Os registos podem ser enviados para o Log Analytics, o armazenamento do Azure ou os Hubs de eventos. Eles podem ser analisados no Log Analytics ou por diferentes ferramentas como o Excel e Power BI. Para obter mais informações, consulte [Tutorial: Monitorize registos de Firewall do Azure](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como Firewall do Azure funciona forma diferente de serviços existentes como NVAs no marketplace?

Firewall do Azure é um serviço de firewall básica que pode atender a determinados cenários de cliente. Espera-se que terá uma combinação de NVAs de terceiros e a Firewall do Azure. Funcionar melhor em conjunto é uma prioridade de núcleo.

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>O que é a diferença entre o WAF do Gateway de aplicação e o Firewall do Azure?

A Firewall de aplicações Web (WAF) é um recurso do Gateway de aplicação que fornece proteção centralizada de entrada de seus aplicativos web de exploits e vulnerabilidades comuns. Firewall do Azure fornece proteção de entrada para os protocolos não-HTTP/S (por exemplo, RDP, SSH, FTP), a proteção de nível de rede de saída para todas as portas e protocolos e a proteção de ao nível da aplicação de saída HTTP/S.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>O que é a diferença entre grupos de segurança de rede (NSGs) e o Firewall do Azure?

O serviço de Firewall do Azure complementa a funcionalidade de grupo de segurança de rede. Juntos, eles fornecem uma maior segurança de rede de "defesa em profundidade". Grupos de segurança de rede fornecem o tráfego de camada de rede distribuída filtragem para limitar o tráfego para recursos dentro de redes virtuais em cada subscrição. Firewall do Azure é uma rede totalmente com monitoração de estado e centralizada firewall como-serviço, que fornece proteção de nível de rede e de aplicativo em diferentes subscrições e redes virtuais.

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como posso configurar o Firewall do Azure com meus pontos finais de serviço?

Para obter acesso seguro aos serviços de PaaS, recomendamos que pontos finais de serviço. Pode optar por ativar pontos finais de serviço na sub-rede de Firewall do Azure e desativá-las nas redes virtuais spoke ligada. Dessa forma se beneficia da segurança de ponto final funcionalidades - service e de registo central para todo o tráfego.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Quais são os preços para o Firewall do Azure?

Firewall do Azure tem um custo fixo + custos variáveis:

* Taxa fixa: $1.25/firewall/hour
* Taxa variável: $0.03/ GB processado pelo firewall (entrada ou saída)

Não existem não existem custos de uma firewall desalocada.

Para obter mais informações, consulte [preços de Firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Como parar e iniciar a Firewall do Azure?

Pode utilizar o Azure PowerShell *desalocar* e *alocar* métodos.

Por exemplo:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Deverá realocar um firewall e o IP público para o grupo de recursos originais e a subscrição.

### <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidas?

Para os limites do serviço de Firewall do Azure, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../articles/azure-subscription-service-limits.md#azure-firewall-limits)

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Pode a Firewall do Azure numa rede virtual de hub para a frente e filtrar o tráfego de rede entre duas redes virtuais do spoke?

Sim, pode utilizar a Firewall do Azure numa rede virtual para o tráfego e filtro de rota entre a rede virtual do spoke dois hub. As sub-redes em cada uma das redes virtuais "spoke" tem de ter UDR apontando para o Firewall do Azure como um gateway predefinido para este cenário funcione corretamente.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Pode reencaminhar o Firewall do Azure e filtrar o tráfego de rede entre sub-redes na mesma rede virtual ou em modo de peering de redes virtuais?

Sim. No entanto, configurar as UDRs para redirecionar o tráfego entre sub-redes na mesma VNET necessita de atenção adicional. Ao utilizar o intervalo de endereços da VNET como um prefixo de destino para o UDR é suficiente, isso também roteia todo o tráfego de um computador para outro computador na mesma sub-rede por meio da instância de Firewall do Azure. Para evitar isto, incluir uma rota para a sub-rede no UDR com um tipo de próximo salto **VNET**. Gerir estas rotas pode ser complicado e propenso a erros. O método recomendado para segmentação de rede interna é usar os grupos de segurança de rede, que não exigem as UDRs.

### <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Forçar túnel/encadeamento a uma aplicação Virtual de rede suportados?

Sim.

Firewall do Azure tem de ter conectividade à Internet direta. Por predefinição, o AzureFirewallSubnet tem uma rota de 0.0.0.0/0 com o valor de NextHopType definido como **Internet**.

Se ativar o protocolo de túnel forçado para o local através do ExpressRoute ou o Gateway de VPN, terá de configurar uma rota definida pelo utilizador de 0.0.0.0/0 (UDR) com o conjunto de valor NextHopType como Internet e associá-lo a seu AzureFirewallSubnet de explicitamente. Esta ação substitui um gateway predefinido potenciais anúncio do BGP voltar à sua rede no local. Se sua organização precisar de túnel forçado para o Firewall do Azure direcionar o tráfego de gateway padrão voltar por meio de sua rede no local, contacte o suporte. Podemos lista de permissões é mantida a sua subscrição para garantir a conectividade à Internet de firewall necessárias.

### <a name="are-there-any-firewall-resource-group-restrictions"></a>Existem qualquer firewall restrições do grupo de recursos?

Sim. O firewall, sub-rede, VNet e o endereço IP público, que todos têm de estar no mesmo grupo de recursos.

### <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Ao configurar DNAT para tráfego de rede de entrada, é também necessário configurar uma regra de rede correspondente para permitir que o tráfego?

Não. Regras NAT implicitamente adicione uma regra de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Para saber mais sobre a lógica de processamento de regras do Azure Firewall, veja [Lógica de processamento de regras do Azure Firewall](../articles/firewall/rule-processing.md).

### <a name="how-to-wildcards-work-in-an-application-rule-target-fqdn"></a>Como a carateres universais funcionar num destino de regra de aplicação FQDN?

Se configurar ***. contoso.com**, permite *anyvalue*. contoso.com, mas não contoso.com (o vértice do domínio). Se pretender permitir que o vértice do domínio, deve configurá-lo explicitamente como um destino FQDN.
