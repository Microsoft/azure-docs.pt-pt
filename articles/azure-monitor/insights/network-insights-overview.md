---
title: Azure Monitor para Redes
description: Uma visão geral do Azure Monitor for Networks, que proporciona uma visão abrangente da saúde e métricas para todos os recursos de rede implantados sem qualquer configuração.
ms.subservice: ''
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: 5a2238f5c1561abb1e11d69aa6d0bfe761097c64
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437308"
---
# <a name="azure-monitor-for-networks"></a>Azure Monitor para Redes
O Azure Monitor for Networks proporciona uma visão abrangente da [saúde](../../service-health/resource-health-checks-resource-types.md) e métricas para todos os recursos de rede [implantados,](../platform/metrics-supported.md) sem necessidade de qualquer configuração. Também fornece acesso a capacidades de monitorização de rede como [Connection Monitor,](../../network-watcher/connection-monitor-preview.md) [registo de fluxos para grupos de segurança de rede (NSGs)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)e [Traffic Analytics](../../network-watcher/traffic-analytics.md). E fornece outras funcionalidades [de diagnóstico de](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) rede.

O Azure Monitor for Networks está estruturado em torno destes componentes-chave de monitorização:
- [Saúde da rede e métricas](#networkhealth)
- [Conetividade](#connectivity)
- [Trânsito](#traffic)
- [Conjunto de ferramentas de diagnóstico](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Saúde da rede e métricas

A página Azure Monitor for Networks **Overview** fornece uma forma fácil de visualizar o inventário dos seus recursos de networking, juntamente com a saúde dos recursos e alertas. Está dividido em quatro áreas funcionais fundamentais: pesquisa e filtragem, saúde de recursos e métricas, alertas e vista de dependência.

[![Captura de ecrã a mostrar a página Descrição geral](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>Pesquisa e filtragem
Pode personalizar a vista para a saúde dos recursos e alertas utilizando filtros como **Subscrição,** **Grupo de Recursos** e **Tipo**.

Pode utilizar a caixa de pesquisa para procurar recursos e os seus recursos associados. Por exemplo, um IP público está associado a um gateway de aplicação. Uma pesquisa pelo nome DNS do IP público devolverá o IP público e o gateway de aplicações associado:

[![Screenshot que mostra resultados de pesquisa do Azure Monitor para redes.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Saúde de recursos e métricas
No exemplo seguinte, cada azulejo representa um tipo de recurso. O azulejo apresenta o número de ocorrências desse tipo de recurso implantado em todas as subscrições selecionadas. Também apresenta o estado de saúde do recurso. Neste exemplo, existem 105 ligações ER e VPN implantadas. 103 são saudáveis, e 2 estão indisponíveis.

![Screenshot que mostra saúde de recursos e métricas no Azure Monitor for Networks.](media/network-insights-overview/resource-health.png)

Se selecionar as ligações ER e VPN indisponíveis, verá uma vista métrica: 

![Screenshot que mostra a vista métrica no Azure Monitor para redes.](media/network-insights-overview/metric-view.png)

Pode selecionar qualquer item na vista da grelha. Selecione o ícone na coluna **Saúde** para obter a saúde dos recursos para essa ligação. Selecione o valor na coluna **Alerta** para ir à página de alertas e métricas para a ligação. 

### <a name="alerts"></a>Alertas
A caixa **de alerta** no lado direito da página fornece uma visão de todos os alertas gerados para os recursos selecionados em todas as subscrições. Selecione as contagens de alerta para ir a uma página de alertas detalhadas.

### <a name="dependency-view"></a>Vista de dependência
A vista de dependência ajuda-o a visualizar como um recurso é configurado. A vista de dependência está atualmente disponível para Azure Application Gateway, Azure Virtual WAN e Azure Load Balancer. Por exemplo, para o Gateway de Aplicações, pode aceder à vista de dependência selecionando o nome do recurso Application Gateway na vista da grelha de métricas. Pode fazer o mesmo para o Virtual WAN e o Balancer de Carga.

![Sreenshot que mostra a vista do Gateway de Aplicação no Monitor Azure para redes.](media/network-insights-overview/application-gateway.png)

A visão de dependência do Application Gateway proporciona uma visão simplificada de como os IPs front-end estão ligados aos ouvintes, regras e backend pool. As linhas de ligação são codificadas a cores e fornecem detalhes adicionais baseados na saúde da piscina de backend. A vista também fornece uma visão detalhada das métricas e métricas do Gateway de Aplicação para todos os pools de backend relacionados, como conjunto de escala de máquina virtual e instâncias VM.

[![Screenshot que mostra vista de dependência no Azure Monitor para redes.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

O gráfico de dependência proporciona fácil navegação às definições de configuração. Clique com o direito numa piscina de backend para aceder a outras informações. Por exemplo, se o pool de backend for um VM, pode aceder diretamente à resolução de problemas da VM Insights e da ligação Azure Network Watcher para identificar problemas de conectividade:

![Screenshot que mostra o menu de visualização de dependência no Azure Monitor for Networks.](media/network-insights-overview/dependency-view-menu.png)

A barra de pesquisa e filtro na vista de dependência proporciona uma maneira fácil de pesquisar através do gráfico. Por exemplo, se pesquisar **por AppGWTestRule** no exemplo anterior, a vista reduzir-se-á a todos os nós ligados através do AppGWTestRule:

![Screenshot que mostra um exemplo de uma pesquisa no Azure Monitor for Networks.](media/network-insights-overview/search-example.png)

Vários filtros ajudam-no a reduzir para um caminho e estado específicos. Por exemplo, selecione **apenas Insalubre** da lista **de estado da saúde** para mostrar todas as bordas para as quais o estado não é saudável.

Selecione **Ver métricas detalhadas** para abrir um livro pré-configurado que fornece métricas detalhadas para o gateway de aplicação, todos os recursos de backend pool e IPs front-end. 

## <a name="connectivity"></a><a name="connectivity"></a>Conectividade

O separador **Conectividade** proporciona uma forma fácil de visualizar todos os testes configurados através do [Monitor de Ligação](../../network-watcher/connection-monitor-overview.md) e do Monitor de Conexão (clássico) para o conjunto de subscrições selecionado.

![Screenshot que mostra o separador conectividade no Monitor Azure para redes.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Os testes são agrupados por **telhas De Origem** **e Destinos** e apresentam o estado de alcance de cada teste. As definições alcançáveis proporcionam fácil acesso às configurações para os seus critérios de alcance, com base em verificações falhadas (%) e RTT (ms). Depois de definir os valores, o estado de cada teste atualiza com base nos critérios de seleção.

[![Screenshot que mostra testes de conectividade no Azure Monitor para redes.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

Pode selecionar qualquer fonte ou azulejo de destino para abrir uma vista métrica:

[![Screenshot que mostra métricas de conectividade no Azure Monitor para redes.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


Pode selecionar qualquer item na vista da grelha. Selecione o ícone na coluna **Reachability** para ir à página do portal Connection Monitor e ver a topologia hop-by-hop e a conectividade que afeta os problemas identificados. Selecione o valor na coluna **Alerta** para ir a alertas. Selecione os gráficos nas colunas **Checks Failed Percent** e **Round-Trip (ms)** para ir à página de métricas para o monitor de ligação selecionado.

A caixa **de alerta** no lado direito da página fornece uma visão de todos os alertas gerados para os testes de conectividade configurados em todas as subscrições. Selecione as contagens de alerta para ir a uma página de alertas detalhadas.

## <a name="traffic"></a><a name="traffic"></a>Trânsito
O separador **Traffic** fornece acesso a todos os NSGs configurados para [registos de fluxo NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) e [Traffic Analytics](../../network-watcher/traffic-analytics.md) para o conjunto selecionado de subscrições, agrupados por localização. A funcionalidade de pesquisa fornecida neste separador permite identificar os NSGs configurados para o endereço IP pesquisado. Pode pesquisar qualquer endereço IP no seu ambiente. A vista regional em azulejos apresentará todos os NSGs juntamente com os registos de fluxo NSG e o estado de configuração do Traffic Analytics.

[![Screenshot que mostra o separador traffic em Azure Monitor for Networks.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Se selecionar qualquer azulejo da região, aparece uma vista de grelha. A grelha fornece registos de fluxo NSG e Traffic Analytics numa vista fácil de ler e configurar:  

[![Screenshot que mostra a vista da região de tráfego em Azure Monitor for Networks.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

Pode selecionar qualquer item na vista da grelha. Selecione o ícone na coluna **Estado de Configuração do Fluxolog** para editar o registo de fluxo NSG e a configuração de Traffic Analytics. Selecione o valor na coluna **Alerta** para ir aos alertas de tráfego configurados para o NSG selecionado. Da mesma forma, pode ir à vista Traffic Analytics selecionando o espaço de **trabalho traffic Analytics**.  

A caixa **de alerta** no lado direito da página fornece uma visão de todos os alertas baseados no espaço de trabalho traffic Analytics em todas as subscrições. Selecione as contagens de alerta para ir a uma página de alertas detalhadas.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Conjunto de ferramentas de diagnóstico
O Diagnostic Toolkit fornece acesso a todas as funcionalidades de diagnóstico disponíveis para a resolução de problemas da rede. Pode utilizar esta lista de drop-down para aceder a funcionalidades como [captura de pacotes,](../../network-watcher/network-watcher-packet-capture-overview.md) [resolução de problemas VPN,](../../network-watcher/network-watcher-troubleshoot-overview.md) [resolução de problemas de ligação,](../../network-watcher/network-watcher-connectivity-overview.md) [próximo salto](../../network-watcher/network-watcher-next-hop-overview.md)e [verificação do fluxo IP:](../../network-watcher/network-watcher-ip-flow-verify-overview.md)

![Screenshot que mostra o separador De Diagnóstico Toolkit.](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Resolução de problemas 

Para obter orientações gerais sobre a resolução de problemas, consulte o artigo dedicado baseado em insights baseados em [livros.](troubleshoot-workbooks.md)

Esta secção irá ajudá-lo a diagnosticar e resolver problemas comuns que poderá encontrar quando utilizar o Azure Monitor for Networks. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Como resolvo problemas de desempenho ou falhas?

Para saber sobre a resolução de problemas relacionados com a rede que identificar com o Azure Monitor for Networks, consulte a documentação de resolução de problemas para o recurso avariado. 

Aqui estão algumas ligações para artigos de resolução de problemas para serviços frequentemente utilizados. Para obter mais artigos de resolução de problemas sobre estes serviços, consulte os outros artigos na secção de resolução de problemas da tabela de conteúdos para o serviço.
* [Rede Virtual do Azure](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Gateway de Aplicação do Azure](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Balanceador de Carga do Azure](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Por que não vejo os recursos para todas as assinaturas que selecionei?

O Network Insights pode mostrar recursos para apenas cinco subscrições de cada vez. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-network-insights"></a>Como faço alterações ou adiciono visualizações a Insights de Rede?

Para escoar alterações, **selecione Editar Modo** para modificar o livro. Em seguida, pode guardar as suas alterações como um novo livro que está ligado a um grupo de subscrição e recursos designado.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Qual é o grão de tempo depois de eu fixar qualquer parte dos livros?

O Network Insights utiliza o grão de tempo **Automático,** pelo que o grão de tempo baseia-se na gama de tempo selecionada.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte de um livro está presa?

O intervalo de tempo depende das definições do painel de instrumentos.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-network-insights"></a>E se eu quiser ver outros dados ou fazer as minhas próprias visualizações? Como posso fazer alterações no Network Insights?

Pode editar o livro de trabalho que vê em qualquer painel lateral ou métrica detalhada utilizando o modo de edição. Em seguida, pode guardar as suas alterações como um novo livro.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a monitorização da rede: [O que é O Observador de Redes Azure?](../../network-watcher/network-watcher-monitoring-overview.md)
- Conheça os cenários que os livros de trabalho são projetados para suportar, como criar relatórios e personalizar relatórios existentes, e muito mais: [Criar relatórios interativos com livros de trabalho do Azure Monitor](../platform/workbooks-overview.md)