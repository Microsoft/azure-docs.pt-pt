---
title: Monitor Azure para redes (pré-visualização)
description: Uma visão geral rápida para o Azure Monitor for Network que proporciona uma visão abrangente da saúde e métricas para todos os recursos de rede implantados sem qualquer configuração.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: ccc5578944dc6eea9a62360045272896d78e2fac
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128680"
---
# <a name="azure-monitor-for-networks-preview"></a>Monitor Azure para redes (pré-visualização)
O Azure Monitor for Network fornece uma visão abrangente da saúde e métricas para todos os recursos de rede implantados sem qualquer configuração. A capacidade avançada de pesquisa ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que estão hospedando o seu site simplesmente procurando o nome do site hospedado.

A página Azure Monitor for Networks **Overview** fornece uma forma sem esforço de visualizar o inventário dos seus recursos de rede, juntamente com a saúde dos recursos e alertas. Divide-se em quatro áreas funcionais chave:

- Pesquisa e filtragem
- Saúde e Métricas de Recursos
- Alertas 
- Vista de dependência

![Página geral](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Pesquisa e filtragem
A visão de saúde e alertas de recursos pode ser personalizada usando filtros como **Subscrição,** **Grupo de Recursos** e Tipo de **Recurso.** A caixa de pesquisa fornece a capacidade de pesquisar através de propriedades de recursos.

A caixa de pesquisa pode ser usada para procurar recursos e recursos associados. Por exemplo, um IP público está associado a um Gateway de aplicação. Procurar o nome DNS do IPs Público identificará tanto o IP público como o Gateway de Aplicação associado.

![Pesquisar](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Saúde e Métrica de Recursos
Cada azulejo representa um tipo de recurso, com o número de casos implantados em todas as subscrições selecionadas juntamente com o estado de saúde dos recursos. No exemplo abaixo, existem 105 Ligações ER e VPN implantadas, 103 são saudáveis e 2 indisponíveis.

![Estado de funcionamento de recursos](media/network-insights-overview/resource-health.png)

Clicando nas duas ligações ER e VPN indisponíveis, lança uma visão métrica. 

![Vista métrica](media/network-insights-overview/metric-view.png)

Pode clicar em cada elemento na vista da grelha. Clique no ícone Saúde para redirecionar para a saúde dos recursos para essa ligação. Clique em Alertas para redirecionar para a página de alertas e métricas, respectivamente, para essa ligação. 

## <a name="alerts"></a>Alertas
A grelha **de Alertas** à direita fornece uma visão de todos os alertas gerados para os recursos selecionados em todas as subscrições. Clique nas contagens de alerta para navegar para a página de alertas detalhados.

## <a name="dependency-view"></a>Vista de dependência
A visão **de Dependência** ajuda a visualizar como o recurso é configurado. Atualmente a vista de dependência é agora suportada para Application Gateway, Virtual WAN e Load Balancer. Por exemplo, no caso do Gateway de Aplicação, a vista de dependência pode ser acedida clicando no nome do recurso Application Gateway a partir da vista da grelha de métricas. Isto também se aplica a WAN virtual e balanceador de carga. 

![Vista gateway de aplicação](media/network-insights-overview/application-gateway.png)

A visão **de Dependência** para o Gateway de Aplicação proporciona uma visão simplificada de como os IPs front-end estão ligados aos ouvintes, regras e piscina de backend. As bordas de ligação são codificadas por cores e fornecem detalhes adicionais baseados na saúde da piscina de backend. A vista também fornece uma visão detalhada das métricas e métricas do Gateway de Aplicação para todos os pools de backend relacionados, tais como vmss e vm instâncias.

![Vista de dependência](media/network-insights-overview/dependency-view.png)

O gráfico de dependência permite uma fácil navegação às definições de configuração. Clique direito em uma piscina de backend para aceder a outra funcionalidade. Por exemplo, se o pool de backend for um VM, então pode aceder diretamente a VM Insights e à resolução de problemas de ligação do Observador de Rede para identificar problemas de conectividade.

![Menu de visualização de dependência](media/network-insights-overview/dependency-view-menu.png)

A barra de pesquisa e filtro na vista de dependência fornece uma forma sem esforço de pesquisar através do gráfico. Por exemplo, a procura de *AppGWTestRule* no exemplo abaixo reduzirá a visão gráfica a todos os nós ligados através *do AppGWTestRule*. 

![Exemplo de pesquisa](media/network-insights-overview/search-example.png)

Filtros diferentes ajudam a reduzir-se a um caminho e estado específicos. Por exemplo, selecione *apenas Insalubre* do Estado de **Saúde** descer para mostrar todas as bordas onde o estado não é *saudável*.

Clique em **Métrica Detalhada para** lançar um livro pré-configurado com métricas detalhadas para o gateway de aplicação, todos os recursos de backend pool e IPs frontais. 

## <a name="next-steps"></a>Passos seguintes 

- Saiba mais sobre a monitorização da rede no Que é [o Observador da Rede Azure?](../../network-watcher/network-watcher-monitoring-overview.md)
