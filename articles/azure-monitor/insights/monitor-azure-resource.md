---
title: Monitor de recursos Azure com o Monitor Azure [ Monitor Azul] Microsoft Docs
description: Descreve como recolher e analisar dados de monitorização de recursos em Azure usando o Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249272"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorização de recursos Azure com o Monitor Azure
Quando se tem aplicações críticas e processos de negócio baseados nos recursos do Azure, pretende monitorizar esses recursos para a sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitorização gerados pelos recursos do Azure e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar sobre estes dados.

> [!IMPORTANT]
> Este artigo aplica-se a todos os serviços do Azure que utilizam o Azure Monitor. Os recursos computacionais, incluindo VMs e App Service, geram os mesmos dados de monitorização descritos aqui, mas também têm um sistema operativo de hóspedes que também pode gerar registos e métricas. Consulte a documentação de monitorização destes serviços para obter detalhes sobre como recolher e analisar estes dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O Azure Monitor é um serviço completo de monitorização de pilhas em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure, além de recursos em outras nuvens e no local. A plataforma de [dados Azure Monitor](../platform/data-platform.md) recolhe dados em [registos](../platform/data-platform-logs.md) e [métricas](../platform/data-platform-metrics.md) onde podem ser analisados em conjunto utilizando um conjunto completo de ferramentas de monitorização, conforme descrito nas seguintes secções.

- [O que se pode fazer com as Métricas do Monitor Azure?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [O que pode fazer com os Registos do Monitor Azure?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Assim que criar um recurso Azure, o Azure Monitor está ativado e começa a recolher métricas e registos de atividade que pode [ver e analisar no portal Azure.](#monitoring-in-the-azure-portal) Com alguma configuração, pode recolher dados adicionais de monitorização e ativar funcionalidades adicionais. Consulte [os Dados de Monitorização](#monitoring-data) abaixo para obter detalhes sobre quaisquer requisitos de configuração.


## <a name="costs-associated-with-monitoring"></a>Custos associados à monitorização
Não há qualquer custo para analisar dados de monitorização que são recolhidos por padrão. Isto inclui o seguinte:

- Recolhendo métricas da plataforma e analisando-as com o explorador de métricas.
- Recolher registo de Atividades e analisá-lo no portal Azure.
- Criar uma regra de alerta de registo de atividade.

Não existem custos do Monitor Azure para a recolha e exportação de troncos e métricas, mas pode haver custos relacionados associados ao destino:

- Custos associados à ingestão e retenção de dados na recolha de registos e métricas no espaço de trabalho do Log Analytics. Consulte os preços do [Monitor Azure para log analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Custos associados ao armazenamento de dados ao recolher registos e métricas numa conta de armazenamento Azure. Consulte [o preço de armazenamento Azure para armazenamento de bolhas](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Custos associados ao streaming de hub de eventos ao reencaminhar registos e métricas para Hubs de Eventos Azure. Consulte os preços dos Hubs de [Eventos Azure.](https://azure.microsoft.com/pricing/details/event-hubs/)

Pode haver custos do Monitor Azure associados aos seguintes. Ver preços do [Monitor Azure:](https://azure.microsoft.com/pricing/details/monitor/)

- A fazer uma consulta de registo.
- Criar uma regra de alerta de consulta métrica ou de registo.
- Enviando uma notificação de qualquer regra de alerta.
- Aceder às métricas através da API.

## <a name="monitoring-data"></a>Monitorizar dados
Os recursos em Azure geram [registos](../platform/data-platform-logs.md) e [métricas mostrados](../platform/data-platform-metrics.md) no seguinte diagrama. Consulte a documentação de cada serviço Azure para os dados específicos que geram e quaisquer soluções ou insights adicionais que forneçam.

![Descrição geral](media/monitor-azure-resource/logs-metrics.png)



- [Métricas](../platform/data-platform-metrics.md) da plataforma - valores numéricos que são automaticamente recolhidos em intervalos regulares e descrevem algum aspeto de um recurso em um determinado momento. 
- [Registos de recursos](../platform/platform-logs-overview.md) - Forneça informações sobre as operações que foram realizadas dentro de um recurso Azure (o plano de dados), por exemplo, obter um segredo de um Cofre chave ou fazer um pedido para uma base de dados. O conteúdo e estrutura dos registos de recursos varia em função do serviço Azure e do tipo de recursos.
- [Registo de atividade](../platform/platform-logs-overview.md) - Fornece informações sobre as operações em cada recurso Azure na subscrição a partir do exterior (o plano de gestão), por exemplo, criando um novo recurso ou iniciando uma máquina virtual. Esta é a informação sobre o quê, quem e quando para quaisquer operações de escrita (PUT, POST, DELETE) assumiu os recursos da sua subscrição.


## <a name="configuration-requirements"></a>Requisitos de configuração

### <a name="configure-monitoring"></a>Configurar a monitorização
Alguns dados de monitorização são recolhidos automaticamente, mas pode ser necessário efetuar alguma configuração dependendo dos seus requisitos. Consulte as informações abaixo para obter informações específicas para cada tipo de dados de monitorização.

- [Métricas da plataforma](../platform/data-platform-metrics.md) - As métricas da plataforma são recolhidas automaticamente nas [Métricas do Monitor Do Azure](../platform/data-platform-metrics.md) sem necessidade de configuração. Crie uma definição de diagnóstico para enviar entradas para registos do Monitor Azure ou para reencaminhar para fora do Azure.
- [Registos](../platform/platform-logs-overview.md) de recursos - Os registos de recursos são automaticamente gerados pelos recursos do Azure, mas não recolhidos sem uma definição de diagnóstico.  Crie uma definição de diagnóstico para enviar entradas para registos do Monitor Azure ou para reencaminhar para fora do Azure.
- [Registo de atividades](../platform/platform-logs-overview.md) - O registo de atividade é recolhido automaticamente sem qualquer configuração necessária e pode ser visto no portal Azure. Crie uma definição de diagnóstico para copiá-los para registos do Monitor Azure ou para reencaminhar para fora do Azure.

### <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
A recolha de dados em Registos do Monitor Azure requer um espaço de trabalho de Log Analytics. Pode começar a monitorizar o seu serviço rapidamente criando um novo espaço de trabalho, mas pode haver valor na utilização de um espaço de trabalho que está a recolher dados de outros serviços. Consulte criar um espaço de [trabalho log Analytics no portal Azure](../learn/quick-create-workspace.md) para obter detalhes sobre a criação de um espaço de trabalho e projetar a implementação de [Registos do Monitor Azure](../platform/design-logs-deployment.md) para ajudar a determinar o melhor design de espaço de trabalho para as suas necessidades. Se utilizar um espaço de trabalho existente na sua organização, então necessitará de permissões adequadas, conforme descrito no Acesso a dados de registo e espaços de [trabalho no Monitor Azure](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Definições de diagnóstico
As definições de diagnóstico definem onde devem ser enviados registos e métricas de recursos para um determinado recurso. Os destinos possíveis são:

- [Log Analytics workspace](../platform/resource-logs-collect-workspace.md) que permite analisar dados com outros dados de monitorização recolhidos pelo Azure Monitor usando consultas de registo poderosas e também para alavancar outras funcionalidades do Monitor de Acesso, tais como alertas de registo e visualizações. 
- Centros de [eventos](../platform/resource-logs-stream-event-hubs.md) para transmitir dados para sistemas externos, tais como SIEMs de terceiros e outras soluções de análise de registo. 
- Conta de [armazenamento azure](../platform/resource-logs-collect-storage.md) que é útil para auditoria, análise estática ou backup.

Siga o procedimento na [Definição de diagnóstico Create para recolher registos e métricas de plataforma em Azure](../platform/diagnostic-settings.md) para criar e gerir as definições de diagnóstico através do portal Azure. Consulte a definição de [diagnóstico Create em Azure utilizando um modelo de Gestor](../platform/diagnostic-settings-template.md) de Recursos para defini-los num modelo e permitir a monitorização completa de um recurso quando for criado.


## <a name="monitoring-in-the-azure-portal"></a>Monitorização no portal Azure
 Pode aceder a dados de monitorização da maioria dos recursos do Azure a partir do menu do recurso no portal Azure. Isto lhe dará acesso aos dados de um único recurso utilizando ferramentas padrão do Monitor Azure. Alguns serviços Azure fornecerão diferentes opções, pelo que deverá fazer referência à documentação desse serviço para obter informações adicionais. Utilize o menu **Azure Monitor** para analisar dados de todos os recursos monitorizados. 

### <a name="overview"></a>Descrição geral
Muitos serviços incluirão a monitorização de dados na sua página **de visão geral** como um rápido olhar para o seu funcionamento. Isto será normalmente baseado num subconjunto de métricas de plataforma armazenadas em Métricas de Monitor Azure. Outras opções de monitorização estarão normalmente disponíveis numa secção de **Monitorização** dos serviços. .

![Página de visão geral](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Insights e Soluções 
Alguns serviços fornecerão ferramentas para além das características padrão do Monitor Azure. [Insights](../insights/insights-overview.md) fornecem uma experiência de monitorização personalizada baseada na plataforma de dados Azure Monitor e funcionalidades padrão. [As soluções](../insights/solutions.md) fornecem uma lógica de monitorização predefinida construída em Registos de Monitor Estoque Azure. 

Se um serviço tiver uma visão do Monitor Azure, pode acessá-lo a partir da **Monitorização** no menu de cada recurso. Aceda a todos os insights e soluções do menu **Azure Monitor.**

![Informações](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Métricas
Analise as métricas no portal Azure utilizando o explorador de [métricas](../platform/metrics-getting-started.md) que está disponível a partir do item do menu Metrics para a maioria dos **serviços.** Esta ferramenta permite-lhe trabalhar com métricas individuais ou combinar múltiplas para identificar correlações e tendências. 

- Ver [Começar com o Azure Metrics Explorer](../platform/metrics-getting-started.md) para o básico de usar o explorador de métricas.
- Consulte [as características avançadas do Azure Metrics Explorer](../platform/metrics-charts.md) para obter funcionalidades avançadas do explorador de métricas, tais como usar várias métricas e aplicar filtros e dividir.

![Métricas](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Registo de atividades 
Ver entradas no registo de atividade no portal Azure com o filtro inicial definido para o recurso atual. Copie o registo de atividade para um espaço de trabalho do Log Analytics para aceder ao mesmo para o utilizar em consultas de registo e livros de trabalho. 

- Consulte ver [e recuperar eventos](../platform/activity-log-view.md) de registo da Atividade Azure para obter detalhes sobre a visualização do registo de atividade e recuperação de entradas utilizando uma variedade de métodos.
- Consulte a documentação do seu serviço Azure para os eventos específicos que são registados.

![Registo de Atividades](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
O Azure Monitor Logs consolida registos e métricas de vários serviços e outras fontes de dados para análise com uma poderosa ferramenta de consulta. Como descrito acima, crie uma definição de diagnóstico para recolher métricas de plataforma, registo de atividade e registos de recursos num espaço de trabalho de Log Analytics no Monitor Azure.

[O Log Analytics](../log-query/get-started-portal.md) permite-lhe trabalhar com consultas de [registo,](../log-query/log-query-overview.md)que é uma característica poderosa do Monitor Azure que lhe permite realizar uma análise avançada dos dados de registo utilizando uma linguagem de consulta totalmente apresentada. Abra o Log Analytics a partir de **Logs** no menu **monitoramento** para um recurso Azure para trabalhar com consultas de registo usando o recurso como âmbito de [consulta](../log-query/scope.md#query-scope). Isto permite-lhe analisar dados em várias tabelas apenas para esse recurso. Utilize **registos** do menu Do Monitor Azure para aceder a registos para todos os recursos. 

- Ver Começar com consultas de [log no Monitor Azure](../log-query/get-started-queries.md) para um tutorial sobre a utilização da linguagem de consulta usada para escrever consultas de log.
- Consulte os registos de recursos do Azure no espaço de [trabalho do Log Analytics no Monitor Azure](../platform/resource-logs-collect-workspace.md) para obter informações sobre como os registos de recursos são recolhidos em Registos do Monitor Do Azure e detalhes sobre como acessá-los numa consulta.
- Consulte o [modo de recolha](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) para obter uma explicação de como os dados de registo de recursos são estruturados em Registos do Monitor Do Azure.
- Consulte a documentação de cada serviço Azure para obter detalhes na sua tabela em Registos do Monitor Azure.

![Registos](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitorização da linha de comando
Pode aceder aos dados de monitorização recolhidos a partir do seu recurso a partir de uma linha de comando ou incluir num script utilizando a Interface da Linha de Comando [Azure PowerShell](/powershell/azure/) ou [Azure](/cli/azure/). 

- Consulte a referência métrica serrada do [CLI](/cli/azure/monitor/metrics) para aceder a dados métricos a partir do CLI.
- Consulte a [referência CLI Log Analytics](/cli/azure/ext/log-analytics/monitor/log-analytics) para aceder aos dados de registos do Monitor Do Azure utilizando uma consulta de log do CLI.
- Consulte a referência das [métricas Azure PowerShell](/powershell/module/azurerm.insights/get-azurermmetric) para aceder a dados métricos da Azure PowerShell.
- Consulte a referência de consulta de log log [Azure PowerShell](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) para aceder aos dados do Azure Monitor Logs utilizando uma consulta de log da Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitorização da API REST
Inclua a monitorização dos dados recolhidos do seu recurso numa aplicação personalizada utilizando uma API REST.

- Consulte o [acompanhamento do Azure REST API](../platform/rest-api-walkthrough.md) para obter mais informações sobre o acesso às métricas a partir da API REST do Monitor Azure.
- Consulte [o Azure Log Analytics REST API](https://dev.loganalytics.io/) para obter informações sobre o acesso aos dados do Azure Monitor Logs utilizando uma consulta de log da Azure PowerShell.

## <a name="alerts"></a>Alertas
[Os alertas](../platform/alerts-overview.md) notificam-no proativamente e potencialmente tomam medidas quando forem encontradas condições importantes nos seus dados de monitorização. Cria-se uma regra de alerta que define um alvo para o alerta, as condições para criar um alerta e quaisquer ações a tomar em resposta.

Diferentes tipos de dados de monitorização são usados para diferentes tipos de regras de alerta.

- [Alerta](../platform/alerts-activity-log.md) de registo de atividade - Criar um alerta quando uma entrada é criada no registo de atividade que corresponda a critérios específicos. Isto permite-lhe ser notificado, por exemplo, quando um determinado tipo de recurso é criado ou se uma alteração de configuração falha.
- [Alerta métrico](../platform/alerts-metric.md) - Criar um alerta quando um valor métrico exceder um determinado limiar. Os alertas métricos são mais responsivos do que outros alertas e podem ser resolvidos automaticamente quando o problema é corrigido.
- [Alerta](../platform/alerts-log.md) de consulta de log - Faça uma consulta de registo em intervalos regulares e crie um alerta se for encontrada uma determinada condição. Isto permite-lhe realizar análises complexas em vários conjuntos de dados e .

Utilize **alertas** do menu de um recurso para ver alertas e gerir regras de alerta para esse recurso. Apenas alertas de registo de atividade e alertas métricos usam recursos azure individuais como alvo. Os alertas de consulta de log usam um espaço de trabalho log Analytics como alvo e baseiam-se numa consulta que pode aceder a quaisquer registos armazenados nesse espaço de trabalho. Utilize o menu Azure Monitor para visualizar e gerir alertas para todos os recursos e as regras de alerta de consulta de registo de gestão.

- Consulte os artigos para os diferentes tipos de alertas acima para obter detalhes sobre a criação de regras de alerta.
- Consulte criar e gerir grupos de [ação no portal Azure](../platform/action-groups.md) para obter detalhes sobre a criação de um grupo de ação que lhe permite gerir respostas a alertas.



## <a name="next-steps"></a>Passos seguintes

* Consulte [serviços suportados, schemas e categorias para Registos](../platform/diagnostic-logs-schema.md) de Recursos Azure para obter detalhes sobre registos de recursos para diferentes serviços Azure.  
