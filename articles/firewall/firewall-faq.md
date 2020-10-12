---
title: FAQ do Azure Firewall
description: FAQ para Azure Firewall. Um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos da Rede Virtual Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 75435155ba1dad798d301006a30a5d5b6e96226a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88611182"
---
# <a name="azure-firewall-faq"></a>FAQ do Azure Firewall

## <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É um firewall-as-service totalmente imponente com alta disponibilidade incorporada e escalabilidade de nuvem sem restrições. Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Que capacidades são suportadas no Azure Firewall?

Para saber mais sobre as funcionalidades do Azure Firewall, consulte [as funcionalidades do Azure Firewall](features.md).

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo típico de implantação do Azure Firewall?

Pode implementar o Azure Firewall em qualquer rede virtual, mas os clientes normalmente implementam-no numa rede virtual central e fazem o peering de outras redes virtuais para o mesmo num modelo hub-and-spoke. Em seguida, pode definir a rota padrão a partir das redes virtuais espreitadas para apontar para esta rede virtual de firewall central. O espremiamento global do VNet é suportado, mas não é recomendado devido a potenciais problemas de desempenho e latência em todas as regiões. Para obter o melhor desempenho, implemente uma firewall por região.

A vantagem deste modelo é a capacidade de exercer centralmente o controlo de várias VNETs spoke em diferentes subscrições. Também existem economias de custos, uma vez que não precisa de implantar uma firewall em cada VNet separadamente. As economias de custos devem ser avaliadas em relação ao custo de peering associado com base nos padrões de tráfego do cliente.

## <a name="how-can-i-install-the-azure-firewall"></a>Como posso instalar o Azure Firewall?

Pode configurar o Azure Firewall utilizando o portal Azure, PowerShell, REST API ou utilizando modelos. Ver [Tutorial: Implementar e configurar a Firewall Azure utilizando o portal Azure](tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

## <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos de Azure Firewall?

A Azure Firewall suporta regras e coleções de regras. Uma coleção de regras é um conjunto de regras que partilham a mesma ordem e prioridade. As coleções de regras são executadas por ordem da sua prioridade. As coleções de regras de rede são mais prioritárias do que as coleções de regras de aplicação, e todas as regras estão terminando.

Existem três tipos de coleções de regras:

* *Regras de aplicação*: Configurar nomes de domínio totalmente qualificados (FQDNs) que podem ser acedidos a partir de uma sub-rede.
* *Regras de rede*: Configurar regras que contenham endereços de origem, protocolos, portas de destino e endereços de destino.
* *Regras NAT*: Configurar as regras do DNAT para permitir a entrada de ligações à Internet.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Azure Firewall suporta a filtragem de tráfego de entrada?

O Azure Firewall suporta a filtragem de entrada e saída. A proteção de entrada é normalmente utilizada para protocolos não-HTTP/S. Por exemplo, protocolos RDP, SSH e FTP. Para obter uma melhor proteção HTTP/S de entrada, utilize uma firewall de aplicação web como [a Firewall de Aplicação Web Azure (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Que serviços de registo e análise são suportados pelo Azure Firewall?

O Azure Firewall está integrado com o Azure Monitor para visualização e análise de registos de firewall. Os registos podem ser enviados para Log Analytics, Azure Storage ou Event Hubs. Podem ser analisados no Log Analytics ou por diferentes ferramentas como o Excel e o Power BI. Para obter mais informações, consulte [Tutorial: Monitor Azure Firewall registos](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como funciona o Azure Firewall de forma diferente dos serviços existentes, como os NVAs no mercado?

O Azure Firewall é um serviço básico de firewall que pode abordar certos cenários do cliente. Espera-se que tenha uma mistura de NVAs de terceiros e Azure Firewall. Trabalhar melhor juntos é uma prioridade fundamental.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre a Aplicação Gateway WAF e a Azure Firewall?

O Firewall de Aplicações Web (WAF) é uma funcionalidade do Application Gateway que fornece proteção centralizada de entrada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure Firewall fornece proteção de entrada para protocolos não-HTTP/S (por exemplo, RDP, SSH, FTP), proteção ao nível da rede de saída para todas as portas e protocolos, e proteção ao nível da aplicação para HTTP/S de saída.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual é a diferença entre grupos de segurança de rede (NSGs) e Azure Firewall?

O serviço Azure Firewall complementa a funcionalidade do grupo de segurança de rede. Juntos, proporcionam uma melhor segurança na rede "defesa-em-profundidade". Os grupos de segurança de rede fornecem a filtragem do tráfego da camada de rede distribuído para limitar o tráfego a recursos nas redes virtuais em cada subscrição. O Azure Firewall é uma firewall de rede totalmente imponente e centralizada como serviço, que fornece proteção ao nível da rede e aplicação em diferentes subscrições e redes virtuais.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>Os Grupos de Segurança da Rede (NSGs) são suportados na AzureFirewallSubnet?

Azure Firewall é um serviço gerido com várias camadas de proteção, incluindo proteção de plataforma com NSGs de nível NIC (não visível).  Os NSGs de nível de sub-rede não são necessários na AzureFirewallSubnet e são desativados para garantir que não há interrupção de serviço.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como posso configurar o Azure Firewall com os meus pontos finais de serviço?

Para acesso seguro aos serviços PaaS, recomendamos pontos finais de serviço. Pode optar por ativar os pontos finais de serviço na sub-rede Azure Firewall e desativá-los nas redes virtuais ligadas. Desta forma, beneficia de ambas as funcionalidades: segurança no ponto final de serviço e registo central para todo o tráfego.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço do Azure Firewall?

Ver [Preços da Firewall Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Como posso parar e começar a Azure Firewall?

Você pode usar o *deallocate* Azure PowerShell e *alocar* métodos.

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
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip1,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Você deve realocar uma firewall e IP público para o grupo de recursos originais e subscrição.

## <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidos?

Para os limites de serviço da Azure Firewall, consulte [os limites de subscrição e serviço da Azure, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>O Azure Firewall pode num hub de rede virtual avançar e filtrar o tráfego da rede entre duas redes virtuais faladas?

Sim, pode utilizar o Azure Firewall numa rede virtual do hub para encaminhar e filtrar o tráfego entre duas redes virtuais falada. As sub-redes de cada uma das redes virtuais faladas devem ter um UDR apontando para o Azure Firewall como uma porta de entrada padrão para que este cenário funcione corretamente.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>O Azure Firewall pode avançar e filtrar o tráfego de rede entre sub-redes na mesma rede virtual ou redes virtuais?

Sim. No entanto, configurar as UDRs para redirecionar o tráfego entre sub-redes no mesmo VNET requer uma atenção adicional. Ao utilizar a gama de endereços VNET como prefixo-alvo para o UDR é suficiente, isto também liga todo o tráfego de uma máquina para outra máquina na mesma sub-rede através da instância Azure Firewall. Para evitar isto, inclua uma rota para a sub-rede na UDR com um próximo tipo de **VNET**. Gerir estas rotas pode ser complicado e propenso a erros. O método recomendado para a segmentação interna da rede é utilizar grupos de segurança de rede, que não requerem RAM.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>A Azure Firewall sai do SNAT entre redes privadas?

O Azure Firewall não sNAT quando o endereço IP de destino é uma gama IP privada por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se a sua organização utilizar um intervalo de endereços IP público para redes privadas, o Azure Firewall SNATs faz o tráfego para um dos endereços IP privados de firewall em AzureFirewallSubnet. Pode configurar o Azure Firewall para **não** sNAT o seu intervalo de endereços IP público. Para obter mais informações, consulte [as gamas de endereços IP privados Azure Firewall SNAT](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>O túnel/acorrentamento forçado a um aparelho virtual de rede é suportado?

Os túneis forçados são suportados quando se cria uma nova firewall. Não se pode configurar uma firewall existente para fazer túneis forçados. Para mais informações, consulte [o Azure Firewall forjando túneis.](forced-tunneling.md)

O Azure Firewall tem de ter conectividade Internet direta. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a isto com um UDR de 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta na Internet.

Se a sua configuração necessitar de um túnel forçado para uma rede no local e puder determinar os prefixos IP alvo para os seus destinos na Internet, pode configurar estas gamas com a rede no local como o próximo lúpulo através de uma rota definida pelo utilizador na AzureFirewallSubnet. Ou, você pode usar BGP para definir estas rotas.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Existem restrições ao grupo de recursos de firewall?

Sim. A firewall, o VNet e o endereço IP público devem estar todos no mesmo grupo de recursos.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Ao configurar o DNAT para o tráfego de rede de Internet de entrada, também preciso de configurar uma regra de rede correspondente para permitir esse tráfego?

N.º As regras da NAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Para saber mais sobre a lógica de processamento de regras do Azure Firewall, veja [Lógica de processamento de regras do Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Como funcionam os wildcards numa regra de aplicação alvo FQDN?

Wildcards atualmente só podem ser usados no lado esquerdo do FQDN. Por exemplo, ***.contoso.com** e ***contoso.com**.

Se configurar ***.contoso.com,** permite *qualquer valor*.contoso.com, mas não contoso.com (o ápice do domínio). Se quiser permitir o ápice de domínio, deve configurá-lo explicitamente como um FQDN alvo.

## <a name="what-does-provisioning-state-failed-mean"></a>O que *significa Provisioning: Falhado?*

Sempre que uma alteração de configuração é aplicada, o Azure Firewall tenta atualizar todas as instâncias de back-end subjacentes. Em casos raros, uma destas instâncias de backend pode não conseguir atualizar-se com a nova configuração e o processo de atualização para com um estado de provisionamento falhado. O Azure Firewall continua operacional, mas a configuração aplicada poderá estar num estado inconsistente, em que algumas instâncias têm a configuração anterior e outras têm o conjunto de regras atualizado. Se isto acontecer, tente atualizar a sua configuração mais uma vez até que a operação tenha sucesso e a sua Firewall esteja num estado de provisionamento *bem sucedido.*

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Como é que a Azure Firewall lida com a manutenção planeada e falhas não planeadas?
O Azure Firewall consiste em vários nós de backend numa configuração ativa.  Para qualquer manutenção planeada, temos lógica de drenagem de ligação para atualizar graciosamente os nós.  Estão previstas atualizações durante o horário não comercial para cada uma das regiões do Azure para limitar ainda mais o risco de perturbação.  Para questões não planeadas, instantaneamos um novo nó para substituir o nó falhado.  A conectividade com o novo nó é tipicamente restabelecida dentro de 10 segundos a partir do momento da falha.

## <a name="how-does-connection-draining-work"></a>Como funciona a drenagem de ligação?

Para qualquer manutenção planeada, a lógica de drenagem de ligação atualiza graciosamente os nós de backend. O Azure Firewall aguarda 90 segundos para que as ligações existentes fechem. Se necessário, os clientes podem restabelecer automaticamente a conectividade com outro nó de backend.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Existe um limite de caracteres para um nome de firewall?

Sim. Há um limite de 50 caracteres para um nome de firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Porque é que o Azure Firewall precisa de uma sub-rede /26?

O Azure Firewall deve providenciar mais casos de máquinas virtuais à medida que escala. Um espaço de endereço /26 garante que a firewall tem endereços IP suficientes disponíveis para acomodar o dimensionamento.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>O tamanho da sub-rede de firewall precisa de ser alterado à medida que a balança de serviço é alterada?

N.º A Azure Firewall não precisa de uma sub-rede maior que /26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Como posso aumentar a minha produção de firewall?

A capacidade inicial de produção do Azure Firewall é de 2,5 - 3 Gbps e escama-se para 30 Gbps. Escala automaticamente com base no uso e produção de CPU.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Quanto tempo demora a Azure Firewall a escalonar?

O Azure Firewall escala gradualmente quando o consumo médio de produção ou CPU está em 60%. Uma potência máxima de implantação predefinida é de aproximadamente 2,5 - 3 Gbps e começa a escalar quando atinge 60% desse número. A escala leva de 5 a 7 minutos. 

Ao testar o desempenho, certifique-se de testar durante pelo menos 10 a 15 minutos e iniciar novas ligações para tirar partido dos novos nós de Firewall criados.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>O Azure Firewall permite o acesso ao Ative Directory por defeito?

N.º Azure Firewall bloqueia o acesso ao Ative Directory por predefinição. Para permitir o acesso, configurar a etiqueta de serviço AzureActiveDirectory. Para obter mais informações, consulte [as etiquetas de serviço Azure Firewall](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Posso excluir um FQDN ou um endereço IP da filtragem baseada em ameaças de firewall Azure?

Sim, pode usar a Azure PowerShell para o fazer:

```azurepowershell
# Add a Threat Intelligence allow list to an Existing Azure Firewall

## Create the allow list with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Por que um ping TCP e ferramentas semelhantes podem ligar-se com sucesso a um FQDN alvo mesmo quando nenhuma regra sobre a Firewall Azure permite esse tráfego?

Um ping TCP não está realmente ligado ao FQDN alvo. Isto acontece porque o representante transparente da Azure Firewall ouve na porta 80/443 para tráfego de saída. O ping TCP estabelece uma ligação com a firewall, que depois deixa cair o pacote e regista a ligação. Este comportamento não tem nenhum impacto na segurança. No entanto, para evitar confusões, estamos a investigar possíveis alterações a este comportamento.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Existem limites para o número de endereços IP suportados por grupos IP?

Sim. Para mais informações, consulte [limites de subscrição e serviço da Azure, quotas e constrangimentos](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>Posso mover um Grupo IP para outro grupo de recursos?

Não, a transferência de um Grupo IP para outro grupo de recursos não é suportada atualmente.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>O que é o TCP Idle Timeout para Azure Firewall?

Um comportamento padrão de uma firewall de rede é garantir que as ligações TCP são mantidas vivas e fechá-las rapidamente se não houver atividade. O tempo limite de funcionamento da Azure Firewall TCP é de quatro minutos. Esta definição não é configurável. Se um período de inatividade for maior do que o valor de tempo limite, não há garantia de que a sessão TCP ou HTTP seja mantida. Uma prática comum é usar um TCP de vida. Esta prática mantém a ligação ativa por um período mais longo. Para obter mais informações, consulte os [exemplos .NET](https://docs.microsoft.com/dotnet/api/system.net.servicepoint.settcpkeepalive?redirectedfrom=MSDN&view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_).

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>Posso implantar o Azure Firewall sem um endereço IP público?

Não, atualmente tem de implantar o Azure Firewall com um endereço IP público.

## <a name="where-does-azure-firewall-store-customer-data"></a>Onde é que a Azure Firewall armazena os dados dos clientes?

O Azure Firewall não move nem armazena os dados dos clientes para fora da região onde está implantado.
