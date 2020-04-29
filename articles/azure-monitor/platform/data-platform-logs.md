---
title: Registos no Monitor Azure [ Monitor De acesso) Microsoft Docs
description: Descreve os registos no Monitor Azure que são utilizados para uma análise avançada dos dados de monitorização.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 0b288bf6d987b9db682c8d1439879cf6b499f213
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457336"
---
# <a name="logs-in-azure-monitor"></a>Registos no Azure Monitor

> [!NOTE]
> Todos os dados recolhidos pelo Azure Monitor enquadram-se num dos dois tipos fundamentais, Métricas e Registos. Este artigo descreve Registos. Consulte as [Métricas no Monitor Azure](data-platform-metrics.md) para obter uma descrição detalhada das métricas e para [monitorizar os dados recolhidos pelo Azure Monitor](data-platform.md) para uma comparação entre as duas.

Os registos no Monitor Azure são especialmente úteis para realizar análises complexas através de dados de várias fontes. Este artigo descreve como os Registos são estruturados no Monitor Do Azure, o que pode fazer com os dados, e identifica diferentes fontes de dados que armazenam dados em Registos.

> [!NOTE]
> É importante distinguir entre registos do Monitor Azure e fontes de dados de registo em Azure. Por exemplo, os eventos de nível de subscrição em Azure são escritos para um registo de [atividade](platform-logs-overview.md) que você pode ver a partir do menu Do Monitor Azure. A maioria dos recursos irá escrever informações operacionais para um registo de [recursos](platform-logs-overview.md) que você pode encaminhar para diferentes locais. O Azure Monitor Logs é uma plataforma de dados de registo que recolhe registos de atividade sonantes e registos de recursos, juntamente com outros dados de monitorização, para fornecer uma análise profunda em todo o conjunto de recursos.

## <a name="what-are-azure-monitor-logs"></a>O que são registos do Monitor Azure?

Os registos no Monitor Azure contêm diferentes tipos de dados organizados em registos com diferentes conjuntos de propriedades para cada tipo. Os registos podem conter valores numéricos como as Métricas do Monitor de Azure, mas normalmente contêm dados de texto com descrições detalhadas. Diferem ainda dos dados métricos, na medida em que variam na sua estrutura e muitas vezes não são recolhidos a intervalos regulares. A telemetria, como eventos e vestígios, são armazenados registos do Monitor Azure, para além dos dados de desempenho, para que todos possam ser combinados para análise.

Um tipo comum de entrada de log é um evento, que é recolhido esporadicamente. Os eventos são criados por uma aplicação ou serviço e normalmente incluem informações suficientes para fornecer o contexto completo por si só. Por exemplo, um evento pode indicar que um determinado recurso foi criado ou modificado, um novo hospedeiro começou em resposta ao aumento do tráfego, ou foi detetado um erro numa aplicação.

 Como o formato dos dados pode variar, as aplicações podem criar registos personalizados utilizando a estrutura de que necessitam. Os dados métricos podem até ser armazenados em Registos para os combinar com outros dados de monitorização para a tendência e outras análises de dados.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que pode fazer com os Registos do Monitor Azure?
A tabela seguinte lista as diferentes formas de utilizar os Registos no Monitor Azure.


|  |  |
|:---|:---|
| Analisar | Utilize o [Log Analytics](../log-query/get-started-portal.md) no portal Azure para escrever consultas de [registo](../log-query/log-query-overview.md) e analisar interativamente os dados de registo utilizando o poderoso motor de análise do Data Explorer.<br>Utilize a [consola de análise Application Insights](../app/analytics.md) no portal Azure para escrever consultas de registo e analisar interativamente os dados de registo a partir de Insights de Aplicação. |
| Visualizar | Resultados de consulta de pinos renderizados como tabelas ou gráficos para um [painel de instrumentos Azure](../../azure-portal/azure-portal-dashboards.md).<br>Crie um [livro](../app/usage-workbooks.md) para combinar com vários conjuntos de dados num relatório interativo. <br>Exportar os resultados de uma consulta ao [Power BI](powerbi.md) para utilizar diferentes visualizações e partilhar com utilizadores fora do Azure.<br>Exporte os resultados de uma consulta à [Grafana](grafana-plugin.md) para alavancar o seu dashboard e combinar com outras fontes de dados.|
| Alerta | Configure uma regra de alerta de [registo](alerts-log.md) que envie uma notificação ou tome [medidas automatizadas](action-groups.md) quando os resultados da consulta correspondam a um resultado específico.<br>Configure uma [regra de alerta métrico](alerts-metric-logs.md) em certos registos de dados de registo extraídos como métricas. |
| Recuperar | A consulta de registo de acesso resulta de uma linha de comando utilizando [o Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>A consulta de registo de acesso resulta de uma linha de comando utilizando [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>A consulta de registo de acesso resulta de uma aplicação personalizada utilizando [a REST API](https://dev.loganalytics.io/). |
| Exportar | Construa um fluxo de trabalho para recuperar dados de registo e copiá-lo para um local externo usando [aplicações lógicas](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Como é estruturado os dados nos Registos do Monitor Do Azure?
Os dados recolhidos pelo Azure Monitor Logs são armazenados num espaço de [trabalho do Log Analytics](../platform/design-logs-deployment.md). Cada espaço de trabalho contém várias tabelas que cada armazenar dados de uma determinada fonte. Enquanto todas as tabelas partilham [algumas propriedades comuns,](log-standard-properties.md)cada uma tem um conjunto único de propriedades dependendo do tipo de dados que armazena. Um novo espaço de trabalho terá um conjunto padrão de tabelas, e mais tabelas serão adicionadas por diferentes soluções de monitorização e outros serviços que escrevem para o espaço de trabalho.

Os dados de registo da Application Insights utilizam o mesmo motor Log Analytics que os espaços de trabalho, mas são armazenados separadamente para cada aplicação monitorizada. Cada aplicação tem um conjunto padrão de tabelas para guardar dados como pedidos de aplicação, exceções e visualizações de página.

As consultas de registo utilizarão dados de um espaço de trabalho do Log Analytics ou de uma aplicação De insights de aplicação. Pode utilizar uma [consulta de recurso cruzado](../log-query/cross-workspace-query.md) para analisar dados de aplicação juntamente com outros dados de registo ou para criar consultas, incluindo vários espaços de trabalho ou aplicações.

![Áreas de Trabalho](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Registar consultas
Os dados em Registos do Monitor Do Azure são recuperados através de uma consulta de [log](../log-query/log-query-overview.md) escrita com a linguagem de [consulta Kusto](../log-query/get-started-queries.md), que permite recuperar, consolidar e analisar rapidamente os dados recolhidos. Utilize [o Log Analytics](../log-query/portals.md) para escrever e testar consultas de registo no portal Azure. Permite-lhe trabalhar com resultados interativamente ou fixá-los num dashboard para vê-los com outras visualizações.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Abra [o Log Analytics a partir de Insights de Aplicação](../app/analytics.md) para analisar os dados do Application Insights.

![Insights de Aplicação Analytics](media/data-platform-logs/app-insights-analytics.png)

Também pode recuperar dados de registo utilizando a [API](https://dev.loganalytics.io/documentation/overview) log analytics e os Insights de [Aplicação REST API](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Fontes de registos de monitores azure
O Azure Monitor pode recolher dados de registo de uma variedade de fontes dentro do Azure e de recursos no local. As tabelas seguintes listam as diferentes fontes de dados disponíveis a partir de diferentes recursos que escrevem dados para o Azure Monitor Logs. Cada um tem um link para detalhes sobre qualquer configuração necessária.

### <a name="azure-tenant-and-subscription"></a>Inquilino e subscrição azure

| Dados | Descrição |
|:---|:---|
| Registos de auditoria do Diretório Ativo Azure | Configurado através de definições de diagnóstico para cada diretório. Consulte [registos de Anúncios Azure integrados com registos do Monitor Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Registos de atividade | Armazenado si separado por padrão e pode ser usado para alertas em tempo real. Instale a solução analytics de registo de atividade para escrever para log Analytics espaço de trabalho. Consulte recolher e analisar registos de [atividade do Azure no Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Recursos do Azure

| Dados | Descrição |
|:---|:---|
| Diagnóstico de recursos | Configure as definições de diagnóstico para escrever para dados de diagnóstico, incluindo métricas para um espaço de trabalho de Log Analytics. Consulte os registos de [recursos do Stream Azure para registar o Analytics](resource-logs-collect-workspace.md). |
| Soluções de monitorização | As soluções de monitorização escrevem dados que recolhem para o seu espaço de trabalho log Analytics. Consulte detalhes da recolha de dados para soluções de [gestão em Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Consulte [as soluções de monitorização no Monitor Azure](../insights/solutions.md) para obter mais informações sobre a instalação e utilização de soluções. |
| Métricas | Envie métricas de plataforma para os recursos do Azure Monitor para um espaço de trabalho de Log Analytics para reter dados de registo por períodos mais longos e realizar análises complexas com outros tipos de dados usando a [linguagem de consulta Kusto](/azure/kusto/query/). Consulte os registos de [recursos do Stream Azure para registar o Analytics](resource-logs-collect-storage.md). |
| Armazenamento de tabelas do Azure | Recolha dados do armazenamento do Azure onde alguns recursos do Azure escrevem dados de monitorização. Consulte o armazenamento de [blob Azure para armazenamento de mesa IIS e Azure para eventos com Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Virtual Machines

| Dados | Descrição |
|:---|:---|
|  Origens de dados do agente | Fontes de dados recolhidas a partir de agentes [windows](agent-windows.md) e [linux](../learn/quick-collect-linux-computer.md) incluem eventos, dados de desempenho e registos personalizados. Consulte as fontes de dados do [Agente no Monitor Azure](data-sources.md) para obter uma lista de fontes de dados e detalhes sobre a configuração. |
| Soluções de monitorização | As soluções de monitorização escrevem dados que recolhem dos agentes para o seu espaço de trabalho log Analytics. Consulte detalhes da recolha de dados para soluções de [gestão em Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Consulte [as soluções de monitorização no Monitor Azure](../insights/solutions.md) para obter mais informações sobre a instalação e utilização de soluções. |
| System Center Operations Manager | Connect Operations Manager management group to Azure Monitor para recolher dados de eventos e desempenho de agentes no local em registos. Consulte o [Connect Operations Manager para registar o Analytics](om-agents.md) para obter detalhes sobre esta configuração. |


### <a name="applications"></a>Aplicações

| Dados | Descrição |
|:---|:---|
| Pedidos e exceções | Os dados detalhados sobre pedidos de candidatura e exceções estão nos _pedidos,_ _pageViews,_ e tabelas de _exceções._ As chamadas para [componentes externos](../app/asp-net-dependencies.md) estão na tabela de _dependências._ |
| Utilização e desempenho | O desempenho da aplicação está disponível nas _tabelas de pedidos,_ _browserTimings_ e _performanceCounters._ Os dados para [métricas personalizadas](../app/api-custom-events-metrics.md#trackevent) estão na tabela _customMetrics._|
| Dados de rastreio | Os resultados do [rastreio distribuído](../app/distributed-tracing.md) são armazenados na tabela de _vestígios._ |
| Testes de disponibilidade | Os dados sumários dos testes de [disponibilidade](../app/monitor-web-app-availability.md) são armazenados na tabela _de resultados_ de disponibilidade. Os dados detalhados destes testes encontram-se em armazenamento separado e acedidos a partir de Insights de Aplicação no portal Azure. |

### <a name="insights"></a>Informações

| Dados | Descrição |
|:---|:---|
| Azure Monitor para contentores | Dados de inventário e desempenho recolhidos pelo [Azure Monitor para contentores.](../insights/container-insights-overview.md) Consulte [os detalhes da recolha de dados do Recipiente](../insights/container-insights-log-search.md#container-records) para obter uma lista das tabelas. |
| Azure Monitor para VMs | Dados de mapa e desempenho recolhidos pelo [Azure Monitor para VMs](../insights/vminsights-overview.md). Consulte [como consultar os registos do Monitor Azure para obter informações](../insights/vminsights-log-search.md) sobre a consulta destes dados. |

### <a name="custom"></a>Personalizado 

| Dados | Descrição |
|:---|:---|
| API REST | Escreva dados para um espaço de trabalho de Log Analytics de qualquer cliente REST. Consulte enviar dados de [registo para o Monitor Azure com a API](data-collector-api.md) de Coleção de Dados HTTP para obter mais detalhes.
| Aplicação Lógica | Escreva quaisquer dados para um espaço de trabalho de Log Analytics a partir de um fluxo de trabalho de Aplicação Lógica com a ação de Colecionador de **Dados Azure Log Analytics.** |

### <a name="security"></a>Segurança

| Dados | Descrição |
|:---|:---|
| Centro de Segurança do Azure | [O Azure Security Center](/azure/security-center/) armazena dados que recolhe num espaço de trabalho de Log Analytics onde pode ser analisado com outros dados de registo. Consulte a recolha de dados no Centro de [Segurança Azure](../../security-center/security-center-enable-data-collection.md) para obter detalhes sobre a configuração do espaço de trabalho. |
| Azure Sentinel | [O Azure Sentinel](/azure/sentinel/) armazena dados de fontes de dados num espaço de trabalho do Log Analytics. Ver [Ligar fontes](/azure/sentinel/connect-data-sources)de dados .  |


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a plataforma de [dados Azure Monitor.](data-platform.md)
- Conheça [as métricas no Monitor Azure.](data-platform-metrics.md)
- Conheça os [dados de monitorização disponíveis](data-sources.md) para diferentes recursos no Azure.
