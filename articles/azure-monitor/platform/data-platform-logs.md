---
title: Registos do Azure Monitor
description: Descreve registos de monitores Azure que são utilizados para análise avançada de dados de monitorização.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 69fda41249ced8518e430af8305c0cb10822b214
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559070"
---
# <a name="azure-monitor-logs-overview"></a>Descrição geral dos Registos do Azure Monitor
Azure Monitor Logs é uma característica do Azure Monitor que recolhe e organiza dados de registo e desempenho a partir de [recursos monitorizados.](../monitor-reference.md) Dados de diferentes fontes, como [registos](platform-logs-overview.md) de plataformas a partir de serviços Azure, dados de registo e desempenho de [agentes de máquinas virtuais,](agents-overview.md)e dados de utilização e desempenho de aplicações podem ser consolidados num único espaço de trabalho para que possam ser [analisados](../app/app-insights-overview.md) em conjunto usando uma linguagem de consulta sofisticada que é capaz de analisar rapidamente milhões de registos. Pode realizar uma consulta simples que apenas recupera um conjunto específico de registos ou realiza análises de dados sofisticadas para identificar padrões críticos nos seus dados de monitorização. Trabalhe com consultas de registo e seus resultados interativamente usando Log Analytics, use-os em regras de alerta para ser notificado proativamente de problemas, ou visualizar os seus resultados em um livro ou painel de instrumentos.

> [!NOTE]
> Azure Monitor Logs é metade da plataforma de dados que suporta o Azure Monitor. O outro é [a Azure Monitor Metrics](data-platform-metrics.md) que armazena dados numéricos numa base de dados de séries temporizadas. Isto torna estes dados mais leves do que os dados em Registos monitores do Azure e capazes de suportar cenários em tempo real tornando-os particularmente úteis para alertar e detetar rapidamente problemas. As métricas só podem armazenar dados numéricos numa determinada estrutura, enquanto os Logs podem armazenar uma variedade de diferentes tipos de dados cada um com a sua própria estrutura. Também pode realizar análises complexas em dados de Registos utilizando consultas de registo que não podem ser usadas para análise de dados de Métricas.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que pode fazer com os registos do Monitor Azure?
A tabela a seguir descreve algumas das diferentes formas de utilização de Registos no Monitor Azure:

|  |  |
|:---|:---|
| **Analisar** | Use [o Log Analytics](../log-query/log-analytics-tutorial.md) no portal Azure para escrever consultas de [registo](../log-query/log-query-overview.md) e analise interativamente dados de registo usando um poderoso motor de análise |
| **Alerta** | Configure uma [regra de alerta de registo](alerts-log.md) que envia uma notificação ou toma [medidas automatizadas](action-groups.md) quando os resultados da consulta correspondem a um resultado específico. |
| **Visualizar** | Resultados da consulta de pinos renderizados como tabelas ou gráficos para um [painel de instrumentos Azure](../../azure-portal/azure-portal-dashboards.md).<br>Crie um [livro para](./workbooks-overview.md) combinar com vários conjuntos de dados num relatório interativo. <br>Exporte os resultados de uma consulta ao [Power BI](powerbi.md) para utilizar diferentes visualizações e partilhar com utilizadores fora do Azure.<br>Exporte os resultados de uma consulta à [Grafana](grafana-plugin.md) para alavancar o seu dashboarding e combinar com outras fontes de dados.|
| **Informações** | Informações [de](../monitor-reference.md#insights-and-core-solutions) suporte que proporcionam uma experiência de monitorização personalizada para aplicações e serviços específicos.  |
| **Recuperar** | Consulta de registo de acesso resulta de uma linha de comando utilizando [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>A consulta de registo de acesso resulta de uma linha de comando utilizando [cmdlets PowerShell](/powershell/module/az.operationalinsights).<br>A consulta de registo de acesso resulta de uma aplicação personalizada utilizando [a API REST](https://dev.loganalytics.io/). |
| **Exportar** | Configure [a exportação automatizada de dados de registo](logs-data-export.md) para a conta de armazenamento Azure ou para a Azure Event Hubs.<br>Construa um fluxo de trabalho para recuperar dados de registo e copiá-los para um local externo usando [As Aplicações Lógicas.](logicapp-flow-connector.md) |

![Visão geral dos registos](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Recolha de dados
Uma vez criado um espaço de trabalho Log Analytics, tem de configurar diferentes fontes para enviar os seus dados. Nenhum dado é recolhido automaticamente. Esta configuração será diferente dependendo da fonte de dados. Por exemplo, [crie configurações de diagnóstico](diagnostic-settings.md) para enviar registos de recursos dos recursos Azure para o espaço de trabalho. [Ativar o Azure Monitor para VMs](../insights/vminsights-enable-overview.md) para recolher dados de máquinas virtuais. Configure [fontes de dados no espaço de trabalho](data-sources.md) para recolher eventos adicionais e dados de desempenho.

- Ver [O que é monitorizado pelo Azure Monitor?](../monitor-reference.md)


## <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics
Os dados recolhidos pelo Azure Monitor Logs são [armazenados](./design-logs-deployment.md)num ou mais espaços de trabalho do Log Analytics . O espaço de trabalho define a localização geográfica dos dados, os direitos de acesso que definem os utilizadores que podem aceder aos dados, e as definições de configuração, como o nível de preços e a retenção de dados.  

Tem de criar pelo menos um espaço de trabalho para utilizar os Registos do Monitor Azure. Um único espaço de trabalho pode ser suficiente para todos os seus dados de monitorização, ou pode optar por criar múltiplos espaços de trabalho dependendo dos seus requisitos. Por exemplo, pode ter um espaço de trabalho para os seus dados de produção e outro para testes. 

- Consulte [criar um espaço de trabalho log Analytics no portal Azure](../learn/quick-create-workspace.md) para criar um novo espaço de trabalho.
- Consulte [a implementação de Registos do Monitor Azure](design-logs-deployment.md) em considerações para criar múltiplos espaços de trabalho.

## <a name="data-structure"></a>Estrutura de dados
As consultas de registo recuperam os seus dados de um espaço de trabalho do Log Analytics. Cada espaço de trabalho contém várias tabelas que são organizadas em colunas separadas com várias linhas de dados. Cada tabela é definida por um conjunto único de colunas que são partilhadas pelas linhas de dados fornecidas pela fonte de dados. 

[![Estrutura de registos de monitores Azure](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Os dados de registo do Application Insights também são armazenados em Registos monitores Azure, mas é armazenado diferente dependendo da configuração da sua aplicação. Para uma aplicação baseada no espaço de trabalho, os dados são armazenados num espaço de trabalho do Log Analytics num conjunto padrão de tabelas para conter dados como pedidos de aplicações, exceções e visualizações de página. Várias aplicações podem usar o mesmo espaço de trabalho. Para uma aplicação clássica, os dados não são armazenados num espaço de trabalho log analytics. Utiliza a mesma linguagem de consulta e cria e executa consultas utilizando a mesma ferramenta Log Analytics no portal Azure. Os dados para aplicações clássicas são armazenados separadamente uns dos outros. A sua estrutura geral é a mesma que as aplicações baseadas no espaço de trabalho, embora os nomes da tabela e das colunas sejam diferentes. Consulte [alterações de recursos baseadas no Workspace](../app/apm-tables.md) para uma comparação detalhada do esquema para aplicações clássicas e baseadas em espaço de trabalho.


> [!NOTE]
> Ainda fornecemos compatibilidade total para trás para as suas consultas clássicas de recursos, livros de trabalho e alertas baseados em registos dentro da experiência Application Insights. Para consultar/ver contra a [nova estrutura/esquema de mesa baseada no espaço de trabalho,](../app/apm-tables.md) tem primeiro de navegar para o seu espaço de trabalho Log Analytics. Durante a pré-visualização, a seleção de **Registos** a partir dos painéis de Insights de Aplicação irá dar-lhe acesso à experiência clássica de consulta de Insights de Aplicação. Consulte [a margem de consulta](../log-query/scope.md) para mais detalhes.


[![Estrutura de registos de monitores Azure para Insights de Aplicações](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Registar consultas
Os dados são obtidos a partir de um espaço de trabalho do Log Analytics utilizando uma consulta de registo que é um pedido apenas de leitura para processar dados e obter resultados de devolução. As consultas de registo são escritas em [Língua de Consulta de Kusto (KQL),](/azure/data-explorer/kusto/query/)que é a mesma linguagem de consulta usada pelo Azure Data Explorer. Pode escrever consultas de registo no Log Analytics para analisar interativamente os seus resultados, usá-los em regras de alerta para ser notificado proativamente de problemas, ou incluir os seus resultados em livros ou dashboards. Os insights incluem consultas pré-construídas para apoiar as suas opiniões e livros.

- Consulte [as consultas de Log no Azure Monitor](log-query/../../log-query/log-query-overview.md) para obter uma lista de onde são usadas consultas de registo e referências a tutoriais e outra documentação para começar.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Utilize o Log Analytics, que é uma ferramenta no portal Azure, para editar e executar consultas de registo e analisar interativamente os seus resultados. Em seguida, pode utilizar as consultas que cria para suportar outras funcionalidades no Azure Monitor, tais como alertas de consulta de registos e livros de trabalho. Aceder ao Registo Analytics a partir da opção **Logs** no menu Azure Monitor ou da maioria dos outros serviços no portal Azure.

- Consulte [a visão geral do Log Analytics no Azure Monitor](../log-query/log-analytics-overview.md) para obter uma descrição do Log Analytics. 
- Consulte [o tutorial do Log Analytics](../log-query/log-analytics-tutorial.md) para passear através das funcionalidades do Log Analytics para criar uma consulta simples de registo e analisar os seus resultados.



## <a name="relationship-to-azure-data-explorer"></a>Relação com Azure Data Explorer
Os Registos do Monitor Azure baseiam-se no Azure Data Explorer. Um espaço de trabalho Log Analytics é aproximadamente o equivalente a uma base de dados no Azure Data Explorer, as tabelas são estruturadas da mesma forma, e ambas usam a mesma linguagem de consulta Kusto (KQL). A experiência de usar o Log Analytics para trabalhar com consultas do Azure Monitor no portal Azure é semelhante à experiência que utiliza o Azure Data Explorer Web UI. Pode [inclusivamente incluir dados de um espaço de trabalho log analytics numa consulta do Azure Data Explorer.](/azure/data-explorer/query-monitor-data) 


## <a name="next-steps"></a>Passos seguintes

- Saiba mais [sobre consultas de registo](../log-query/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log analytics.
- Saiba mais [sobre as métricas no Azure Monitor](data-platform-metrics.md).
- Conheça os [dados de monitorização disponíveis](data-sources.md) para diferentes recursos em Azure.
