---
title: Monitorar recursos do Azure com o Azure Monitor | Microsoft Docs
description: Descreve como coletar e analisar dados de monitoramento de recursos no Azure usando o Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 0748047581945d513300d929c2d34d20099bf4d6
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529699"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorando recursos do Azure com o Azure Monitor
Quando você tem aplicativos críticos e processos de negócios que dependem de recursos do Azure, você deseja monitorar esses recursos para sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitoramento gerados pelos recursos do Azure e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

> [!IMPORTANT]
> Este artigo se aplica a todos os serviços no Azure que usam Azure Monitor. Os recursos de computação, incluindo VMs e serviço de aplicativo, geram os mesmos dados de monitoramento descritos aqui, mas também têm um sistema operacional convidado que também pode gerar logs e métricas. Consulte a documentação de monitoramento para esses serviços para obter detalhes sobre como coletar e analisar esses dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
Azure Monitor é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e locais. A [plataforma de dados Azure monitor](../platform/data-platform.md) coleta dados em [logs](../platform/data-platform-logs.md) e [métricas](../platform/data-platform-metrics.md) onde eles podem ser analisados juntos usando um conjunto completo de ferramentas de monitoramento, conforme descrito nas seções a seguir.

- [O que você pode fazer com Azure Monitor métricas?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [O que você pode fazer com os logs de Azure Monitor?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Assim que você criar um recurso do Azure, o Azure Monitor será habilitado e começará a coletar métricas e logs de atividade que você pode [Exibir e analisar no portal do Azure](#monitoring-in-the-azure-portal). Com algumas configurações, você pode coletar dados de monitoramento adicionais e habilitar recursos adicionais. Veja [dados de monitoramento](#monitoring-data) abaixo para obter detalhes sobre quaisquer requisitos de configuração.


## <a name="costs-associated-with-monitoring"></a>Custos associados ao monitoramento
Não há nenhum custo para analisar dados de monitoramento que são coletados por padrão. Tais atributos incluem:

- Coletando métricas de plataforma e analisando-as com o Metrics Explorer.
- Coletando o log de atividades e analisando-o no portal do Azure.
- Criando uma regra de alerta do log de atividades.

Não há custos de Azure Monitor para coletar e exportar logs e métricas, mas pode haver custos relacionados associados ao destino:

- Custos associados à ingestão de dados e retenção ao coletar logs e métricas no espaço de trabalho Log Analytics. Consulte [preços de Azure monitor para log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Os custos associados ao armazenamento de dados ao coletar logs e métricas para uma conta de armazenamento do Azure. Consulte [preços do armazenamento do Azure para armazenamento de BLOBs](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Os custos associados ao streaming do hub de eventos ao encaminhar os logs e as métricas para os hubs de eventos do Azure. Consulte [preços dos hubs de eventos do Azure](https://azure.microsoft.com/pricing/details/event-hubs/).

Pode haver Azure Monitor custos associados ao seguinte. Consulte [Azure monitor preços](https://azure.microsoft.com/pricing/details/monitor/):

- Executando uma consulta de log.
- Criando uma regra de alerta de consulta de métrica ou log.
- Enviando uma notificação de qualquer regra de alerta.
- Acessando métricas por meio da API.

## <a name="monitoring-data"></a>Monitorizar dados
Os recursos no Azure geram [logs](../platform/data-platform-logs.md) e [métricas](../platform/data-platform-metrics.md) mostrados no diagrama a seguir. Consulte a documentação de cada serviço do Azure para obter os dados específicos que eles geram e quaisquer soluções ou informações adicionais que eles fornecem.

![Visão geral](media/monitor-azure-resource/logs-metrics.png)



- [Métricas de plataforma](../platform/data-platform-metrics.md) -valores numéricos que são coletados automaticamente em intervalos regulares e descrevem algum aspecto de um recurso em um determinado momento. 
- [Logs de recursos](../platform/resource-logs-overview.md) -fornecem informações sobre as operações que foram executadas em um recurso do Azure (o plano de dados), por exemplo, obtendo um segredo de um Key Vault ou fazendo uma solicitação para um banco de dado. O conteúdo e a estrutura de logs de recursos variam de acordo com o serviço e o tipo de recurso do Azure.
- [Log de atividades](../platform/activity-logs-overview.md) – fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (o plano de gerenciamento), por exemplo, criando um novo recurso ou iniciando uma máquina virtual. Estas são informações sobre o que, quem e quando para qualquer operação de gravação (PUT, POST, DELETE) realizada nos recursos em sua assinatura.


## <a name="configuration-requirements"></a>Requisitos de configuração

### <a name="configure-monitoring"></a>Configurar a monitorização
Alguns dados de monitoramento são coletados automaticamente, mas talvez seja necessário executar algumas configurações dependendo de suas necessidades. Consulte as informações abaixo para obter informações específicas para cada tipo de dados de monitoramento.

- [Métricas de plataforma](../platform/data-platform-metrics.md) – as métricas de plataforma são coletadas automaticamente em [métricas Azure monitor](../platform/data-platform-metrics.md) sem configuração necessária. Crie uma configuração de diagnóstico para enviar entradas para Azure Monitor logs ou para encaminhá-las fora do Azure.
- [Logs de recursos](../platform/resource-logs-overview.md) – os logs de recursos são gerados automaticamente pelos recursos do Azure, mas não são coletados sem uma configuração de diagnóstico.  Crie uma configuração de diagnóstico para enviar entradas para Azure Monitor logs ou para encaminhá-las fora do Azure.
- [Log de atividades](../platform/activity-logs-overview.md) – o log de atividades é coletado automaticamente sem nenhuma configuração necessária e pode ser exibido no portal do Azure. Crie uma configuração de diagnóstico para copiá-las para Azure Monitor logs ou para encaminhá-las fora do Azure.

### <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
A coleta de dados em logs de Azure Monitor requer um espaço de trabalho Log Analytics. Você pode começar a monitorar seu serviço rapidamente criando um novo espaço de trabalho, mas pode haver um valor no uso de um espaço de trabalho que está coletando dados de outros serviços. Consulte [criar um log Analytics espaço de trabalho no portal do Azure](../learn/quick-create-workspace.md) para obter detalhes sobre como criar um espaço de trabalho e [criar sua implantação de logs de Azure monitor](../platform/design-logs-deployment.md) para ajudar a determinar o melhor design de espaço de trabalho para seus requisitos. Se você usar um espaço de trabalho existente em sua organização, você precisará de permissões apropriadas, conforme descrito em [gerenciar o acesso a dados de log e espaços de trabalho no Azure monitor](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Definições de diagnóstico
As configurações de diagnóstico definem onde os logs de recursos e as métricas para um recurso específico devem ser enviados. Os possíveis destinos são:

- [Log Analytics espaço de trabalho](../platform/resource-logs-collect-workspace.md) que permite analisar dados com outros dados de monitoramento coletados por Azure monitor usando consultas de log poderosas e também para aproveitar outros recursos de Azure monitor, como alertas de log e visualizações. 
- [Hubs de eventos](../platform/resource-logs-stream-event-hubs.md) para transmitir dados para sistemas externos, como Siems de terceiros e outras soluções do log Analytics. 
- [Conta de armazenamento do Azure](../platform/resource-logs-collect-storage.md) que é útil para auditoria, análise estática ou backup.

Siga o procedimento em [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../platform/diagnostic-settings.md) para criar e gerenciar configurações de diagnóstico por meio do portal do Azure. Consulte [criar configuração de diagnóstico no Azure usando um modelo do Resource Manager](../platform/diagnostic-settings-template.md) para defini-las em um modelo e habilitar o monitoramento completo de um recurso quando ele é criado.


## <a name="monitoring-in-the-azure-portal"></a>Monitoramento no portal do Azure
 Você pode acessar os dados de monitoramento para a maioria dos recursos do Azure no menu do recurso na portal do Azure. Isso dará acesso a dados de um único recurso usando ferramentas de Azure Monitor padrão. Alguns serviços do Azure fornecerão opções diferentes, portanto, você deve consultar a documentação desse serviço para obter informações adicionais. Use o menu **Azure monitor** para analisar dados de todos os recursos monitorados. 

### <a name="overview"></a>Visão geral
Muitos serviços incluirão dados de monitoramento em sua página de **visão geral** como uma visão rápida de sua operação. Normalmente, isso será baseado em um subconjunto de métricas de plataforma armazenadas em métricas de Azure Monitor. Outras opções de monitoramento normalmente estarão disponíveis em uma seção **monitoramento** dos serviços. .

![Página de visão geral](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Informações e soluções 
Alguns serviços fornecerão ferramentas além dos recursos padrão do Azure Monitor. As [informações](../insights/insights-overview.md) fornecem uma experiência de monitoramento personalizada criada com base na plataforma de dados Azure monitor e nos recursos padrão. As [soluções](../insights/solutions.md) fornecem lógica de monitoramento predefinida com base em Logs de Azure monitor. 

Se um serviço tiver um insight Azure Monitor, você poderá acessá-lo do **monitoramento** no menu de cada recurso. Acesse todas as informações e soluções no menu **Azure monitor** .

![Insights](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Métricas
Analise as métricas no portal do Azure usando o [Metrics Explorer](../platform/metrics-getting-started.md) , que está disponível no item de menu **métricas** para a maioria dos serviços. Essa ferramenta permite que você trabalhe com métricas individuais ou combine várias para identificar correlações e tendências. 

- Consulte [introdução ao Azure Metrics Explorer](../platform/metrics-getting-started.md) para obter noções básicas sobre como usar o Metrics Explorer.
- Consulte [recursos avançados do Azure Metrics Explorer](../platform/metrics-charts.md) para recursos avançados do Metrics Explorer, como o uso de várias métricas e a aplicação de filtros e divisão.

![Métricas](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Registo de atividades 
Exiba entradas no log de atividades no portal do Azure com o filtro inicial definido como o recurso atual. Copie o log de atividades em um espaço de trabalho Log Analytics para acessá-lo para usá-lo em consultas de log e pastas de trabalho. 

- Consulte [Exibir e recuperar eventos do log de atividades do Azure](../platform/activity-log-view.md) para obter detalhes sobre como exibir o log de atividades e recuperar entradas usando uma variedade de métodos.
- Consulte a documentação do serviço do Azure para obter os eventos específicos que são registrados.

![Registo de Atividade](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
Os logs de Azure Monitor consolida os logs e as métricas de vários serviços e outras fontes de dados para análise com uma poderosa ferramenta de consulta. Conforme descrito acima, crie uma configuração de diagnóstico para coletar métricas de plataforma, log de atividades e logs de recursos em um espaço de trabalho Log Analytics no Azure Monitor.

[Log Analytics](../log-query/get-started-portal.md) permite que você trabalhe com [consultas de log](../log-query/log-query-overview.md), que é um recurso poderoso de Azure monitor que permite executar uma análise avançada dos dados de log usando uma linguagem de consulta totalmente em destaque. Abra Log Analytics de **logs** no menu **monitoramento** de um recurso do Azure para trabalhar com consultas de log usando o recurso como o [escopo da consulta](../log-query/scope.md#query-scope). Isso permite que você analise dados em várias tabelas apenas para esse recurso. Use **logs** do menu Azure monitor para acessar os logs de todos os recursos. 

- Consulte Introdução [às consultas de log no Azure monitor](../log-query/get-started-queries.md) para obter um tutorial sobre como usar a linguagem de consulta usada para gravar consultas de log.
- Consulte [coletar logs de recursos do Azure no espaço de trabalho log Analytics no Azure monitor](../platform/resource-logs-collect-workspace.md) para obter informações sobre como os logs de recursos são coletados nos logs de Azure monitor e detalhes sobre como acessá-los em uma consulta.
- Consulte [modo de coleta](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) para obter uma explicação de como os dados de log de recursos são estruturados em Logs de Azure monitor.
- Consulte a documentação de cada serviço do Azure para obter detalhes sobre sua tabela em logs de Azure Monitor.

![Registos](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitoramento da linha de comando
Você pode acessar os dados de monitoramento coletados do recurso de uma linha de comando ou incluir em um script usando [Azure PowerShell](/powershell/azure/) ou a [interface de linha de comando do Azure](/cli/azure/). 

- Consulte [referência de métricas da CLI](/cli/azure/monitor/metrics) para acessar dados de métrica da CLI.
- Consulte [referência de log Analytics da CLI](/cli/azure/ext/log-analytics/monitor/log-analytics) para acessar dados de Logs de Azure monitor usando uma consulta de log da CLI.
- Consulte [Azure PowerShell referência de métricas](/powershell/module/azurerm.insights/get-azurermmetric) para acessar dados de métrica do Azure PowerShell.
- Consulte [Azure PowerShell referência de consulta de log](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) para acessar dados de Logs de Azure monitor usando uma consulta de log do Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitoramento da API REST
Inclua dados de monitoramento coletados de seu recurso em um aplicativo personalizado usando uma API REST.

- Consulte [instruções da API REST de monitoramento do Azure](../platform/rest-api-walkthrough.md) para obter detalhes sobre como acessar métricas da API rest do Azure monitor.
- Consulte [API REST do Azure log Analytics](https://dev.loganalytics.io/) para obter informações sobre como acessar dados de Logs de Azure monitor usando uma consulta de Log do Azure PowerShell.

## <a name="alerts"></a>Alertas
Os [alertas](../platform/alerts-overview.md) o notificam proativamente e potencialmente podem agir quando condições importantes são encontradas nos dados de monitoramento. Você cria uma regra de alerta que define um destino para o alerta, as condições para se criar um alerta e as ações a serem executadas em resposta.

Tipos diferentes de dados de monitoramento são usados para diferentes tipos de regras de alerta.

- [Alerta do log de atividades](../platform/alerts-activity-log.md) – crie um alerta quando uma entrada for criada no log de atividades que corresponde a critérios específicos. Isso permite que você seja notificado por exemplo quando um determinado tipo de recurso é criado ou se uma alteração de configuração falhar.
- [Alerta de métrica](../platform/alerts-metric.md) – crie um alerta quando um valor de métrica exceder um limite específico. Os alertas de métricas são mais responsivos do que outros alertas e podem ser resolvidos automaticamente quando o problema é corrigido.
- [Alerta de consulta de log](../platform/alerts-log.md) – execute uma consulta de log em intervalos regulares e crie um alerta se uma determinada condição for encontrada. Isso permite que você execute análises complexas em vários conjuntos de dados e.

Use **alertas** do menu de um recurso para exibir alertas e gerenciar regras de alerta para esse recurso. Somente alertas de log de atividades e alertas de métrica usam recursos individuais do Azure como um destino. Alertas de consulta de log usam um espaço de trabalho Log Analytics como um destino e se baseiam em uma consulta que pode acessar quaisquer logs armazenados nesse espaço de trabalho. Use o menu Azure Monitor para exibir e gerenciar alertas para todos os recursos e para gerenciar as regras de alerta de consulta de log.

- Consulte os artigos para os diferentes tipos de alertas acima para obter detalhes sobre como criar regras de alerta.
- Consulte [criar e gerenciar grupos de ações no portal do Azure](../platform/action-groups.md) para obter detalhes sobre como criar um grupo de ações que permite gerenciar respostas a alertas.



## <a name="next-steps"></a>Passos seguintes

* Consulte [serviços, esquemas e categorias com suporte para logs de recursos do Azure](../platform/diagnostic-logs-schema.md) para obter detalhes dos logs de recursos para diferentes serviços do Azure.  
