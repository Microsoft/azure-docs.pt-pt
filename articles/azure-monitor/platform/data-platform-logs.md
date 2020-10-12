---
title: Registos no Monitor Azure Microsoft Docs
description: Descreve registos no Azure Monitor, que são usados para análise avançada de dados de monitorização.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032787"
---
# <a name="azure-monitor-logs-overview"></a>Visão geral dos registos do monitor Azure
Azure Monitor Logs é uma característica do Azure Monitor que recolhe e organiza dados de registo de várias fontes e o disponibiliza para análise usando uma linguagem de consulta sofisticada. Os dados de diferentes fontes podem ser consolidados num único espaço de trabalho e analisados em conjunto para executar tais tarefas e análise de tendências, alerta e visualização.

## <a name="relationship-to-azure-monitor-metrics"></a>Relação com as Métricas do Monitor Azure
O Azure Monitor Metrics armazena dados numéricos numa base de dados de séries temporizadas, o que torna estes dados mais leves do que os Registos do Monitor Azure e capazes de suportar cenários em tempo real que os tornam particularmente úteis para alertar e detetar rapidamente problemas. As métricas só podem armazenar dados numéricos numa determinada estrutura, enquanto os Logs podem armazenar uma variedade de diferentes tipos de dados cada um com a sua própria estrutura. Também pode realizar análises complexas em dados de Registos utilizando consultas de registo que não podem ser usadas para análise de dados de Métricas.

Os dados numéricos são frequentemente enviados de fontes de dados para Registos, além de Métricas. Embora exista um custo adicional para a recolha e retenção destes dados em Logs, permite-lhe incluir dados métricos em consultas de registo e analisá-los com os seus outros dados de monitorização.

## <a name="relationship-to-azure-data-explorer"></a>Relação com Azure Data Explorer
Os Registos do Monitor Azure baseiam-se no Azure Data Explorer. Um espaço de trabalho Log Analytics é aproximadamente o equivalente a uma base de dados no Azure Data Explorer, as tabelas são estruturadas da mesma forma, e ambas usam a mesma linguagem de consulta Kusto (KQL). A experiência de usar o Log Analytics para trabalhar com consultas do Azure Monitor no portal Azure é semelhante à experiência que utiliza o Azure Data Explorer Web UI. Pode [inclusivamente incluir dados de um espaço de trabalho log analytics numa consulta do Azure Data Explorer.](/azure/data-explorer/query-monitor-data) 


## <a name="structure-of-data"></a>Estrutura dos dados
Os dados recolhidos pelo Azure Monitor Logs são armazenados num [espaço de trabalho do Log Analytics](./design-logs-deployment.md) que contém várias tabelas que armazenam dados de uma determinada fonte. O espaço de trabalho define a localização geográfica dos dados, os direitos de acesso que definem os utilizadores que podem aceder aos dados, e as definições de configuração, como o nível de preços e a retenção de dados. Pode utilizar um único espaço de trabalho para todos os seus dados de monitorização ou criar vários espaços de trabalho dependendo dos seus requisitos. Consulte [a implementação de Registos do Monitor Azure](design-logs-deployment.md) em considerações para criar múltiplos espaços de trabalho.

Cada espaço de trabalho contém várias tabelas que são organizadas em colunas separadas com várias linhas de dados. Cada tabela é definida por um conjunto único de colunas que são partilhadas pelas linhas de dados fornecidas pela fonte de dados. 

[![Estrutura de registos de monitores Azure](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Os dados de registo do Application Insights também são armazenados em Registos monitores Azure, mas é armazenado diferente dependendo da configuração da sua aplicação. Para uma aplicação baseada no espaço de trabalho, os dados são armazenados num espaço de trabalho do Log Analytics num conjunto padrão de tabelas para conter dados como pedidos de aplicações, exceções e visualizações de página. Várias aplicações podem usar o mesmo espaço de trabalho. Para uma aplicação clássica, os dados não são armazenados num espaço de trabalho log analytics. Utiliza a mesma linguagem de consulta e cria e executa consultas utilizando a mesma ferramenta Log Analytics no portal Azure. Os dados para aplicações clássicas são armazenados separadamente uns dos outros. A sua estrutura geral é a mesma que as aplicações baseadas no espaço de trabalho, embora os nomes da tabela e das colunas sejam diferentes. Consulte [as alterações de recursos baseadas no Workspace](../app/apm-tables.md) para uma comparação detalhada dos dois.


> [!NOTE]
> Ainda fornecemos compatibilidade total para trás para as suas consultas clássicas de recursos, livros de trabalho e alertas baseados em registos dentro da experiência Application Insights. Para consultar/ver contra a [nova estrutura/esquema de mesa baseada no espaço de trabalho,](../app/apm-tables.md) tem primeiro de navegar para o seu espaço de trabalho Log Analytics. Durante a pré-visualização, a seleção de **Registos** a partir dos painéis de Insights de Aplicação irá dar-lhe acesso à experiência clássica de consulta de Insights de Aplicação. Consulte [a margem de consulta](../log-query/scope.md) para mais detalhes.


[![Estrutura de registos de monitores Azure para Insights de Aplicações](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Registar consultas
Os dados são obtidos a partir de um espaço de trabalho do Log Analytics utilizando uma consulta de [registo](../log-query/log-query-overview.md) que é um pedido apenas de leitura para processar dados e obter resultados de devolução. As consultas de registo são escritas em [Língua de Consulta de Kusto (KQL),](/azure/data-explorer/kusto/query/)que é a língua de consulta utilizada pelo Azure Data Explorer. Use o Log Analytics, que é uma ferramenta no portal Azure para editar e executar consultas de registo e analisar interativamente os seus resultados. Em seguida, pode utilizar as consultas que cria para suportar outras funcionalidades no Azure Monitor, tais como alertas de consulta de registos e livros de trabalho.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Fontes de dados para registos do Monitor Azure
O Azure Monitor recolhe dados de registo de uma variedade de fontes, incluindo recursos no Azure Monitor e agentes que executam em máquinas virtuais. Ver [O que é monitorizado pelo Azure Monitor?](../monitor-reference.md)



## <a name="next-steps"></a>Passos seguintes

- Saiba mais [sobre consultas de registo](../log-query/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log analytics.
- Saiba mais [sobre as métricas no Azure Monitor](data-platform-metrics.md).
- Conheça os [dados de monitorização disponíveis](data-sources.md) para diferentes recursos em Azure.

