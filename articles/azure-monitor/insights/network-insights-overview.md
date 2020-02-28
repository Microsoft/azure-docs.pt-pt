---
title: Monitor Azure para Redes (Pré-visualização)
description: Uma visão geral rápida para o Azure Monitor for Network que proporciona uma visão abrangente da saúde e das métricas para todos os recursos de rede implantados sem qualquer configuração.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654873"
---
# <a name="azure-monitor-for-networks-preview"></a>Monitor Azure para Redes (Pré-visualização)
O Azure Monitor for Network proporciona uma visão abrangente da saúde e das métricas para todos os recursos de rede implantados sem qualquer configuração. A capacidade avançada de pesquisa ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que estão hospedando o seu website simplesmente procurando o nome do site hospedado.

A página De **visão geral** do Monitor Azure para redes fornece uma forma sem esforço de visualizar o inventário dos seus recursos de networking, juntamente com a saúde dos recursos e alertas. Divide-se em quatro áreas funcionais fundamentais:

- Pesquisa e filtragem
- Saúde e Métricas de Recursos
- Alertas 
- Vista de dependência

![Página de visão geral](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Pesquisa e filtragem
A visão de saúde de recursos e alertas pode ser personalizada usando filtros como **Subscrição,** **Grupo de Recursos** e Tipo de **Recursos**. A caixa de pesquisa fornece a capacidade de pesquisar através de propriedades de recursos.

A caixa de pesquisa pode ser usada para procurar recursos e recursos associados. Por exemplo, um IP público está associado a um Gateway de Aplicação. A procura do nome DNS do IPS público identificará tanto o IP público como o Gateway de Aplicação associado.

![Pesquisa](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Saúde de Recursos e Métrica
Cada azulejo representa um tipo de recurso, com o número de instâncias implementadas em todas as subscrições selecionadas juntamente com o estado de saúde dos recursos. No exemplo abaixo, existem 105 Ligações ER e VPN implantadas, 103 são saudáveis e 2 indisponíveis.

![Estado de funcionamento de recursos](media/network-insights-overview/resource-health.png)

Clicando nas duas ligações ER e VPN indisponíveis, lança uma vista métrica. 

![Vista métrica](media/network-insights-overview/metric-view.png)

Pode clicar em cada elemento na vista da grelha. Clique no ícone Saúde para redirecionar para a saúde dos recursos para essa ligação. Clique em Alertas para redirecionar para alertas e página métrica, respectivamente para essa ligação. 

## <a name="alerts"></a>Alertas
A grelha **alertas** à direita fornece uma visão de todos os alertas gerados para os recursos selecionados em todas as subscrições. Clique nas contagens de alerta para navegar na página de alertas detalhados.

## <a name="dependency-view"></a>Vista de dependência
A visão **de Dependência** ajuda a visualizar a configuração do recurso. Atualmente, a visão de dependência é suportada apenas para Application Gateway. A visão de dependência pode ser acedida clicando no nome do recurso Application Gateway a partir da visão da grelha das métricas.

![Visão de Gateway de aplicação](media/network-insights-overview/application-gateway.png)

A visão **de Dependência** para o Application Gateway proporciona uma visão simplificada de como os IPs frontais estão ligados aos ouvintes, regras e pool de backend. As bordas de ligação são codificadas por cores e fornecem detalhes adicionais com base na saúde da piscina de backend. A vista também fornece uma visão detalhada das métricas e métricas do Application Gateway para todas as piscinas de backend relacionadas, tais como casos VMSS e VM.

![Vista de dependência](media/network-insights-overview/dependency-view.png)

O gráfico de dependência permite uma navegação fácil para configurações de configuração. Clique direito numa piscina de backend para aceder a outras funcionalidades. Por exemplo, se o pool de backend for um VM, então você pode aceder diretamente a Problemas de ligação VM Insights e Network Watcher para identificar problemas de conectividade.

![Menu de vista de dependência](media/network-insights-overview/dependency-view-menu.png)

A barra de pesquisa e filtro na vista de dependência fornece uma forma sem esforço de pesquisar através do gráfico. Por exemplo, procurar *o AppGWTestRule* no exemplo abaixo reduzirá a vista gráfica a todos os nós ligados via *AppGWTestRule*. 

![Exemplo de pesquisa](media/network-insights-overview/search-example.png)

Diferentes filtros fornecem ajuda para reduzir para um caminho e estado específicos. Por exemplo, selecione apenas *Não saudável* do Estado de **Saúde** descer para mostrar todas as bordas onde o estado é *insalubre*.

Clique em **Detailed Metric View** para lançar um livro pré-configurado com métricas detalhadas para o gateway da aplicação, todos os recursos de piscina de backend e IPs frontais. 

## <a name="next-steps"></a>Passos seguintes 

- Saiba mais sobre a monitorização da rede no What is [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview)
