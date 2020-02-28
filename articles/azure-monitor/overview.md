---
title: Visão geral do Monitor Azure  Microsoft Docs
description: Descrição geral dos serviços e das funcionalidades da Microsoft que contribuem para uma estratégia completa de monitorização para os seus serviços e aplicações do Azure.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: cec437929e2f7ccd94cf4cf94ec81f87dac2678a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669170"
---
# <a name="azure-monitor-overview"></a>Descrição geral do Azure Monitor

O Azure Monitor maximiza a disponibilidade e desempenho das suas aplicações e serviços, fornecendo uma solução abrangente para recolher, analisar e atuar em telemetria a partir da sua nuvem e ambientes no local. Ajuda a compreender o desempenho das suas aplicações e identifica proativamente os problemas que as afetam e os recursos de que dependem.

Apenas alguns exemplos do que pode fazer com o Monitor Azure incluem:

- Detetar e diagnosticar problemas através de aplicações e dependências com [Insights de Aplicação](app/app-insights-overview.md).
- Correlacionar problemas de infraestruturas com [o Monitor Azure para VMs](insights/vminsights-overview.md) e [o Monitor Azure para Contentores.](insights/container-insights-overview.md)
- Insto risa os seus dados de monitorização com [o Log Analytics](log-query/log-query-overview.md) para resolução de problemas e diagnósticos profundos.
- Operações de suporte em escala com [alertas inteligentes](platform/alerts-smartgroups-overview.md) e [ações automatizadas.](platform/alerts-action-rules.md)
- Crie visualizações com [tabliers](learn/tutorial-logs-dashboards.md) [E](app/usage-workbooks.md)Livros Azure.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Descrição geral

O diagrama seguinte dá uma visão de alto nível do Monitor Azure. No centro do diagrama estão as lojas de dados para métricas e registos, que são os dois tipos fundamentais de utilização de dados pelo Azure Monitor. À esquerda estão as [fontes de monitorização dos dados](platform/data-sources.md) que povoam estas lojas de [dados.](platform/data-platform.md) À direita estão as diferentes funções que o Azure Monitor desempenha com estes dados recolhidos, tais como análise, alerta e streaming para sistemas externos.

![Descrição geral do Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Plataforma de dados de monitorização

Todos os dados recolhidos pelo Monitor Azure se enquadram num de dois tipos fundamentais, [métricas e registos.](platform/data-platform.md) [As métricas](platform/data-platform-metrics.md) são valores numéricos que descrevem algum aspeto de um sistema num determinado momento do tempo. Elas são simples e capaz de oferecer suporte a cenários em tempo real em tempo quase. [Os registos](platform/data-platform-logs.md) contêm diferentes tipos de dados organizados em registos com diferentes conjuntos de propriedades para cada tipo. Telemetria, como eventos e rastreios são armazenadas como registos além do mais dados de desempenho para que ele possível combinar tudo para análise.

Para muitos recursos do Azure, você verá dados recolhidos pelo Azure Monitor bem na sua página de visão geral no portal Azure. Veja qualquer máquina virtual, por exemplo, e verá vários gráficos com métricas de desempenho. Clique em qualquer um dos gráficos para abrir os dados no explorador de [métricas](platform/metrics-charts.md) no portal Azure, o que lhe permite traçar os valores de várias métricas ao longo do tempo.  Pode ver os gráficos interativamente ou afixá-los a um dashboard para visualizá-los com outras visualizações.

![Métricas](media/overview/metrics.png)

Os dados de registo recolhidos pelo Azure Monitor podem ser analisados com [consultas](log-query/log-query-overview.md) para recuperar, consolidar e analisar rapidamente os dados recolhidos.  Pode criar e testar consultas utilizando [log analytics](log-query/portals.md) no portal Azure e, em seguida, analisar diretamente os dados usando estas ferramentas ou guardar consultas para uso com [visualizações](visualizations.md) ou regras de [alerta](platform/alerts-overview.md).

O Azure Monitor utiliza uma versão da [linguagem de consulta Kusto](/azure/kusto/query/) utilizada pelo Azure Data Explorer que é adequada para consultas de registo simples, mas também inclui funcionalidades avançadas como agregações, adesões e análises inteligentes. Você pode aprender rapidamente a linguagem de consulta usando [várias lições](log-query/get-started-queries.md).  É fornecida orientação específica para os utilizadores que já estejam familiarizados com [SQL](log-query/sql-cheatsheet.md) e [Splunk](log-query/splunk-cheatsheet.md).

![Registos](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Que dados o Azure Monitor recolhe?

O Monitor Azure pode recolher dados de várias fontes. Pode pensar em monitorizar os dados das suas aplicações em níveis que vão desde a sua aplicação, qualquer sistema operativo e serviços em que se baseie, até à própria plataforma. O Monitor Azure recolhe dados de cada um dos seguintes níveis:

- Dados de monitorização de **aplicações**: Dados sobre o desempenho e funcionalidade do código que escreveu, independentemente da sua plataforma.
- Dados de **monitorização do OS**do hóspede : Dados sobre o sistema operativo em que a sua aplicação está em execução. Isto pode estar a decorrer em Azure, outra nuvem, ou no local. 
- Dados de **monitorização de recursos Azure**: Dados sobre o funcionamento de um recurso Azure.
- Dados de **monitorização de subscrição azure**: Dados sobre o funcionamento e gestão de uma subscrição Azure, bem como dados sobre a saúde e operação do próprio Azure. 
- Dados de **monitorização do inquilino Azure**: Dados sobre o funcionamento dos serviços Azure ao nível dos inquilinos, como o Azure Ative Directory.

Assim que cria uma subscrição do Azure e começa a adicionar recursos como máquinas virtuais e aplicações web, o Azure Monitor começa a recolher dados.  [Os registos de atividade registam](platform/platform-logs-overview.md) quando os recursos são criados ou modificados. [As métricas](platform/data-platform.md) dizem-nos como o recurso está a funcionar e os recursos que está a consumir. 

Alargar os dados que está a recolher para o funcionamento real dos [recursos, permitindo diagnósticos](platform/platform-logs-overview.md) e [adicionando um agente](platform/agent-windows.md) para calcular recursos. Isto irá recolher telemetria para o funcionamento interno do recurso e permitir-lhe configurar diferentes [fontes](platform/agent-data-sources.md) de dados para recolher registos e métricas do sistema operativo windows e do linux. 

Ative a monitorização da aplicação de [Serviços de Aplicações](app/azure-web-apps.md) ou [VM e aplicação de conjunto](app/azure-vm-vmss-apps.md)de escala de máquina virtual, para permitir que os Insights de Aplicação recolham informações detalhadas sobre a sua aplicação, incluindo visualizações de páginas, pedidos de aplicação e exceções. Verifique ainda a disponibilidade da sua aplicação configurando um [teste de disponibilidade](app/monitor-web-app-availability.md) para simular o tráfego do utilizador.

### <a name="custom-sources"></a>Fontes personalizadas

O Azure Monitor pode recolher dados de registo de qualquer cliente REST utilizando a API do [Coletor](platform/data-collector-api.md)de Dados . Isto permite criar cenários de monitorização personalizados e alargar a monitorização a recursos que não expõem a telemetria através de outras fontes.

## <a name="insights"></a>Informações
A monitorização dos dados só é útil se puder aumentar a sua visibilidade para o funcionamento do seu ambiente de computação. O Azure Monitor inclui várias funcionalidades e ferramentas que fornecem informações valiosas sobre as suas aplicações e outros recursos de que dependem. Soluções e funcionalidades de [monitorização,](insights/solutions.md) tais como [Application Insights](app/app-insights-overview.md) e [Azure Monitor para contentores,](insights/container-insights-overview.md) fornecem informações profundas sobre diferentes aspetos da sua aplicação e serviços específicos do Azure. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) monitoriza a disponibilidade, desempenho e utilização das suas aplicações web, quer estejam alojadas na nuvem ou no local. Aproveita a poderosa plataforma de análise de dados no Monitor Do Azure para lhe fornecer informações profundas sobre as operações da sua aplicação e diagnosticar erros sem esperar que um utilizador os reporte. Application Insights inclui pontos de ligação a uma variedade de ferramentas de desenvolvimento e integra-se com o Estúdio Visual para apoiar os seus processos DevOps.

![Insights de Aplicativos](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores
[O Azure Monitor para contentores](insights/container-insights-overview.md) é uma funcionalidade concebida para monitorizar o desempenho das cargas de trabalho dos contentores implantadas para gerir os clusters Kubernetes alojados no Serviço Azure Kubernetes (AKS). Dá visibilidade ao desempenho, recolhendo métricas de memória e processador de controladores, nós e recipientes disponíveis em Kubernetes através da API métricas. Os registos do contentor também são recolhidos.  Depois de ativar a monitorização dos clusters kubernetes, estas métricas e registos são automaticamente recolhidos para si através de uma versão contentorizada do agente Log Analytics para o Linux.

![Saúde dos recipientes](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
O [Monitor Azure para VMs](insights/vminsights-overview.md) monitoriza as suas máquinas virtuais Azure (VM) em escala, analisando o desempenho e a saúde dos seus VMs Windows e Linux, incluindo os seus diferentes processos e dependências interligadas em outros recursos e processos externos. A solução inclui suporte para monitorizar o desempenho e dependências de aplicações para VMs hospedados no local ou outro fornecedor de nuvem.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Soluções de monitorização
[As soluções](insights/solutions.md) de monitorização no Monitor Azure são conjuntos de lógica embalados que fornecem insights para uma determinada aplicação ou serviço. Incluem lógica para a recolha de dados de monitorização para a aplicação ou serviço, [consultas](log-query/log-query-overview.md) para analisar esses dados, e [vistas](../log-analytics/log-analytics-view-designer.md) para visualização. As soluções [de](insights/solutions-inventory.md) monitorização estão disponíveis da Microsoft e parceiros para fornecer monitorização para vários serviços Azure e outras aplicações.

![Soluções de monitorização](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Responder a situações críticas
Além de permitir analisar interativamente os dados de monitorização, uma solução de monitorização eficaz deve ser capaz de responder proativamente às condições críticas identificadas nos dados que recolhe. Isto pode estar a enviar um sms ou um correio a um administrador responsável pela investigação de um problema. Ou pode lançar um processo automatizado que tente corrigir uma condição de erro.


### <a name="alerts"></a>Alertas
[Os alertas no Monitor Azure](platform/alerts-overview.md) notificam-no proativamente de condições críticas e potencialmente tentam tomar medidas corretivas. As regras de alerta baseadas em métricas fornecem alertas quase em tempo real baseados em valores numéricos, enquanto as regras baseadas em registos permitem uma lógica complexa através de dados de várias fontes.

As regras de alerta em [grupos](platform/action-groups.md)de ação do Monitor Azure , que contêm conjuntos únicos de destinatários e ações que podem ser partilhadas através de várias regras. Com base nos seus requisitos, os grupos de ação podem realizar ações como usar webhooks para ter alertas iniciar ações externas ou integrar-se com as suas ferramentas ITSM.

![Alertas](media/overview/alerts.png)

### <a name="autoscale"></a>Dimensionamento Automático
A escala automática permite-lhe dispor da quantidade certa de recursos em execução para lidar com a carga na sua aplicação. Permite criar regras que utilizam métricas recolhidas pelo Azure Monitor para determinar quando adicionar automaticamente recursos para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão parados. Especifica um número mínimo e máximo de casos e a lógica para quando aumentar ou diminuir os recursos.

![Dimensionamento Automático](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualização de dados de monitorização
[Visualizações](visualizations.md) como gráficos e tabelas são ferramentas eficazes para resumir dados de monitorização e apresentá-lo a diferentes públicos. O Azure Monitor tem as suas próprias funcionalidades para visualizar dados de monitorização e aproveita outros serviços azure para publicá-lo em diferentes públicos.

### <a name="dashboards"></a>Dashboards
Os [dashboards Azure](../azure-portal/azure-portal-dashboards.md) permitem combinar diferentes tipos de dados, incluindo métricas e troncos, num único painel no [portal Azure](https://portal.azure.com). Pode partilhar opcionalmente o dashboard com outros utilizadores do Azure. Elementos em todo o Monitor Azure podem ser adicionados a um painel de instrumentos Azure, além da saída de qualquer consulta de registo ou gráfico de métricas. Por exemplo, pode criar um dashboard que combine azulejos que mostrem um gráfico de métricas, uma tabela de registos de atividade, um gráfico de utilização de Application Insights e a saída de uma consulta de registo.

![Dashboard](media/overview/dashboard.png)

### <a name="views"></a>Vistas
[As visualizações](../log-analytics/log-analytics-view-designer.md) apresentam dados de registo visualmente presentes no Monitor Azure.  Cada vista inclui um único azulejo que se perfura até uma combinação de visualizações, como gráficos de barras e linhas, além de listas resumindo dados críticos.  As soluções de monitorização incluem pontos de vista que resumem dados para uma determinada aplicação, e pode criar as suas próprias opiniões para apresentar dados a partir de qualquer consulta de registo. Tal como outros elementos do Monitor Azure, as vistas podem ser adicionadas aos dashboards Azure.

![Ver](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[O Power BI](https://powerbi.microsoft.com) é um serviço de análise de negócios que fornece visualizações interativas através de uma variedade de fontes de dados e é um meio eficaz de disponibilizar dados a outros dentro e fora da sua organização. Pode configurar o Power BI para [importar automaticamente dados de registo do Azure Monitor](../log-analytics/log-analytics-powerbi.md) para tirar partido destas visualizações adicionais.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Dados integrados e de exportação
Muitas vezes terá a obrigação de integrar o Monitor Azure com outros sistemas e de construir soluções personalizadas que utilizem os seus dados de monitorização. Outros serviços azure trabalham com o Azure Monitor para proporcionar esta integração.

### <a name="event-hub"></a>Hub de Eventos
[O Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) é um serviço de plataforma de streaming e de ingestão de eventos que pode transformar e armazenar dados utilizando qualquer fornecedor de análise em tempo real ou adaptadores de lotação/armazenamento. Utilize os Hubs de Eventos para [transmitir dados do Monitor Azure](platform/stream-monitoring-data-event-hubs.md) para o parceiro SIEM e ferramentas de monitorização.


### <a name="logic-apps"></a>Aplicações Lógicas
[Logic Apps](https://azure.microsoft.com/services/logic-apps) é um serviço que permite automatizar tarefas e processos de negócio utilizando fluxos de trabalho que se integram com diferentes sistemas e serviços. Estão disponíveis atividades que lêem e escrevem métricas e registos no Monitor Azure, o que lhe permite construir fluxos de trabalho integrando-se com uma variedade de outros sistemas.


### <a name="api"></a>API
Várias APIs estão disponíveis para ler e escrever métricas e registos de e para o Monitor Azure, além de aceder a alertas gerados. Também pode configurar e recuperar alertas. Isto proporciona-lhe possibilidades essencialmente ilimitadas para construir soluções personalizadas que se integrem com o Azure Monitor.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:

* [Métricas e registos](platform/data-platform.md) para os dados recolhidos pelo Monitor Azure.
* [Fontes](platform/data-sources.md) de dados para como os diferentes componentes da sua aplicação enviam telemetria.
* [Registar consultas](log-query/log-query-overview.md) para analisar os dados recolhidos.
* [Boas práticas](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) para monitorizar aplicações e serviços em nuvem.
