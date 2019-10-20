---
title: Perguntas frequentes do firewall do Azure
description: Perguntas frequentes sobre o Firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: victorh
ms.openlocfilehash: f64e9717a1e6391c15ee5207c7566114f2bf9f8f
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596786"
---
# <a name="azure-firewall-faq"></a>Perguntas frequentes do firewall do Azure

## <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É um firewall como um serviço totalmente monitorado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quais recursos têm suporte no firewall do Azure?

* Firewall com monitorização de estado como serviço
* Elevada disponibilidade incorporada com escalabilidade da cloud sem restrições
* Filtragem FQDN
* Etiquetas FQDN
* Regras de filtragem de tráfego de rede
* Suporte SNAT de saída
* Suporte DNAT de entrada
* Criar, impor e registrar políticas de conectividade de rede e de aplicativo de forma centralizada nas assinaturas e VNETs do Azure
* Integração total com o Azure Monitor para fins de registo e análise

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo de implantação típico para o Firewall do Azure?

Você pode implantar o Firewall do Azure em qualquer rede virtual, mas os clientes normalmente o implantam em uma rede virtual central e emparelham outras redes virtuais a ele em um modelo de Hub e spoke. Em seguida, você pode definir a rota padrão das redes virtuais emparelhadas para apontar para essa rede virtual de firewall central. O emparelhamento VNet global tem suporte, mas não é recomendado devido a possíveis problemas de desempenho e latência entre regiões. Para obter o melhor desempenho, implante um firewall por região.

A vantagem desse modelo é a capacidade de exercer controle central em vários VNETs spoke em diferentes assinaturas. Também há economia de custos, pois você não precisa implantar um firewall em cada VNet separadamente. As economias de custo devem ser medidas versus o custo de emparelhamento associado com base nos padrões de tráfego do cliente.

## <a name="how-can-i-install-the-azure-firewall"></a>Como posso instalar o Firewall do Azure?

Você pode configurar o Firewall do Azure usando o portal do Azure, o PowerShell, a API REST ou o usando modelos. Consulte [tutorial: implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

## <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns dos conceitos de firewall do Azure?

O Firewall do Azure dá suporte a regras e coleções de regras. Uma coleção de regras é um conjunto de regras que compartilham a mesma ordem e prioridade. As coleções de regras são executadas em ordem de prioridade. As coleções de regras de rede têm prioridade mais alta do que as coleções de regras de aplicativo e todas as regras estão sendo encerradas.

Há três tipos de coleções de regras:

* *Regras de aplicativo*: Configure FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados de uma sub-rede.
* *Regras de rede*: Configure regras que contêm endereços de origem, protocolos, portas de destino e endereços de destino.
* *Regras de NAT*: configurar regras de DNAT para permitir conexões de entrada.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Firewall do Azure dá suporte à filtragem de tráfego de entrada?

O Firewall do Azure dá suporte à filtragem de entrada e saída. A proteção de entrada é para protocolos não HTTP/S. Por exemplo, protocolos RDP, SSH e FTP.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quais serviços de log e de análise têm suporte no firewall do Azure?

O Firewall do Azure é integrado com Azure Monitor para exibir e analisar logs de firewall. Os logs podem ser enviados para Log Analytics, armazenamento do Azure ou hubs de eventos. Eles podem ser analisados em Log Analytics ou em ferramentas diferentes, como Excel e Power BI. Para obter mais informações, consulte [tutorial: monitorar logs de firewall do Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como o Firewall do Azure funciona de maneira diferente dos serviços existentes, como o NVAs no Marketplace?

O Firewall do Azure é um serviço de Firewall básico que pode atender a determinados cenários de clientes. Espera-se que você tenha uma combinação de NVAs de terceiros e firewall do Azure. Trabalhar melhor juntos é uma prioridade básica.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre o WAF do gateway de aplicativo e o Firewall do Azure?

O WAF (firewall do aplicativo Web) é um recurso do gateway de aplicativo que fornece proteção de entrada centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O Firewall do Azure fornece proteção de entrada para protocolos não HTTP/S (por exemplo, RDP, SSH, FTP), proteção no nível de rede de saída para todas as portas e protocolos e proteção em nível de aplicativo para HTTP/S de saída.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual é a diferença entre NSGs (grupos de segurança de rede) e o Firewall do Azure?

O serviço de firewall do Azure complementa a funcionalidade do grupo de segurança de rede. Juntos, eles fornecem uma melhor segurança de rede de "defesa aprofundada". Os grupos de segurança de rede fornecem filtragem de tráfego de camada de rede distribuída para limitar o tráfego a recursos em redes virtuais em cada assinatura. O Firewall do Azure é um firewall de rede centralizado e totalmente monitorado como um serviço, que fornece proteção em nível de rede e de aplicativo em diferentes assinaturas e redes virtuais.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Há suporte para NSGs (grupos de segurança de rede) na sub-rede do firewall do Azure?

O Firewall do Azure é um serviço gerenciado com várias camadas de proteção, incluindo a proteção da plataforma com NSGs no nível da NIC (não visível).  Os NSGs de nível de sub-rede não são necessários na sub-rede do firewall do Azure e estão desabilitados para garantir que nenhuma interrupção de serviço seja interrompida.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como fazer configurar o Firewall do Azure com meus pontos de extremidade de serviço?

Para acesso seguro aos serviços de PaaS, recomendamos os pontos de extremidade de serviço. Você pode optar por habilitar os pontos de extremidade de serviço na sub-rede do firewall do Azure e desabilitá-los nas redes virtuais de spoke conectadas. Dessa forma, você se beneficia de ambos os recursos – segurança de ponto de extremidade de serviço e log central para todo o tráfego.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço do firewall do Azure?

Consulte [preços do firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Como posso parar e iniciar o Firewall do Azure?

Você pode usar Azure PowerShell *desalocar* e *alocar* métodos.

Por exemplo:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Você deve realocar um firewall e um IP público para o grupo de recursos e a assinatura originais.

## <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidos?

Para limites de serviço de firewall do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>O Firewall do Azure em uma rede virtual do Hub pode avançar e filtrar o tráfego de rede entre duas redes virtuais spoke?

Sim, você pode usar o Firewall do Azure em uma rede virtual de Hub para rotear e filtrar o tráfego entre duas redes virtuais spoke. As sub-redes em cada uma das redes virtuais spoke devem ter UDR apontando para o Firewall do Azure como um gateway padrão para que esse cenário funcione corretamente.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>O Firewall do Azure pode encaminhar e filtrar o tráfego de rede entre sub-redes na mesma rede virtual ou redes virtuais emparelhadas?

Sim. No entanto, configurar o UDRs para redirecionar o tráfego entre sub-redes na mesma VNET requer atenção adicional. Ao usar o intervalo de endereços da VNET como um prefixo de destino para o UDR é suficiente, isso também roteia todo o tráfego de um computador para outro na mesma sub-rede por meio da instância do firewall do Azure. Para evitar isso, inclua uma rota para a sub-rede no UDR com um tipo de próximo salto de **VNET**. O gerenciamento dessas rotas pode ser complicado e propenso a erros. O método recomendado para segmentação de rede interna é usar grupos de segurança de rede, que não exigem UDRs.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>O Firewall do Azure faz a saída de SNAT entre redes privadas?

O Firewall do Azure não SNAT quando o endereço IP de destino é um intervalo de IP privado por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure SNATs o tráfego para um dos endereços IP privados do firewall em AzureFirewallSubnet.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Há suporte para túnel/encadeamento forçado para uma solução de virtualização de rede?

O túnel forçado não tem suporte no momento. O Firewall do Azure deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprende uma rota padrão para sua rede local via BGP, você deve substituí-lo por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet.

Se sua configuração requer o túnel forçado para uma rede local e você pode determinar os prefixos de IP de destino para seus destinos de Internet, você pode configurar esses intervalos com a rede local como o próximo salto por meio de uma rota definida pelo usuário no AzureFirewallSubnet. Ou, você pode usar o BGP para definir essas rotas.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Há alguma restrição de grupo de recursos de firewall?

Sim. O firewall, a sub-rede, a VNet e o endereço IP público devem estar no mesmo grupo de recursos.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Ao configurar o DNAT para o tráfego de rede de entrada, também preciso configurar uma regra de rede correspondente para permitir esse tráfego?

Não. As regras de NAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Para saber mais sobre a lógica de processamento de regras do Azure Firewall, veja [Lógica de processamento de regras do Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Como os curingas funcionam em um FQDN de destino de regra de aplicativo?

Se você configurar * **. contoso.com**, ele permitirá *anyvalue*. contoso.com, mas não contoso.com (o Apex do domínio). Se você quiser permitir o Apex do domínio, deverá configurá-lo explicitamente como um FQDN de destino.

## <a name="what-does-provisioning-state-failed-mean"></a>O que significa o *estado de provisionamento: falha na* média?

Sempre que uma alteração de configuração é aplicada, o Firewall do Azure tenta atualizar todas as suas instâncias de back-end subjacentes. Em casos raros, uma dessas instâncias de back-end pode falhar ao atualizar com a nova configuração e o processo de atualização é interrompido com um estado de provisionamento com falha. O Firewall do Azure ainda está operacional, mas a configuração aplicada pode estar em um estado inconsistente, em que algumas instâncias têm a configuração anterior, em que outras têm a regra atualizada definida. Se isso acontecer, tente atualizar sua configuração mais uma vez até que a operação seja bem-sucedida e o firewall esteja em um estado de provisionamento *bem-sucedido* .

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Como o Firewall do Azure lida com falhas não planejadas e de manutenção planejada?
O Firewall do Azure consiste em vários nós de back-end em uma configuração ativo-ativo.  Para qualquer manutenção planejada, temos a lógica de descarga de conexão para atualizar nós normalmente.  As atualizações são planejadas fora do horário comercial para cada uma das regiões do Azure para limitar ainda mais o risco de interrupção.  Para problemas não planejados, criamos uma instância de um novo nó para substituir o nó com falha.  A conectividade com o novo nó é normalmente restabelecida dentro de 10 segundos a partir do momento da falha.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Há um limite de caracteres para um nome de firewall?

Sim. Há um limite de 50 caracteres para um nome de firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Por que o Firewall do Azure precisa de um tamanho de sub-rede/26?

O Firewall do Azure deve provisionar mais instâncias de máquina virtual conforme ele é dimensionado. Um espaço de endereço/26 garante que o firewall tenha endereços IP suficientes disponíveis para acomodar o dimensionamento.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>O tamanho da sub-rede do firewall precisa ser alterado conforme o serviço é dimensionado?

Não. O Firewall do Azure não precisa de uma sub-rede maior que/26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Como posso aumentar a taxa de transferência do meu firewall?

A capacidade de taxa de transferência inicial do firewall do Azure é de 2,5 a 3 Gbps. Atualmente, o scale out é baseado apenas no uso da CPU. Em alguns casos, um firewall com regras de rede não escalará verticalmente para aumentar a taxa de transferência, pois as regras de rede não afetam significativamente o uso da CPU. Se precisar de uma taxa de transferência maior para o firewall, entre em contato com o suporte para aumentar a capacidade de taxa de transferência inicial do firewall.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Quanto tempo leva para o Firewall do Azure escalar horizontalmente?

Atualmente, leva de cinco a sete minutos para que o Firewall do Azure Escale horizontalmente. Se você tiver intermitências que exigem um dimensionamento automático mais rápido, entre em contato com o suporte para aumentar a capacidade de taxa de transferência inicial do firewall.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>O Firewall do Azure permite acesso a Active Directory por padrão?

Não. O Firewall do Azure bloqueia o acesso Active Directory por padrão. Para permitir o acesso, configure a marca de serviço AzureActiveDirectory. Para obter mais informações, consulte [marcas de serviço do firewall do Azure](service-tags.md).
