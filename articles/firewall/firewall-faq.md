---
title: Azure Firewall FAQ
description: FAQ para Firewall Azure. Um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos da Rede Virtual Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/29/2020
ms.author: victorh
ms.openlocfilehash: 78269461bf01d61bffeed504b0168b4913c6e131
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77442994"
---
# <a name="azure-firewall-faq"></a>Azure Firewall FAQ

## <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É um firewall-as-a-service totalmente audato com alta disponibilidade incorporada e escalabilidade de nuvem sem restrições. Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Que capacidades são suportadas no Firewall Azure?

* Firewall audada como um serviço
* Elevada disponibilidade incorporada com escalabilidade de nuvens sem restrições
* Filtragem FQDN
* Etiquetas FQDN
* Regras de filtragem de tráfego de rede
* Suporte SNAT de saída
* Suporte DNAT de entrada
* Centralmente criar, impor e registar aplicações e políticas de conectividade de rede em todas as subscrições do Azure e VNETs
* Totalmente integrado com o Monitor Azure para exploração madeireira e analítica

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo típico de implementação do Azure Firewall?

Você pode implementar o Azure Firewall em qualquer rede virtual, mas os clientes normalmente implantam-no numa rede virtual central e peer outras redes virtuais para ele num modelo hub-e-spoke. Em seguida, pode definir a rota padrão a partir das redes virtuais peered para apontar para esta rede virtual de firewall central. O peering Global VNet é suportado, mas não é recomendado devido a potenciais problemas de desempenho e latência em todas as regiões. Para melhor desempenho, implemente uma firewall por região.

A vantagem deste modelo é a capacidade de exercer centralmente o controlo sobre múltiplos VNETs falados em diferentes subscrições. Existem também poupanças de custos, uma vez que não precisa de implementar uma firewall em cada VNet separadamente. As economias de custos devem ser medidas em relação ao custo associado de peering com base nos padrões de tráfego do cliente.

## <a name="how-can-i-install-the-azure-firewall"></a>Como posso instalar o Firewall Azure?

Pode configurar o Azure Firewall utilizando o portal Azure, PowerShell, REST API ou utilizando modelos. Consulte [Tutorial: Desloque e configure a Firewall Azure utilizando o portal Azure](tutorial-firewall-deploy-portal.md) para instruções passo a passo.

## <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos de Firewall Azure?

A Azure Firewall suporta regras e coleções de regras. Uma coleção de regras é um conjunto de regras que partilham a mesma ordem e prioridade. As coleções de regras são executadas por ordem da sua prioridade. As coleções de regras de rede são mais prioritárias do que as coleções de regras de aplicação, e todas as regras estão terminando.

Existem três tipos de coleções de regras:

* *Regras de aplicação*: Configure nomes de domínio totalmente qualificados (FQDNs) que possam ser acedidos a partir de uma subnet.
* *Regras de rede*: Configure regras que contenham endereços de origem, protocolos, portas de destino e endereços de destino.
* *Regras NAT*: Configure as regras de ADN para permitir a entrada de ligações.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>A Firewall Azure suporta a filtragem de tráfego de entrada?

A Firewall Azure suporta a filtragem de entrada e saída. A proteção de entrada é normalmente utilizada para protocolos não HTTP/S. Por exemplo, protocolos RDP, SSH e FTP. Para uma melhor proteção HTTP/S de entrada, utilize uma firewall de aplicação web, como [o Firewall de aplicação web Azure no Portal de Aplicações Azure](../web-application-firewall/ag/ag-overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Que serviços de exploração madeireira e analítica são suportados pela Firewall Azure?

O Azure Firewall está integrado com o Monitor Azure para visualização e análise de registos de firewall. Os registos podem ser enviados para Log Analytics, Azure Storage ou Event Hubs. Podem ser analisados em Log Analytics ou por diferentes ferramentas como Excel e Power BI. Para mais informações, consulte [Tutorial: Monitor Azure Firewall logs](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como funciona o Azure Firewall de forma diferente dos serviços existentes, como os NVAs no mercado?

O Azure Firewall é um serviço básico de firewall que pode abordar certos cenários do cliente. Espera-se que tenha uma mistura de NVAs de terceiros e Firewall Azure. Trabalhar melhor juntos é uma prioridade fundamental.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre application Gateway WAF e Azure Firewall?

O Firewall de aplicação web (WAF) é uma funcionalidade do Application Gateway que fornece proteção centralizada das suas aplicações web a partir de explorações e vulnerabilidades comuns. A Azure Firewall fornece proteção de entrada para protocolos não HTTP/S (por exemplo, RDP, SSH, FTP), proteção de nível de rede de saída para todos os portos e protocolos, e proteção ao nível da aplicação para a saída HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual é a diferença entre grupos de segurança de rede (NSGs) e Firewall Azure?

O serviço Azure Firewall complementa a funcionalidade do grupo de segurança da rede. Juntos, proporcionam uma melhor segurança da rede "defesa-em profundidade". Os grupos de segurança da rede fornecem filtragem de tráfego de camadas de rede distribuída para limitar o tráfego aos recursos dentro das redes virtuais em cada subscrição. O Azure Firewall é um firewall de rede totalmente audacionado e centralizado como um serviço, que fornece proteção ao nível da rede e da aplicação em diferentes subscrições e redes virtuais.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Os Grupos de Segurança da Rede (NSGs) são suportados na subnet Azure Firewall?

O Azure Firewall é um serviço gerido com múltiplas camadas de proteção, incluindo a proteção da plataforma com NSGs de nível NIC (não visível).  NsGs de nível de sub-rede não são necessários na subnet Azure Firewall, e são desativados para garantir que não há interrupção do serviço.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como configurar o Azure Firewall com os meus pontos finais de serviço?

Para acesso seguro aos serviços PaaS, recomendamos pontos finais de serviço. Pode optar por ativar pontos finais de serviço na subnet Azure Firewall e desativá-los nas redes virtuais de raios ligados. Desta forma, beneficia de ambas as funcionalidades... segurança de ponto final de serviço e registo central para todo o tráfego.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço do Azure Firewall?

Ver Preços de [Firewall Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Como posso parar e começar o Azure Firewall?

Pode utilizar o *Desalocar* e *alocar* métodos azure PowerShell.

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
> Você deve realocar uma firewall e IP público para o grupo de recursos originais e subscrição.

## <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidos?

Para os limites de serviço azure Firewall, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Pode o Azure Firewall numa rede virtual de hub para a frente e filtrar o tráfego da rede entre duas redes virtuais faladas?

Sim, você pode usar o Azure Firewall numa rede virtual hub para direcionar e filtrar o tráfego entre duas redes virtuais. As subredes em cada uma das redes virtuais faladas devem ter o UDR apontado para o Firewall Azure como uma porta de entrada padrão para que este cenário funcione corretamente.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>O Azure Firewall pode avançar e filtrar o tráfego de rede entre subredes na mesma rede virtual ou redes virtuais?

Sim. No entanto, configurar os UDRs para redirecionar o tráfego entre subredes no mesmo VNET requer atenção adicional. Ao utilizar a gama de endereços VNET como prefixo-alvo para o UDR é suficiente, isto também faz todo o tráfego de uma máquina para outra na mesma sub-rede através da instância De firewall Azure. Para evitar isto, inclua uma rota para a sub-rede na UDR com um próximo tipo de **VNET**de lúpulo . Gerir estas rotas pode ser complicado e propenso a erros. O método recomendado para segmentação interna da rede é usar grupos de segurança de rede, que não requerem UDRs.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>O Azure Firewall sai do SNAT entre redes privadas?

O Azure Firewall não snaT quando o endereço IP de destino é uma gama ip privada por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se a sua organização utilizar uma gama pública de endereços IP para redes privadas, o Azure Firewall SNATs snats o tráfego para um dos endereços IP privados firewall em AzureFirewallSubnet. Pode configurar o Azure Firewall para **não** sNAT a sua gama pública de endereços IP. Para mais informações, consulte as gamas de [endereços IP privados Azure Firewall SNAT](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>O túnel/acorrentamento forçado a um Aparelho Virtual da Rede é suportado?

O túnel forçado não é suportado atualmente. O Azure Firewall deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a esta com um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta da Internet.

Se a sua configuração necessitar de túneis forçados para uma rede no local e puder determinar os prefixos IP-alvo para os seus destinos de Internet, pode configurar estas gamas com a rede no local como o próximo salto através de uma rota definida pelo utilizador no AzureFirewallSubnet. Ou, pode usar o BGP para definir estas rotas.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Existem restrições ao grupo de recursos de firewall?

Sim. A firewall, a subnet, a VNet e o endereço IP público devem estar todos no mesmo grupo de recursos.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Ao configurar o DNAT para o tráfego de rede de entrada, também preciso de configurar uma regra de rede correspondente para permitir esse tráfego?

Não. As regras do NAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Para saber mais sobre a lógica de processamento de regras do Azure Firewall, veja [Lógica de processamento de regras do Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Como funcionam os wildcards numa regra de aplicação alvo FQDN?

Se configurar * **.contoso.com,** permite *qualquer valor*.contoso.com, mas não contoso.com (o ápice de domínio). Se pretender permitir o ápice de domínio, deve configurá-lo explicitamente como um FQDN alvo.

## <a name="what-does-provisioning-state-failed-mean"></a>O que significa *provisionamento: Falhado?*

Sempre que é aplicada uma alteração de configuração, o Azure Firewall tenta atualizar todas as suas instâncias de backend subjacentes. Em casos raros, um destes casos de backend pode não atualizar com a nova configuração e o processo de atualização para com um estado de provisionamento falhado. O seu Firewall Azure ainda está operacional, mas a configuração aplicada pode estar num estado inconsistente, onde alguns casos têm a configuração anterior onde outros têm o conjunto de regras atualizado. Se isso acontecer, tente atualizar a sua configuração mais uma vez até que a operação tenha sucesso e a sua Firewall esteja num estado de provisionamento *bem sucedido.*

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Como é que a Azure Firewall lida com a manutenção planeada e falhas não planeadas?
O Azure Firewall é composto por vários nódeos traseiros numa configuração ativa.  Para qualquer manutenção planeada, temos a lógica de drenagem de ligação para atualizar graciosamente os nós.  Estão previstas atualizações durante o horário não comercial para cada uma das regiões de Azure para limitar ainda mais o risco de perturbação.  Para questões não planeadas, nós instantaneamente um novo nó para substituir o nó falhado.  A conectividade com o novo nó é normalmente restabelecida em 10 segundos após a hora da falha.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Existe um limite de carácter para um nome de firewall?

Sim. Há um limite de 50 caracteres para um nome de firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Porque é que o Azure Firewall precisa de uma sub-rede /26?

O Azure Firewall deve fornecer mais casos de máquinas virtuais à medida que se escala. Um espaço de endereço /26 garante que a firewall dispõe de endereços IP suficientes para acomodar a escala.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>O tamanho da subrede de firewall precisa de ser alterado à medida que as escalas de serviço?

Não. A Firewall Azure não precisa de uma sub-rede maior que a de 1,26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Como posso aumentar a minha entrada de firewall?

A capacidade inicial de entrada da Azure Firewall é de 2,5 - 3 Gbps. Atualmente, a escala é baseada apenas no uso de CPU. Em alguns casos, uma firewall com regras de rede só não vai aumentar para aumentar a entrada porque as regras da rede não afetam significativamente o uso de CPU. Se precisar de uma entrada mais elevada para a sua firewall, contacte o Suporte para aumentar a capacidade inicial de entrada da sua firewall.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Quanto tempo leva para o Azure Firewall aumentar a escala?

Atualmente, leva de cinco a sete minutos para o Azure Firewall escalar. Se tiver rajadas que exijam uma escala automática mais rápida, contacte o Suporte para aumentar a capacidade inicial de entrada da sua firewall.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>O Firewall Azure permite o acesso ao Diretório Ativo por defeito?

Não. O Azure Firewall bloqueia o acesso ao Diretório Ativo por defeito. Para permitir o acesso, configure a etiqueta de serviço AzureActiveDirectory. Para mais informações, consulte as etiquetas de [serviço Azure Firewall](service-tags.md).
