---
title: Regista no Monitor do Azure | Documentos da Microsoft
description: Descreve os registos no Azure Monitor, que são utilizados para análise avançada de dados de monitorização.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: ec037b16840afe669ac3934beaa832f850cdcfb0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003688"
---
# <a name="logs-in-azure-monitor"></a>Registos no Azure Monitor

> [!NOTE]
> Todos os dados recolhidos pelo Monitor do Azure se encaixa em um dos dois tipos fundamentais, métricas e registos. Este artigo descreve os registos. Consulte a [métricas no Azure Monitor](data-platform-metrics.md) para uma descrição detalhada de métricas e a [dados de monitorização recolhidos pelo Azure Monitor](data-platform.md) para obter uma comparação dos dois.

Os registos no Azure Monitor são especialmente úteis para a realização de análises em todos os dados de uma variedade de origens. Este artigo descreve como os registos estão estruturadas no Azure Monitor, o que pode fazer com os dados e identifica as origens de dados diferentes que armazenam dados nos registos.

> [!NOTE]
> É importante distinguir entre os registos do Azure Monitor e origens de dados de registo no Azure. Por exemplo, os eventos de nível de subscrição no Azure são escritos para um [registo de atividades](activity-logs-overview.md) que pode ver no menu do Azure Monitor. A maioria dos recursos irão escrever as informações operacionais para um [registo de diagnóstico](diagnostic-logs-overview.md) que podem encaminhar para localizações diferentes. Registos de Monitor do Azure é uma plataforma de dados de registo que recolhe registos de atividades e os registos de diagnóstico juntamente com outros dados de monitorização para fornecer uma análise detalhada em sua todo o conjunto de recursos.

## <a name="what-are-azure-monitor-logs"></a>Quais são os Logs de Monitor do Azure?

Os registos no Azure Monitor contêm diferentes tipos de dados organizados em registos com diferentes conjuntos de propriedades de cada tipo. Registos de podem conter valores numéricos, como as métricas de Monitor do Azure, mas normalmente contêm dados de texto com descrições detalhadas. Eles ainda mais diferem dos dados de métrica em que elas variam em sua estrutura e, muitas vezes, não são recolhidas em intervalos regulares. Telemetria, como eventos e rastreios são armazenados os registos de Monitor do Azure, além de dados de desempenho para que ele pode todos ser combinados para análise.

Um tipo comum de entrada de log é um evento, que é recolhido esporadicamente. Os eventos são criados por uma aplicação ou serviço e, normalmente, incluem informações suficientes para fornecer contexto completo por conta própria. Por exemplo, um evento pode indicar que um recurso em particular foi criado ou modificado, um novo anfitrião iniciado em resposta ao aumento de tráfego, ou foi detetado um erro num aplicativo.

 Como o formato dos dados pode variar, os aplicativos podem criar registos personalizados usando a estrutura que eles precisam. Dados métricos ainda podem ser armazenados nos registos para combiná-los com outros dados de monitorização para fins de tendência e outras análises de dados.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que pode fazer com os registos de Monitor do Azure?
A tabela seguinte lista as diferentes formas que pode utilizar os registos no Azure Monitor.


|  |  |
|:---|:---|
| Analisar | Uso [do Log Analytics](../log-query/get-started-portal.md) no portal do Azure para escrever [consultas de registo](../log-query/log-query-overview.md) e analisar dados de registo utilizando o poderoso mecanismo de análise do Data Explorer interativamente.<br>Utilize o [consola do Application Insights analytics](../app/analytics.md) no portal do Azure para escrever consultas de registo e analisar dados de registo do Application Insights interativamente. |
| Visualizar | Afixar os resultados da consulta compostos como tabelas ou de gráficos para uma [dashboard do Azure](../../azure-portal/azure-portal-dashboards.md).<br>Criar uma [livro](../app/usage-workbooks.md) combinar com vários conjuntos de dados num relatório interativo. <br>Exportar os resultados de uma consulta para [Power BI](powerbi.md) utilizar visualizações diferentes e partilhar com utilizadores fora do Azure.<br>Exportar os resultados de uma consulta para [Grafana](grafana-plugin.md) para tirar partido das respetivas vistas e combine com outras origens de dados.|
| Alerta | Configurar uma [regra de alerta de registo](alerts-log.md) que envia uma notificação ou demora [automatizada ação](action-groups.md) quando os resultados da consulta correspondem um resultado específico.<br>Configurar uma [regra de alerta de métrica](alerts-metric-logs.md) em determinados registos de dados de registo extraídos como métricas. |
| Obter | Aceder os resultados da consulta de registo a partir de uma linha de comandos, utilizando [CLI do Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Aceder os resultados da consulta de registo a partir de uma linha de comandos, utilizando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Os resultados da consulta de registo de acesso de um aplicativo personalizado usando [REST API](https://dev.loganalytics.io/). |
| Exportar | Criar um fluxo de trabalho para recuperar dados de registo e copie-o para uma localização externa usando [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>É como os dados nos registos do Azure Monitor estruturado?
Dados recolhidos pelos registos de Monitor do Azure são armazenados num [área de trabalho do Log Analytics](../platform/manage-access.md). Pode [criar várias áreas de trabalho](manage-access.md#determine-the-number-of-workspaces-you-need) na sua subscrição para gerir diferentes conjuntos de dados de registo. Cada área de trabalho contém várias tabelas que cada armazenar dados de uma origem específica. Embora todas as tabelas compartilham [algumas propriedades comuns](log-standard-properties.md), cada um tem um conjunto exclusivo de propriedades, dependendo do tipo de dados que armazena. Uma nova área de trabalho terão um conjunto padrão de tabelas e mais tabelas serão adicionadas por diferentes soluções de monitorização e a outros serviços que escrever para a área de trabalho.

Dados de registo do Application Insights utilizam o mesmo motor de análise de registo como áreas de trabalho, mas ele é armazenado em separado para cada aplicação monitorizada. Cada aplicativo tem um conjunto padrão de tabelas para armazenar dados, como pedidos de aplicações, exceções e vistas de página.

Consultas de log serão a dados de utilização de uma área de trabalho do Log Analytics ou um aplicativo do Application Insights. Pode utilizar um [consulta entre recursos](../log-query/cross-workspace-query.md) para analisar dados de aplicativos com outros dados de registo ou para criar consultas, incluindo várias áreas de trabalho ou aplicativos.

![Áreas de Trabalho](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Registar consultas
Dados nos registos do Azure Monitor são recuperados usando um [consulta de registo](../log-query/log-query-overview.md) escrito com o [linguagem de consulta de Kusto](../log-query/get-started-queries.md), que permite a rapidamente obter, consolidar e analisar os dados recolhidos. Uso [do Log Analytics](../log-query/portals.md) escrever e testar consultas de registo no portal do Azure. Permite-lhe trabalhar com os resultados interativamente ou afixá-los a um dashboard para visualizá-los com outras visualizações.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Open [Log Analytics do Application Insights](../app/analytics.md) para analisar dados do Application Insights.

![O Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

Também pode obter dados de registo utilizando o [API do Log Analytics](https://dev.loganalytics.io/documentation/overview) e o [API de REST do Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Fontes de registos do Azure Monitor
O Azure Monitor pode recolher dados de registo de uma variedade de origens no Azure e de recursos no local. As tabelas seguintes listam as origens de dados diferentes disponíveis de recursos diferentes que escrever dados nos registos do Azure Monitor. Cada um tem uma ligação para obter detalhes sobre qualquer configuração necessária.

### <a name="azure-tenant-and-subscription"></a>Inquilino do Azure e subscrição

| Dados | Descrição |
|:---|:---|
| Registos de auditoria do Azure Active Directory | Configurado através de definições de diagnóstico para cada diretório. Ver [registos de integrar o Azure AD com os registos do Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Registos de atividade | Armazenados em separado por predefinição e pode ser utilizado para perto de alertas em tempo real. Instale a solução Log Analytics da atividade escrever a área de trabalho do Log Analytics. Ver [recolher e analisar registos de atividades do Azure no Log Analytics](collect-activity-logs.md). |

### <a name="azure-resources"></a>Recursos do Azure

| Dados | Descrição |
|:---|:---|
| Diagnóstico de recursos | Configure definições de diagnóstico para escrever dados de diagnóstico, incluindo métricas numa área de trabalho do Log Analytics. Ver [Stream registos de diagnóstico do Azure para o Log Analytics](diagnostic-logs-stream-log-store.md). |
| Soluções de monitorização | Soluções de monitorização escrever dados em que eles recolher a sua área de trabalho do Log Analytics. Ver [detalhes de recolha de dados para soluções de gestão no Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Ver [monitorizar soluções no Azure Monitor](../insights/solutions.md) para obter detalhes sobre como instalar e utilizar as soluções. |
| Métricas | Enviar métricas de plataforma para os recursos do Azure Monitor para uma área de trabalho do Log Analytics para manter os dados de registo por períodos mais longos e realizar análises complexas com outros tipos de dados com o [linguagem de consulta de Kusto](/azure/kusto/query/). Ver [Stream registos de diagnóstico do Azure para o Log Analytics](diagnostic-logs-stream-log-store.md). |
| Armazenamento de tabelas do Azure | Recolher dados de armazenamento do Azure em que alguns recursos do Azure se escrever dados de monitorização. Ver [armazenamento de Blobs do Azure de utilização para o IIS e o Azure, armazenamento de tabela para eventos com o Log Analytics](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Virtual Machines

| Dados | Descrição |
|:---|:---|
|  Origens de dados do agente | Origens de dados recolhidos a partir [Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) agentes incluem eventos, dados de desempenho e registos personalizados. Ver [origens de dados do agente no Azure Monitor](data-sources.md) para obter uma lista de origens de dados e detalhes sobre a configuração. |
| Soluções de monitorização | Soluções de monitorização gravar dados que recolhemos agentes para sua área de trabalho do Log Analytics. Ver [detalhes de recolha de dados para soluções de gestão no Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Ver [monitorizar soluções no Azure Monitor](../insights/solutions.md) para obter detalhes sobre como instalar e utilizar as soluções. |
| System Center Operations Manager | Ligar o grupo de gestão do Operations Manager para o Azure Monitor para recolher dados de eventos e desempenho de agentes no local para os registos. Ver [ligar o Operations Manager ao Log Analytics](om-agents.md) para obter detalhes sobre esta configuração. |


### <a name="applications"></a>Aplicações

| Dados | Descrição |
|:---|:---|
| Pedidos e exceções | Dados detalhados sobre exceções e pedidos de aplicação estão no _pedidos_, _pageViews_, e _exceções_ tabelas. Chama [componentes externos](../app/asp-net-dependencies.md) estão no _dependências_ tabela. |
| Utilização e desempenho | Desempenho da aplicação está disponível na _pedidos_, _browserTimings_ e _performanceCounters_ tabelas. Dados para [métricas personalizadas](../app/api-custom-events-metrics.md#trackevent) está no _customMetrics_ tabela.|
| Dados de rastreio | Resulta da [distribuído rastreio](../app/distributed-tracing.md) são armazenadas no _rastreios_ tabela. |
| Testes de disponibilidade | Dados de resumo da [testes de disponibilidade](../app/monitor-web-app-availability.md) é armazenado na _availabilityResults_ tabela. Dados detalhados desses testes estão em armazenamento separado e acessado a partir do Application Insights no portal do Azure. |

### <a name="insights"></a>Informações

| Dados | Descrição |
|:---|:---|
| Azure Monitor para contentores | Dados de inventário e de desempenho recolhidos pelo [Monitor do Azure para contentores](../insights/container-insights-overview.md). Ver [detalhes de recolha de dados de contentor](../insights/container-insights-log-search.md#container-records) para obter uma lista das tabelas. |
| Azure Monitor para VMs | Dados de mapa e de desempenho recolhidos pelo [do Azure Monitor para VMs](../insights/vminsights-overview.md). Ver [como consultar os registos do Azure Monitor para VMs](../insights/vminsights-log-search.md) para obter detalhes sobre como consultar estes dados. |

### <a name="custom"></a>Personalizado 

| Dados | Descrição |
|:---|:---|
| API REST | Escreva dados para uma área de trabalho do Log Analytics a partir de qualquer cliente REST. Ver [enviar dados de registo para o Azure Monitor com a API HTTP Data Collector](data-collector-api.md) para obter detalhes.
| Aplicação Lógica | Escrever todos os dados para uma área de trabalho do Log Analytics a partir de um fluxo de trabalho de aplicação lógica com o **do Azure Log Analytics Data Collector** ação. |

### <a name="security"></a>Segurança

| Dados | Descrição |
|:---|:---|
| Centro de Segurança do Azure | [Centro de segurança do Azure](/azure/security-center/) armazena os dados que recolhe numa área de trabalho do Log Analytics onde podem ser analisados com outros dados de registo. Ver [recolha de dados no Centro de segurança do Azure](../../security-center/security-center-enable-data-collection.md) para obter detalhes sobre a configuração de área de trabalho. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) armazena os dados de origens de dados numa área de trabalho do Log Analytics. Ver [ligar a origens de dados](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [plataforma de dados do Azure Monitor](data-platform.md).
- Saiba mais sobre [métricas no Azure Monitor](data-platform-metrics.md).
- Saiba mais sobre o [monitorização dos dados disponíveis](data-sources.md) diferentes recursos no Azure.
