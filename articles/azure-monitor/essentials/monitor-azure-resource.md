---
title: Monitorar recursos do Azure com | do Monitor Azure Microsoft Docs
description: Descreve como recolher e analisar dados de monitorização de recursos em Azure usando o Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: af2803a945821a23ff59f196967d792a6593009f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033389"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorizar os recursos do Azure com o Azure Monitor
Quando você tem aplicações críticas e processos de negócio contando com recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pelos recursos da Azure e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar sobre estes dados.

> [!IMPORTANT]
> Este artigo aplica-se a todos os serviços em Azure que utilizam o Azure Monitor. Os recursos compute, incluindo VMs e App Service, geram os mesmos dados de monitorização descritos aqui, mas também têm um sistema operativo de hóspedes que também pode gerar registos e métricas. Consulte a documentação de monitorização destes serviços para obter mais informações sobre como recolher e analisar estes dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O Azure Monitor é um serviço completo de monitorização de stacks em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure, além de recursos em outras nuvens e no local. A [plataforma de dados Azure Monitor](../data-platform.md) recolhe dados em [registos](../logs/data-platform-logs.md) e [métricas](../essentials/data-platform-metrics.md) onde podem ser analisados em conjunto usando um conjunto completo de ferramentas de monitorização. Veja a lista completa de aplicações e serviços que podem ser monitorizados pelo Azure Monitor no [What is monitored by Azure Monitor?](../monitor-reference.md)

Assim que criar um recurso Azure, o Azure Monitor está ativado e começa a recolher métricas e registos de atividade que pode [ver e analisar no portal Azure](#monitoring-in-the-azure-portal). Com alguma configuração, pode recolher dados de monitorização adicionais e ativar funcionalidades adicionais. Consulte [os dados de monitorização](#monitoring-data) abaixo para obter mais informações sobre quaisquer requisitos de configuração.


## <a name="costs-associated-with-monitoring"></a>Custos associados à monitorização
Não há qualquer custo para a análise de dados de monitorização que são recolhidos por padrão. Isto inclui o seguinte:

- Recolher métricas da plataforma e analisá-las com o explorador de métricas.
- Recolher registo de atividade e analisá-lo no portal Azure.
- Criar uma regra de alerta de registo de atividade.

Não existem custos do Azure Monitor para a recolha e exportação de registos e métricas, mas pode haver custos relacionados com o destino:

- Custos associados à ingestão e retenção de dados ao recolher registos e métricas no espaço de trabalho Log Analytics. Consulte os preços do [Monitor Azure para o Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Custos associados ao armazenamento de dados ao recolher registos e métricas para uma conta de armazenamento Azure. Consulte [os preços de armazenamento Azure para armazenamento de bolhas](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Custos associados ao streaming de centros de eventos ao encaminhar registos e métricas para Azure Event Hubs. Ver [preços do Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Pode haver custos do Azure Monitor associados com os seguintes. Ver [preços do Monitor Azure:](https://azure.microsoft.com/pricing/details/monitor/)

- A fazer uma consulta de registo.
- Criar uma regra de alerta de consulta métrica ou de log.
- Envio de uma notificação de qualquer regra de alerta.
- Aceder a métricas através da API.

## <a name="monitoring-data"></a>Monitorizar dados
Os recursos em Azure geram [registos](../logs/data-platform-logs.md) e [métricas mostrados](../essentials/data-platform-metrics.md) no diagrama seguinte. Consulte a documentação de cada serviço Azure para os dados específicos que geram e quaisquer soluções ou insights adicionais que forneçam.

![Descrição Geral](media/monitor-azure-resource/logs-metrics.png)



- [Métricas da plataforma](../essentials/data-platform-metrics.md) - Valores numéricos que são automaticamente recolhidos a intervalos regulares e descrevem algum aspeto de um recurso num determinado momento. 
- [Registos de recursos](./platform-logs-overview.md) - Forneça informações sobre as operações que foram realizadas dentro de um recurso Azure (o plano de dados), por exemplo, obtendo um segredo de um Cofre-Chave ou fazendo um pedido para uma base de dados. O conteúdo e estrutura dos registos de recursos varia consoam em função do serviço Azure e do tipo de recurso.
- [Log de atividade](./platform-logs-overview.md) - Fornece informações sobre as operações em cada recurso Azure na subscrição a partir do exterior (o plano de gestão), por exemplo, criando um novo recurso ou iniciando uma máquina virtual. Esta é uma informação sobre o quê, quem, e quando para quaisquer operações de escrita (PUT, POST, DELETE) assumiu os recursos na sua subscrição.


## <a name="configuration-requirements"></a>Requisitos de configuração

### <a name="configure-monitoring"></a>Configurar a monitorização
Alguns dados de monitorização são recolhidos automaticamente, mas pode ser necessário realizar alguma configuração dependendo dos seus requisitos. Consulte as informações abaixo para obter informações específicas para cada tipo de dados de monitorização.

- [Métricas da plataforma](../essentials/data-platform-metrics.md) - As métricas da plataforma são recolhidas automaticamente em [Métricas do Monitor Azure](../essentials/data-platform-metrics.md) sem necessidade de configuração. Crie uma definição de diagnóstico para enviar entradas para registos do Monitor Azure ou para reencamizá-las para fora do Azure.
- [Registos de recursos](./platform-logs-overview.md) - Os registos de recursos são gerados automaticamente pelos recursos do Azure, mas não recolhidos sem uma definição de diagnóstico.  Crie uma definição de diagnóstico para enviar entradas para registos do Monitor Azure ou para reencamizá-las para fora do Azure.
- [Registo de atividade](./platform-logs-overview.md) - O registo de atividade é recolhido automaticamente sem necessidade de configuração e pode ser visto no portal Azure. Crie uma definição de diagnóstico para copiá-los para Registos do Monitor Azure ou para reencamizá-los para fora de Azure.

### <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
A recolha de dados em Registos monitores Azure requer um espaço de trabalho log analytics. Pode começar a monitorizar o seu serviço rapidamente criando um novo espaço de trabalho, mas pode haver valor na utilização de um espaço de trabalho que está a recolher dados de outros serviços. Consulte [Criar um espaço de trabalho log Analytics no portal Azure](../logs/quick-create-workspace.md) para obter detalhes sobre a criação de um espaço de trabalho e [conceber a sua implementação de Registos monitores Azure](../logs/design-logs-deployment.md) para ajudar a determinar o melhor design do espaço de trabalho para os seus requisitos. Se utilizar um espaço de trabalho existente na sua organização, então necessitará de permissões apropriadas, conforme descrito na [Gestão do acesso a dados de registo e espaços de trabalho no Azure Monitor.](../logs/manage-access.md) 





## <a name="diagnostic-settings"></a>Definições de diagnóstico
As definições de diagnóstico definem onde devem ser enviados registos de recursos e métricas para um determinado recurso. Os destinos possíveis são:

- [Log Analytics espaço de trabalho](./resource-logs.md#send-to-log-analytics-workspace) que permite analisar dados com outros dados de monitorização recolhidos pelo Azure Monitor usando consultas de registo poderosas e também para aproveitar outras funcionalidades do Azure Monitor, como alertas de registo e visualizações. 
- [Os centros de eventos](./resource-logs.md#send-to-azure-event-hubs) para transmitir dados para sistemas externos, tais como SIEMs de terceiros e outras soluções de análise de registo. 
- [Conta de armazenamento Azure](./resource-logs.md#send-to-azure-storage) que é útil para auditoria, análise estática ou backup.

Siga o procedimento na [configuração de diagnóstico para recolher registos e métricas da plataforma em Azure](../essentials/diagnostic-settings.md) para criar e gerir definições de diagnóstico através do portal Azure. Consulte [Criar a definição de diagnóstico em Azure utilizando um modelo de Gestor de Recursos](./resource-manager-diagnostic-settings.md) para defini-los num modelo e permitir uma monitorização completa de um recurso quando este for criado.


## <a name="monitoring-in-the-azure-portal"></a>Monitorização no portal Azure
 Pode aceder aos dados de monitorização da maioria dos recursos Azure a partir do menu do recurso no portal Azure. Isto lhe dará acesso aos dados de um único recurso utilizando ferramentas padrão do Azure Monitor. Alguns serviços da Azure fornecerão diferentes opções, pelo que deverá fazer referência à documentação desse serviço para obter informações adicionais. Utilize o menu **Azure Monitor** para analisar dados de todos os recursos monitorizados. 

### <a name="overview"></a>Descrição Geral
Muitos serviços incluirão a monitorização de dados na sua página **de Visão Geral** como um rápido olhar para o seu funcionamento. Isto será normalmente baseado num subconjunto de métricas de plataforma armazenadas em Métricas do Monitor Azure. Outras opções de monitorização estarão normalmente disponíveis numa secção de **monitorização** do menu do serviço.

![Página de descrição geral](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Insights e Soluções 
Alguns serviços fornecerão ferramentas para além das características padrão do Azure Monitor. [Os Insights](../monitor-reference.md) fornecem uma experiência de monitorização personalizada construída na plataforma de dados do Azure Monitor e nas funcionalidades padrão. [As soluções](../insights/solutions.md) fornecem lógica de monitorização predefinida baseada em registos do Monitor Azure. 

Se um serviço tiver uma visão do Azure Monitor, pode aceder-lhe a partir da **Monitorização** no menu de cada recurso. Aceda a todos os insights e soluções do menu **Azure Monitor.**

![Insights no portal Azure](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Métricas
Analise as métricas no portal Azure utilizando o [explorador de métricas](./metrics-getting-started.md) que está disponível no item do menu **Métricas** para a maioria dos serviços. Esta ferramenta permite-lhe trabalhar com métricas individuais ou combinar múltiplos para identificar correlações e tendências. 

- Veja [como começar com o Azure Metrics Explorer](./metrics-getting-started.md) para o básico de usar o explorador de métricas.
- Consulte [funcionalidades avançadas do Azure Metrics Explorer](../essentials/metrics-charts.md) para funcionalidades avançadas do explorador de métricas, tais como usar várias métricas e aplicar filtros e dividir.

![Explorador de métricas no portal Azure](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Registo de atividades 
Ver entradas no registo de atividade no portal Azure com o filtro inicial definido para o recurso atual. Copie o registo de atividade para um espaço de trabalho log Analytics para aceder a ele para usá-lo em consultas de registo e livros de trabalho. 

- Consulte os eventos de registo de atividade do [Azure](../essentials/activity-log.md#view-the-activity-log) para obter detalhes sobre a visualização do registo de atividade e a recuperação de entradas utilizando uma variedade de métodos.
- Consulte a documentação do seu serviço Azure para os eventos específicos que são registados.

![Registo de Atividades](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
O Azure Monitor Logs consolida registos e métricas de vários serviços e outras fontes de dados para análise com uma poderosa ferramenta de consulta. Como descrito acima, crie uma definição de diagnóstico para recolher métricas de plataforma, registos de atividade e registos de recursos num espaço de trabalho log Analytics no Azure Monitor.

[O Log Analytics](../logs/log-analytics-tutorial.md) permite-lhe trabalhar com [consultas de registo](../logs/log-query-overview.md), que é uma característica poderosa do Azure Monitor que lhe permite realizar uma análise avançada dos dados de registo utilizando uma linguagem de consulta totalmente apresentada. Abrir Log Analytics a partir de **Logs** no menu **de monitorização** para que um recurso Azure funcione com consultas de registo utilizando o recurso como âmbito de [consulta](../logs/scope.md#query-scope). Isto permite analisar dados em várias tabelas apenas para esse recurso. Utilize **Registos** do menu Azure Monitor para aceder a registos para todos os recursos. 

- Consulte [Começar com consultas de log no Azure Monitor](../logs/get-started-queries.md) para obter um tutorial sobre a utilização do idioma de consulta utilizado para escrever consultas de log.
- Consulte [os registos de recursos do Collect Azure no log analytics workspace no Azure Monitor](./resource-logs.md#send-to-log-analytics-workspace) para obter informações sobre como os registos de recursos são recolhidos em Registos do Monitor Azure e detalhes sobre como aceder aos mesmos numa consulta.
- Consulte [o modo de recolha](./resource-logs.md#send-to-log-analytics-workspace) para obter uma explicação de como os dados de registo de recursos são estruturados em Registos do Monitor Azure.
- Consulte a documentação de cada serviço Azure para obter detalhes sobre a sua tabela nos Registos do Monitor Azure.

![Log Analytics no portal Azure](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitorização da linha de comando
Pode aceder aos dados de monitorização recolhidos a partir de uma linha de comando ou incluir num script utilizando a interface de linha [de comando Azure PowerShell](/powershell/azure/) ou [Azure Command Line Interface](/cli/azure/). 

- Consulte [a referência de métricas CLI](/cli/azure/monitor/metrics) para aceder a dados métricos do CLI.
- Consulte [a referência CLI Log Analytics](/cli/azure/ext/log-analytics/monitor/log-analytics) para aceder aos dados do Azure Monitor Logs utilizando uma consulta de registo a partir do CLI.
- Consulte [a referência das métricas Azure PowerShell](/powershell/module/azurerm.insights/get-azurermmetric) para aceder a dados métricos da Azure PowerShell.
- Consulte [a referência de consulta de registos Azure PowerShell](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) para aceder aos dados do Azure Monitor Logs utilizando uma consulta de registo da Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitorização da REST API
Inclua os dados de monitorização recolhidos do seu recurso numa aplicação personalizada utilizando uma API REST.

- Consulte [a Azure Monitoring REST API walkthrough](./rest-api-walkthrough.md) para obter detalhes sobre o acesso às métricas a partir da API do Monitor Azure.
- Consulte [a Azure Log Analytics REST API](https://dev.loganalytics.io/) para obter informações sobre o acesso aos dados do Azure Monitor Logs utilizando uma consulta de registo da Azure PowerShell.

## <a name="alerts"></a>Alertas
[Os alertas](../alerts/alerts-overview.md) notificam-no proativamente e podem tomar medidas quando forem encontradas condições importantes nos seus dados de monitorização. Cria-se uma regra de alerta que define um alvo para o alerta, as condições para criar um alerta e quaisquer ações a tomar em resposta.

Diferentes tipos de dados de monitorização são utilizados para diferentes tipos de regras de alerta.

- [Alerta de registo de atividade](../alerts/alerts-activity-log.md) - Criar um alerta quando uma entrada é criada no registo de atividade que corresponde a critérios específicos. Isto permite-lhe ser notificado, por exemplo, quando um determinado tipo de recurso é criado ou se uma alteração de configuração falhar.
- [Alerta métrico](../alerts/alerts-metric.md) - Criar um alerta quando um valor métrico exceder um determinado limiar. Os alertas métricos são mais responsivos do que outros alertas e podem ser resolvidos automaticamente quando o problema é corrigido.
- [Alerta de consulta de registo](../alerts/alerts-log.md) - Faça uma consulta de registo em intervalos regulares e crie um alerta se uma determinada condição for encontrada. Isto permite-lhe realizar análises complexas em vários conjuntos de dados e .

Utilize **alertas** do menu de um recurso para visualizar alertas e gerir as regras de alerta para esse recurso. Apenas os alertas de registo de atividade e os alertas métricos utilizam os recursos Azure individuais como alvo. Os alertas de consulta de registo utilizam um espaço de trabalho log Analytics como alvo e baseiam-se numa consulta que pode aceder a quaisquer registos armazenados nesse espaço de trabalho. Utilize o menu Azure Monitor para visualizar e gerir alertas para todos os recursos e gerir as regras de alerta de consulta de registo.

- Consulte os artigos para os diferentes tipos de alertas acima para obter detalhes sobre a criação de regras de alerta.
- Consulte [Criar e gerir grupos de ação no portal Azure](../alerts/action-groups.md) para obter detalhes sobre a criação de um grupo de ação que lhe permita gerir respostas aos alertas.



## <a name="next-steps"></a>Passos seguintes

* Consulte [serviços, esquemas e categorias de Registos de Recursos Azure](./resource-logs-schema.md) para obter detalhes de registos de recursos para diferentes serviços Azure.