---
title: Registos no Monitor Azure Microsoft Docs
description: Descreve registos no Azure Monitor que são utilizados para análise avançada de dados de monitorização.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: dae72454cd9c0b3cb7370873619595840b770ed3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83799984"
---
# <a name="logs-in-azure-monitor"></a>Registos no Azure Monitor

> [!NOTE]
> Todos os dados recolhidos pelo Azure Monitor enquadram-se num dos dois tipos fundamentais, Métricas e Registos. Este artigo descreve Logs. Consulte [as Métricas em Azure Monitor](data-platform-metrics.md) para obter uma descrição detalhada das métricas e para [monitorizar os dados recolhidos pelo Azure Monitor](data-platform.md) para uma comparação das duas.

Os registos no Azure Monitor são especialmente úteis para a realização de análises complexas em todos os dados de várias fontes. Este artigo descreve como os Registos são estruturados no Azure Monitor, o que pode fazer com os dados, e identifica diferentes fontes de dados que armazenam dados em Registos.

> [!NOTE]
> É importante distinguir entre registos do Monitor Azure e fontes de dados de registo em Azure. Por exemplo, os eventos de nível de subscrição em Azure são escritos para um registo de [atividade](platform-logs-overview.md) que você pode ver a partir do menu Azure Monitor. A maioria dos recursos escreverá informações operacionais para um [registo de recursos](platform-logs-overview.md) que pode encaminhar para diferentes locais. O Azure Monitor Logs é uma plataforma de dados de registo que recolhe registos de atividade e registos de recursos, juntamente com outros dados de monitorização para fornecer uma análise profunda em todo o seu conjunto de recursos.

## <a name="what-are-azure-monitor-logs"></a>O que são registos do Monitor Azure?

Os registos no Azure Monitor contêm diferentes tipos de dados organizados em registos com diferentes conjuntos de propriedades para cada tipo. Os registos podem conter valores numéricos como as Métricas do Monitor Azure, mas normalmente contêm dados de texto com descrições detalhadas. Diferem ainda dos dados métricos, na medida em que variam na sua estrutura e muitas vezes não são recolhidos a intervalos regulares. A telemetria, como eventos e vestígios, são armazenadas Registos do Monitor Azure, além dos dados de desempenho, para que tudo possa ser combinado para análise.

Um tipo comum de entrada de log é um evento, que é recolhido esporadicamente. Os eventos são criados por uma aplicação ou serviço e normalmente incluem informações suficientes para fornecer contexto completo por si mesmos. Por exemplo, um evento pode indicar que um determinado recurso foi criado ou modificado, um novo hospedeiro começou em resposta ao aumento do tráfego, ou foi detetado um erro numa aplicação.

 Como o formato dos dados pode variar, as aplicações podem criar registos personalizados utilizando a estrutura de que necessitam. Os dados métricos podem mesmo ser armazenados em Logs para os combinar com outros dados de monitorização para tendências e outras análises de dados.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que pode fazer com os registos do Monitor Azure?
A tabela que se segue lista as diferentes formas de utilização de Logs no Azure Monitor.


|  |  |
|:---|:---|
| Analisar | Utilize [o Log Analytics](../log-query/get-started-portal.md) no portal Azure para escrever consultas de [registo](../log-query/log-query-overview.md) e analisar interativamente dados de registo utilizando o poderoso motor de análise do Data Explorer.<br>Utilize a [consola de análise de Insights de Aplicação](../app/analytics.md) no portal Azure para escrever consultas de registo e analisar interativamente dados de registo a partir de Insights de Aplicação. |
| Visualizar | Resultados da consulta de pinos renderizados como tabelas ou gráficos para um [painel de instrumentos Azure](../../azure-portal/azure-portal-dashboards.md).<br>Crie um [livro para](../platform/workbooks-overview.md) combinar com vários conjuntos de dados num relatório interativo. <br>Exporte os resultados de uma consulta ao [Power BI](powerbi.md) para utilizar diferentes visualizações e partilhar com utilizadores fora do Azure.<br>Exporte os resultados de uma consulta à [Grafana](grafana-plugin.md) para alavancar o seu dashboarding e combinar com outras fontes de dados.|
| Alerta | Configure uma [regra de alerta de registo](alerts-log.md) que envia uma notificação ou toma [medidas automatizadas](action-groups.md) quando os resultados da consulta correspondem a um resultado específico.<br>Configure uma [regra de alerta métrico](alerts-metric-logs.md) em certos registos de dados extraídos como métricas. |
| Recuperar | Consulta de registo de acesso resulta de uma linha de comando utilizando [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>A consulta de registo de acesso resulta de uma linha de comando utilizando [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>A consulta de registo de acesso resulta de uma aplicação personalizada utilizando [a API REST](https://dev.loganalytics.io/). |
| Exportar | Construa um fluxo de trabalho para recuperar dados de registo e copiá-los para um local externo usando [As Aplicações Lógicas.](~/articles/logic-apps/index.yml) |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Como são estruturados os dados nos Registos do Monitor Azure?
Os dados recolhidos pelo Azure Monitor Logs são armazenados num [espaço de trabalho log analytics](../platform/design-logs-deployment.md). Cada espaço de trabalho contém várias tabelas que armazenam dados de uma determinada fonte. Enquanto todas as tabelas partilham [algumas propriedades comuns,](log-standard-properties.md)cada uma tem um conjunto único de propriedades dependendo do tipo de dados que armazena. Um novo espaço de trabalho terá um conjunto padrão de tabelas, e mais mesas serão adicionadas por diferentes soluções de monitorização e outros serviços que escrevem para o espaço de trabalho.

Os dados de registo do Application Insights utilizam o mesmo motor Log Analytics que os espaços de trabalho, mas são armazenados separadamente para cada aplicação monitorizada. Cada aplicação tem um conjunto padrão de tabelas para conter dados como pedidos de aplicação, exceções e vistas de página.

As consultas de registo utilizarão dados de um espaço de trabalho do Log Analytics ou de uma aplicação Application Insights. Pode utilizar uma [consulta de recursos cruzados](../log-query/cross-workspace-query.md) para analisar dados de aplicações juntamente com outros dados de registo ou para criar consultas, incluindo múltiplos espaços de trabalho ou aplicações.

![Áreas de trabalho](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Registar consultas
Os dados em Azure Monitor Logs são recuperados usando uma [consulta de log](../log-query/log-query-overview.md) escrita com o [idioma de consulta Kusto](../log-query/get-started-queries.md), que permite recuperar, consolidar e analisar rapidamente os dados recolhidos. Utilize [o Log Analytics](../log-query/portals.md) para escrever e testar consultas de registo no portal Azure. Permite-lhe trabalhar com resultados interativamente ou fixá-los a um dashboard para vê-los com outras visualizações.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Abrir [o Registo Analytics a partir de Insights de Aplicação](../app/analytics.md) para analisar os dados de Insights de Aplicação.

![Aplicações Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

Também pode obter dados de registo utilizando a [API do Log Analytics](https://dev.loganalytics.io/documentation/overview) e a [API de Insights de Aplicação](https://dev.applicationinsights.io/documentation/overview)REST .


## <a name="sources-of-azure-monitor-logs"></a>Fontes de Registos monitores Azure
O Azure Monitor pode recolher dados de registo de uma variedade de fontes, tanto dentro do Azure como a partir de recursos no local. As tabelas que se seguem listam as diferentes fontes de dados disponíveis de diferentes recursos que escrevem dados para registos do Monitor Azure. Cada um tem um link para detalhes sobre qualquer configuração necessária.

### <a name="azure-tenant-and-subscription"></a>Inquilino e assinatura Azure

| Dados | Descrição |
|:---|:---|
| Registos de auditoria do Azure Ative Directory | Configurado através das definições de Diagnóstico para cada diretório. Consulte [registos AD integrados com registos do Monitor Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Registos de atividade | Armazenado separadamente por padrão e pode ser usado para alertas em tempo real. Instalar solução de Análise de registo de atividade para escrever no log analytics. Consulte [recolher e analisar registos de atividades do Azure no Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Recursos do Azure

| Dados | Descrição |
|:---|:---|
| Diagnóstico de recursos | Configure as definições de Diagnóstico para escrever para dados de diagnóstico, incluindo métricas para um espaço de trabalho Log Analytics. Consulte [os registos de recursos stream Azure para registar analíticos.](resource-logs-collect-workspace.md) |
| Soluções de monitorização | As soluções de monitorização escrevem dados que recolhem para o seu espaço de trabalho Log Analytics. Consulte [os detalhes da recolha de dados para soluções de gestão em Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Consulte [as soluções de monitorização no Azure Monitor](../insights/solutions.md) para obter mais informações sobre a instalação e utilização de soluções. |
| Métricas | Envie métricas da plataforma para os recursos do Azure Monitor para um espaço de trabalho log Analytics para reter dados de registo por períodos mais longos e realizar análises complexas com outros tipos de dados utilizando a [linguagem de consulta Kusto](/azure/kusto/query/). Consulte [os registos de recursos stream Azure para registar analíticos.](resource-logs-collect-storage.md) |
| Armazenamento de tabelas do Azure | Recolher dados do armazenamento da Azure onde alguns recursos da Azure escrevem dados de monitorização. Consulte [o armazenamento de blob Azure para o armazenamento de mesa IIS e Azure para eventos com Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Máquinas Virtuais

| Dados | Descrição |
|:---|:---|
|  Origens de dados do agente | As fontes de dados recolhidas a partir de agentes do [Windows](agent-windows.md) e [do Linux](../learn/quick-collect-linux-computer.md) incluem eventos, dados de desempenho e registos personalizados. Consulte [as fontes de dados do Agente no Azure Monitor](data-sources.md) para obter uma lista de fontes de dados e detalhes sobre a configuração. |
| Soluções de monitorização | As soluções de monitorização escrevem dados que recolhem dos agentes para o seu espaço de trabalho Log Analytics. Consulte [os detalhes da recolha de dados para soluções de gestão em Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Consulte [as soluções de monitorização no Azure Monitor](../insights/solutions.md) para obter mais informações sobre a instalação e utilização de soluções. |
| System Center Operations Manager | Connect Operations Manager grupo de gestão ao Azure Monitor para recolher dados de eventos e desempenho de agentes no local em registos. Consulte [o Gestor de Operações de Ligação para registar o Analytics](om-agents.md) para obter mais detalhes sobre esta configuração. |


### <a name="applications"></a>Aplicações

| Dados | Descrição |
|:---|:---|
| Pedidos e exceções | Dados detalhados sobre pedidos de aplicações e exceções estão nos _pedidos_, _pageViews_e _tabelas de exceções._ As chamadas para [componentes externos](../app/asp-net-dependencies.md) estão na tabela de _dependências._ |
| Utilização e desempenho | O desempenho da aplicação está disponível nos _pedidos,_ _browserTimings_ e tabelas _PerformanceCounters._ Os [dados para métricas personalizadas](../app/api-custom-events-metrics.md#trackevent) estão na tabela _personalizadaMetrics._|
| Vestígios de dados | Os resultados do [rastreio distribuído](../app/distributed-tracing.md) são armazenados na tabela _de vestígios._ |
| Testes de disponibilidade | Os dados sumários dos [testes de disponibilidade](../app/monitor-web-app-availability.md) são armazenados na tabela _disponibilidadeResults._ Os dados detalhados destes testes encontram-se em armazenamento separado e acedidos a partir de Insights de Aplicação no portal Azure. |

### <a name="insights"></a>Informações

| Dados | Descrição |
|:---|:---|
| Azure Monitor para contentores | Dados de inventário e desempenho recolhidos pelo [Azure Monitor para contentores](../insights/container-insights-overview.md). Consulte [os detalhes da recolha de dados](../insights/container-insights-log-search.md#container-records) do contentor para obter uma lista das tabelas. |
| Azure Monitor para VMs | Mapa e dados de desempenho recolhidos pelo [Azure Monitor para VMs](../insights/vminsights-overview.md). Consulte [como consultar os registos do Azure Monitor para VMs](../insights/vminsights-log-search.md) para obter detalhes sobre a consulta destes dados. |

### <a name="custom"></a>Personalizar 

| Dados | Descrição |
|:---|:---|
| API REST | Escreva dados para um espaço de trabalho Log Analytics de qualquer cliente REST. Consulte [enviar dados de registo para O Monitor de Azure com a API do Colecionador de Dados HTTP](data-collector-api.md) para obter mais informações.
| Aplicação Lógica | Escreva quaisquer dados para um espaço de trabalho Log Analytics a partir de um fluxo de trabalho da Aplicação Lógica com a ação **Azure Log Analytics Data Collector.** |

### <a name="security"></a>Segurança

| Dados | Descrição |
|:---|:---|
| Centro de Segurança do Azure | [O Azure Security Center](/azure/security-center/) armazena dados que recolhe num espaço de trabalho do Log Analytics onde podem ser analisados com outros dados de registo. Consulte [a recolha de dados no Azure Security Center](../../security-center/security-center-enable-data-collection.md) para obter detalhes sobre a configuração do espaço de trabalho. |
| Azure Sentinel | [O Azure Sentinel](/azure/sentinel/) armazena dados de fontes de dados num espaço de trabalho log analytics. Consulte [as fontes de dados do Connect](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a [plataforma de dados do Azure Monitor.](data-platform.md)
- Saiba mais [sobre as métricas no Azure Monitor](data-platform-metrics.md).
- Conheça os [dados de monitorização disponíveis](data-sources.md) para diferentes recursos em Azure.
