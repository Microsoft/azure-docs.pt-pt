---
title: Monitor Azure para redes (pré-visualização)
description: Uma visão geral rápida para o Azure Monitor for Network que proporciona uma visão abrangente da saúde e métricas para todos os recursos de rede implantados sem qualquer configuração.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 5f076f477c36f96d1807ce7071720225a6df8e03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803811"
---
# <a name="azure-monitor-for-networks-preview"></a>Monitor Azure para redes (pré-visualização)
O Azure Monitor for Network fornece uma visão abrangente da [saúde](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) e [métricas](../platform/metrics-supported.md) para todos os recursos de rede implantados sem qualquer configuração.  Também fornece acesso a todas as capacidades de monitorização da rede, tais como [Connection Monitor,](../../network-watcher/connection-monitor-preview.md) [registo de fluxos para grupos de segurança de rede (NSGs),](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) [Traffic Analytics](../../network-watcher/traffic-analytics.md)e outras funcionalidades de [diagnóstico de](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) rede.

O Monitor Azure para redes está estruturado em torno dos seguintes componentes-chave de monitorização:
- [Saúde da rede e métricas](#networkhealth)
- [Conetividade](#connectivity)
- [Trânsito](#traffic)
- [Conjunto de ferramentas de diagnóstico](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Saúde da Rede e métricas

A página Azure Monitor for Networks **Overview** fornece uma forma sem esforço de visualizar o inventário dos seus recursos de rede, juntamente com a saúde dos recursos e alertas. Divide-se em quatro áreas funcionais chave - Pesquisa e filtragem, Saúde de Recursos e Métricas, Alertas. e Vista de Dependência

![Página geral](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Pesquisa e filtragem
A visão de saúde e alertas de recursos pode ser personalizada usando filtros como **Subscrição,** **Grupo de Recursos** e Tipo de **Recurso.** A caixa de pesquisa fornece a capacidade de pesquisar através de propriedades de recursos.

A caixa de pesquisa pode ser usada para procurar recursos e recursos associados. Por exemplo, um IP público está associado a um Gateway de aplicação. Procurar o nome DNS do IPs Público identificará tanto o IP público como o Gateway de Aplicação associado.

![Monitor Azure para redes - Pesquisa](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Saúde e Métrica de Recursos
Cada azulejo representa um tipo de recurso, com o número de casos implantados em todas as subscrições selecionadas juntamente com o estado de saúde dos recursos. No exemplo abaixo, existem 45 Ligações ER e VPN implantadas, 44 são saudáveis e 1 indisponíveis.

![Monitor Azure para redes - Saúde de recursos](media/network-insights-overview/resource-health.png)

Clicando nas ligações Indisponível ER e VPN, lança uma visão métrica. 

![Monitor Azure para redes - vista métrica](media/network-insights-overview/metric-view.png)

Pode clicar em cada elemento na vista da grelha. Clique no ícone Saúde para redirecionar para a saúde dos recursos para essa ligação. Clique em Alertas para redirecionar para a página de alertas e métricas, respectivamente, para essa ligação. 

### <a name="alerts"></a>Alertas
A grelha **de Alertas** à direita fornece uma visão de todos os alertas gerados para os recursos selecionados em todas as subscrições. Clique nas contagens de alerta para navegar para a página de alertas detalhados.

### <a name="dependency-view"></a>Vista de dependência
A visão **de Dependência** ajuda a visualizar como o recurso é configurado. Atualmente a vista de dependência é agora suportada para Application Gateway, Virtual WAN e Load Balancer. Por exemplo, no caso do Gateway de Aplicação, a vista de dependência pode ser acedida clicando no nome do recurso Application Gateway a partir da vista da grelha de métricas. Isto também se aplica a WAN virtual e balanceador de carga.

![Monitor Azure para redes - Visualização do Gateway de aplicações](media/network-insights-overview/application-gateway.png)

A visão **de Dependência** para o Gateway de Aplicação proporciona uma visão simplificada de como os IPs front-end estão ligados aos ouvintes, regras e piscina de backend. As bordas de ligação são codificadas por cores e fornecem detalhes adicionais baseados na saúde da piscina de backend. A vista também fornece uma visão detalhada das métricas e métricas do Gateway de Aplicação para todos os pools de backend relacionados, tais como conjunto de escala de máquina virtual e instâncias VM.

![Monitor Azure para redes - Vista de dependência](media/network-insights-overview/dependency-view.png)

O gráfico de dependência permite uma fácil navegação às definições de configuração. Clique com o direito numa piscina de backend para aceder a outras funcionalidades. Por exemplo, se o pool de backend for um VM, pode aceder diretamente a VM Insights e à resolução de problemas de ligação do Observador de Rede para identificar problemas de conectividade.

![Monitor Azure para redes - Menu de visualização de dependência](media/network-insights-overview/dependency-view-menu.png)

A barra de pesquisa e filtro na vista de dependência fornece uma forma sem esforço de pesquisar através do gráfico. Por exemplo, a procura de *AppGWTestRule* no exemplo abaixo reduzirá a visão gráfica a todos os nós ligados através *do AppGWTestRule*.

![Azure Monitor para Redes - Exemplo de pesquisa](media/network-insights-overview/search-example.png)

Filtros diferentes ajudam a reduzir-se a um caminho e estado específicos. Por exemplo, selecione *apenas Insalubre* do Estado de **Saúde** descer para mostrar todas as bordas onde o estado não é *saudável*.

Clique em **Métrica Detalhada para** lançar um livro pré-configurado com métricas detalhadas para o Gateway de aplicação, todos os recursos de backend pool e IPs frontais. 

## <a name="connectivity"></a><a name="connectivity"></a>Conectividade

O separador **Conectividade** proporciona uma forma sem esforço de visualizar todos os testes configurados utilizando o Monitor de Ligação e o Monitor de [Conexão (Preview)](../../network-watcher/connection-monitor-preview.md) para o conjunto de subscrições selecionado.

![Separador de conectividade no Monitor Azure para redes](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Os testes são agrupados por telhas De Origem e Destinos e apresentam o estado de alcance de cada teste. As definições alcançáveis proporcionam um fácil acesso para configurar os seus critérios de alcance com base em Verificações falhadas (%) e RTT (ms). Uma vez definidos os valores, o estado de cada teste é atualizado com base nos critérios de seleção.

![Testes de conectividade no Azure Monitor para Redes](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Clicar em qualquer fonte ou azulejo de destino lança uma visão métrica.

![Métricas de conectividade no Monitor Azure para redes](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


Pode clicar em cada elemento na vista da grelha. Clique no ícone **Reachability** para redirecionar para a página do portal **Connection Monitor** para ver o hop by hop topology e problemas de impacto de conectividade identificados. Clique em **Alertas** para redirecionar para alertas e **verifica o tempo de por cento/ida e volta falhado** para redirecionar para a página de métricas para o monitor de conexão selecionado.

A grelha **de alertas**   à direita fornece uma visão de todos os alertas gerados para os testes de conectividade configurados em todas as subscrições. Clique nas contagens de alerta para navegar para a página de alertas detalhados.

## <a name="traffic"></a><a name="traffic"></a>Trânsito
O separador de tráfego fornece acesso a todos os NSGs configurados para [registos NSG Flow](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) e [Traffic Analytics](../../network-watcher/traffic-analytics.md) para o conjunto selecionado de subscrições e agrupados por locais. A funcionalidade de pesquisa fornecida neste separador permite identificar os NSGs configurados para o endereço IP pesquisado. Pode pesquisar qualquer endereço IP no seu ambiente e a vista regional em azulejo apresentará todos os NSGs juntamente com os registos NSG Flow e o estado de configuração da análise de tráfego.

![Vista de tráfego no Monitor Azure para redes](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

Clicar em qualquer azulejo da região lança uma vista de grelha que proporciona fácil visualização e configuração de registos de fluxo NSG e Traffic Analytics.  

![Vista da região de tráfego em Azure Monitor for Networks](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

Pode clicar em cada elemento na vista da grelha. Clique no estado de configuração para editar o registo de fluxo NSG e a configuração de Traffic Analytics. Clique em alertas para redirecionar para os Alertas de Tráfego configurados para o NSG selecionado. Da mesma forma, pode navegar para a vista Traffic Analytics clicando no espaço de trabalho.  

A grelha **de alertas**   à direita fornece uma visão de todos os alertas baseados no espaço de trabalho Traffic Analytics em todas as subscrições. Clique nas contagens de alerta para navegar para a página de alertas detalhados.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Kit de ferramentas de diagnóstico
O Diagnostic Toolkit fornece acesso a todas as funcionalidades de Diagnóstico disponíveis para resolução de problemas na rede. A partir deste dropdown você pode ter acesso a funcionalidades como [Captura de Pacotes,](../../network-watcher/network-watcher-packet-capture-overview.md) [Resolução de Problemas VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), [Resolução de Problemas de Conexão](../../network-watcher/network-watcher-connectivity-overview.md), [Next Hop](../../network-watcher/network-watcher-next-hop-overview.md) e IP Flow [Check](../../network-watcher/network-watcher-ip-flow-verify-overview.md).

![Separador de kit de ferramentas de diagnóstico](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Resolução de problemas 

Para obter orientações gerais sobre a resolução de problemas, consulte o artigo dedicado de resolução de [problemas](troubleshoot-workbooks.md)baseado em livros de trabalho .

Esta secção irá ajudá-lo com o diagnóstico e resolução de problemas de algumas das questões comuns que poderá encontrar ao utilizar o Azure Monitor for Networks. Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-issues-or-failures"></a>Resolução de problemas de desempenho ou falhas

Para ajudar a resolver problemas relacionados com a rede que identificar com o Azure Monitor for Networks, consulte a documentação de resolução de problemas do recurso avariado. Os links de resolução de problemas para serviços de alto utilização estão listados abaixo.
* Rede Virtual (VNET)
* Gateway de Aplicação
* Gateway de VPN
* ExpressRoute 
* Load balancer 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>Por que não vejo os recursos de todas as assinaturas que selecionei

O Network Insights só pode mostrar recursos a partir de 5 subscrições de cada vez. 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>Quero fazer alterações ou adicionar visualizações adicionais à Network Insights, como faço isso

Para escoar alterações, selecione o "Modo de Edição" para modificar o livro, então pode guardar o seu trabalho como um novo livro que está ligado a um grupo de subscrição e recursos designado.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual é o grão-tempo uma vez que pinemos qualquer parte dos livros de trabalho

Utilizamos o grão de tempo "Auto", pelo que depende do intervalo de tempo selecionado.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte do livro está presa

O intervalo de tempo dependerá das definições do painel de instrumentos.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>E se eu quiser ver outros dados ou fazer as minhas próprias visualizações? Como posso fazer alterações no Network Insights

Pode editar o livro que vê em qualquer painel lateral e visão métrica detalhada, através da utilização do modo de edição, e depois guardar o seu trabalho como um novo livro que terá todas as suas novas alterações.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a monitorização da rede no [Que é o Observador da Rede Azure?](../../network-watcher/network-watcher-monitoring-overview.md)
- Saiba os cenários que os livros de trabalho são projetados para apoiar, como autorizar novos e personalizar relatórios existentes, e mais através da revisão [criar relatórios interativos com os livros de trabalho do Azure Monitor.](../platform/workbooks-overview.md)
