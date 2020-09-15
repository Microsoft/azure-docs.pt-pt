---
title: Visão geral do Azure Monitor Microsoft Docs
description: Descrição geral dos serviços e das funcionalidades da Microsoft que contribuem para uma estratégia completa de monitorização para os seus serviços e aplicações do Azure.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 005068c8e81adb9a79a4e6dc7e86a9bfb39902a1
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088634"
---
# <a name="azure-monitor-overview"></a>Descrição geral do Azure Monitor

O Azure Monitor maximiza a disponibilidade e desempenho das suas aplicações e serviços, fornecendo uma solução abrangente para recolher, analisar e agir em telemetria a partir dos seus ambientes de nuvem e no local. Ajuda a compreender o desempenho das suas aplicações e identifica proativamente os problemas que as afetam e os recursos de que dependem.

Apenas alguns exemplos do que pode fazer com o Azure Monitor incluem:

- Detetar e diagnosticar problemas entre aplicações e dependências com [Insights de Aplicação](app/app-insights-overview.md).
- Correlacionar problemas de infraestrutura com [o Monitor Azure para VMs](insights/vminsights-overview.md) e [Monitor Azure para contentores](insights/container-insights-overview.md).
- Faça a sua análise com [o Log Analytics](log-query/log-query-overview.md) para resolução de problemas e diagnósticos profundos.
- Operações de suporte em escala com [alertas inteligentes](platform/alerts-smartgroups-overview.md) e [ações automatizadas.](platform/alerts-action-rules.md)
- Criar visualizações com [painéis](learn/tutorial-logs-dashboards.md) e [livros](platform/workbooks-overview.md)Azure .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Descrição geral

O seguinte diagrama fornece uma visão geral do Azure Monitor. No centro do diagrama estão as lojas de dados para métricas e registos, que são os dois tipos fundamentais de dados utilizados pelo Azure Monitor. À esquerda estão as [fontes de dados de monitorização](platform/data-sources.md) que povoam estas [lojas de dados.](platform/data-platform.md) À direita estão as diferentes funções que o Azure Monitor desempenha com estes dados recolhidos, tais como análise, alerta e streaming para sistemas externos.

![Descrição geral do Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Plataforma de dados de monitorização

Todos os dados recolhidos pelo Azure Monitor enquadram-se num dos dois tipos fundamentais, [métricas e registos.](platform/data-platform.md) [As métricas](platform/data-platform-metrics.md) são valores numéricos que descrevem algum aspeto de um sistema num determinado momento do tempo. São leves e capazes de suportar cenários quase em tempo real. [Os registos](platform/data-platform-logs.md) contêm diferentes tipos de dados organizados em registos com diferentes conjuntos de propriedades para cada tipo. A telemetria, como eventos e vestígios, são armazenados como registos para além dos dados de desempenho, para que todos possam ser combinados para análise.

Para muitos recursos Azure, você verá dados recolhidos pelo Azure Monitor na sua página geral no portal Azure. Veja qualquer máquina virtual, por exemplo, e verá vários gráficos que exibem métricas de desempenho. Clique em qualquer um dos gráficos para abrir os dados no [explorador de métricas](platform/metrics-charts.md) no portal Azure, o que lhe permite traçar os valores de várias métricas ao longo do tempo.  Pode ver os gráficos interativamente ou fixá-los num dashboard para os visualizar com outras visualizações.

![O diagrama mostra dados de métricas que fluem para o Explorador de Métricas para usar em visualizações.](media/overview/metrics.png)

Os dados de registo recolhidos pelo Azure Monitor podem ser [analisados](log-query/log-query-overview.md) com consultas para recuperar, consolidar e analisar rapidamente os dados recolhidos.  Pode criar e testar consultas utilizando o [Log Analytics](./log-query/log-query-overview.md) no portal Azure e, em seguida, analisar diretamente os dados usando diferentes ferramentas ou guardar consultas para utilização com visualizações ou [regras](visualizations.md) [de alerta](platform/alerts-overview.md).

O Azure Monitor utiliza uma versão da linguagem de [consulta Kusto](/azure/kusto/query/) utilizada pelo Azure Data Explorer que é adequada para consultas simples de log, mas também inclui funcionalidades avançadas como agregações, juntas e análise inteligente. Pode aprender rapidamente a linguagem de consulta usando [várias lições.](log-query/get-started-queries.md)  É fornecida orientação específica para os utilizadores que já estejam familiarizados com [SQL](log-query/sql-cheatsheet.md) e [Splunk](log-query/splunk-cheatsheet.md).

![O diagrama mostra os dados dos Registos que fluem para o Log Analytics para análise.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Que dados é que o Azure Monitor recolhe?

O Azure Monitor pode recolher dados de várias origens. Pode pensar em monitorizar os dados das suas aplicações em camadas, desde a sua aplicação, qualquer sistema operativo e serviços de que dependem, até à própria plataforma. O Azure Monitor recolhe dados de cada um dos seguintes escalões:

- **Dados de monitorização**da aplicação : Dados sobre o desempenho e funcionalidade do código que tenha escrito, independentemente da sua plataforma.
- **Dados de monitorização do SISTEMA do Hóspede**: Dados sobre o sistema operativo em que a sua aplicação está em execução. Pode estar em execução no Azure, noutra cloud ou no local. 
- **Dados de monitorização de recurso do Azure**: Dados sobre o funcionamento de um recurso do Azure.
- **Dados de monitorização da subscrição Azure**: Dados sobre o funcionamento e gestão de uma subscrição da Azure, bem como dados sobre a saúde e funcionamento da própria Azure. 
- **Dados de monitorização do inquilino da Azure**: Dados sobre o funcionamento dos serviços Azure ao nível do inquilino, tais como diretório ativo Azure.

Assim que cria uma subscrição do Azure e começa a adicionar recursos, como máquinas virtuais e aplicações Web, o Azure Monitor começa a recolher dados.  [Os registos de atividade](platform/platform-logs-overview.md) registam-se quando os recursos são criados ou modificados. [As métricas](platform/data-platform.md) dizem-lhe como o recurso está a funcionar e os recursos que está a consumir. 

Alargar os dados que está a recolher para o funcionamento real dos [recursos, permitindo diagnósticos](platform/platform-logs-overview.md) e [adicionando um agente](platform/agent-windows.md) para calcular recursos. Isto irá recolher telemetria para o funcionamento interno do recurso e permitir-lhe-á configurar [diferentes fontes](platform/agent-data-sources.md) de dados para recolher registos e métricas do sistema operativo de hóspedes Windows e Linux. 

Habilitar a monitorização da sua [aplicação de Serviços de Aplicações](app/azure-web-apps.md) ou [aplicação de conjunto de escala de VM e de escala de máquina virtual,](app/azure-vm-vmss-apps.md)para permitir que o Application Insights recolha informações detalhadas sobre a sua aplicação, incluindo visualizações de páginas, pedidos de aplicação e exceções. Verifique ainda a disponibilidade da sua aplicação configurando um [teste de disponibilidade](app/monitor-web-app-availability.md) para simular o tráfego do utilizador.

### <a name="custom-sources"></a>Fontes personalizadas

O Azure Monitor pode recolher dados de registo de qualquer cliente REST utilizando a [API do Colecionador de Dados](platform/data-collector-api.md). Isto permite-lhe criar cenários de monitorização personalizados e alargar a monitorização a recursos que não expõem a telemetria através de outras fontes.

## <a name="insights"></a>Informações
A monitorização dos dados só é útil se puder aumentar a sua visibilidade no funcionamento do seu ambiente informático. O Azure Monitor inclui várias funcionalidades e ferramentas que fornecem informações valiosas sobre as suas aplicações e outros recursos de que dependem. [Soluções](insights/solutions.md) de monitorização e funcionalidades como [Application Insights](app/app-insights-overview.md) e [Azure Monitor para contentores fornecem informações profundas](insights/container-insights-overview.md) sobre diferentes aspetos da sua aplicação e serviços específicos da Azure. 

### <a name="application-insights"></a>Application Insights
[O Application Insights](app/app-insights-overview.md) monitoriza a disponibilidade, desempenho e utilização das suas aplicações web, quer estejam hospedadas na nuvem ou no local. Aproveita a poderosa plataforma de análise de dados do Azure Monitor para fornecer-lhe informações profundas sobre as operações da sua aplicação e diagnosticar erros sem esperar que um utilizador os reporte. O Application Insights inclui pontos de ligação para uma variedade de ferramentas de desenvolvimento e integra-se com o Visual Studio para suportar os seus processos de DevOps.

![AppInsights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores
[O Azure Monitor para contentores](insights/container-insights-overview.md) é uma funcionalidade concebida para monitorizar o desempenho das cargas de trabalho dos contentores implantadas em aglomerados de Kubernetes geridos alojados no Serviço Azure Kubernetes (AKS). Dá-lhe visibilidade de desempenho recolhendo métricas de memória e processador de controladores, nós e recipientes que estão disponíveis em Kubernetes através da API métricas. Os registos do contentor também são recolhidos.  Depois de ativar a monitorização dos clusters Kubernetes, estas métricas e registos são automaticamente recolhidos para si através de uma versão contentorizada do agente Log Analytics para o Linux.

![Saúde dos Contentores](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
[O Azure Monitor for VMs](insights/vminsights-overview.md) monitoriza as suas máquinas virtuais Azure (VM) em escala, analisando o desempenho e a saúde dos seus VMs Windows e Linux, incluindo os seus diferentes processos e dependências interligadas com outros recursos e processos externos. A solução inclui suporte para monitorizar o desempenho e as dependências de aplicações para VMs hospedados no local ou outro fornecedor de nuvem.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Soluções de monitorização
[As soluções de monitorização](insights/solutions.md) no Azure Monitor são conjuntos de lógica embalados que fornecem insights para uma determinada aplicação ou serviço. Incluem lógica para a recolha de dados de monitorização para a aplicação ou serviço, [consultas](log-query/log-query-overview.md) para analisar esses dados e [vistas](./platform/view-designer.md) para visualização. As soluções de monitorização estão [disponíveis da Microsoft](./monitor-reference.md) e parceiros para fornecer monitorização para vários serviços Azure e outras aplicações.

![Soluções de monitorização](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Respondendo a situações críticas
Além de permitir que analise interativamente os dados de monitorização, uma solução de monitorização eficaz deve ser capaz de responder proativamente às condições críticas identificadas nos dados que recolhe. Isto pode estar a enviar um sms ou correio a um administrador responsável pela investigação de um problema. Ou pode lançar um processo automatizado que tenta corrigir uma condição de erro.


### <a name="alerts"></a>Alertas
[Os alertas no Azure Monitor](platform/alerts-overview.md) notificam-no proativamente de condições críticas e potencialmente tentam tomar medidas corretivas. As regras de alerta baseadas em métricas fornecem alertas quase em tempo real com base em valores numéricos, enquanto as regras baseadas em registos permitem uma lógica complexa através de dados de várias fontes.

As regras de alerta no Azure Monitor utilizam grupos de [ação,](platform/action-groups.md)que contêm conjuntos únicos de destinatários e ações que podem ser partilhadas através de várias regras. Com base nos seus requisitos, os grupos de ação podem executar tais ações como usar webhooks para que os alertas iniciem ações externas ou para se integrarem com as suas ferramentas ITSM.

![A screenshot mostra alertas no Azure Monitor com gravidade, alertas totais e outras informações.](media/overview/alerts.png)

### <a name="autoscale"></a>Dimensionamento Automático
A autoescala permite-lhe ter a quantidade certa de recursos a correr para lidar com a carga na sua aplicação. Permite criar regras que utilizem métricas recolhidas pelo Azure Monitor para determinar quando adicionar automaticamente recursos para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão parados. Especifica um número mínimo e máximo de ocorrências e a lógica para quando aumentar ou diminuir recursos.

![O diagrama mostra a escala automática, com vários servidores numa linha marcada como Tempo de Processador > 80% e dois servidores marcados como mínimo, três servidores como capacidade atual e cinco como máximo.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualizar dados de monitorização
[Visualizações](visualizations.md) como gráficos e tabelas são ferramentas eficazes para resumir os dados de monitorização e apresentá-lo a diferentes públicos. O Azure Monitor tem as suas próprias funcionalidades para visualizar dados de monitorização e aproveita outros serviços da Azure para publicá-lo para diferentes públicos.

### <a name="dashboards"></a>Dashboards
[Os dashboards Azure](../azure-portal/azure-portal-dashboards.md) permitem combinar diferentes tipos de dados, incluindo métricas e troncos, num único painel no [portal Azure](https://portal.azure.com). Pode, opcionalmente, partilhar o painel com outros utilizadores do Azure. Elementos em todo o Azure Monitor podem ser adicionados a um dashboard Azure, além da saída de qualquer consulta de registo ou gráfico de métricas. Por exemplo, pode criar um dashboard que combine azulejos que mostrem um gráfico de métricas, uma tabela de registos de atividade, um gráfico de utilização da Application Insights e a saída de uma consulta de registo.

![O Screenshot mostra um Painel Azure, que inclui telhas de aplicação e segurança, juntamente com outras informações personalizáveis.](media/overview/dashboard.png)

### <a name="views"></a>Vistas
[As vistas](./platform/view-designer.md) apresentam visualmente dados de registo no Azure Monitor.  Cada vista inclui um único azulejo que perfura até uma combinação de visualizações, tais como gráficos de barras e linhas, além de listas que resumem dados críticos.  As soluções de monitorização incluem pontos de vista que resumem dados para uma determinada aplicação, e pode criar as suas próprias opiniões para apresentar dados a partir de qualquer consulta de registo. Como outros elementos no Azure Monitor, as vistas podem ser adicionadas aos dashboards Azure.

![A screenshot mostra um azulejo para a solução de monitorização do contentor e a visão detalhada que se abre se selecionar o azulejo.](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) é um serviço de análise de negócios que fornece visualizações interativas em várias fontes de dados e é um meio eficaz de disponibilizar dados a outras pessoas dentro e fora da sua organização. Pode configurar o Power BI para [importar automaticamente dados de registo do Azure Monitor](./platform/powerbi.md) para tirar partido destas visualizações adicionais.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integração e dados de exportação
Muitas vezes terá a obrigação de integrar o Azure Monitor com outros sistemas e de construir soluções personalizadas que utilizem os seus dados de monitorização. Outros serviços da Azure trabalham com o Azure Monitor para proporcionar esta integração.

### <a name="event-hub"></a>Hub de Eventos
[O Azure Event Hubs](../event-hubs/index.yml) é uma plataforma de streaming e serviço de ingestão de eventos que pode transformar e armazenar dados usando qualquer fornecedor de análise em tempo real ou adaptadores de lotação/armazenamento. Utilize os Centros de Eventos para [transmitir dados do Azure Monitor](platform/stream-monitoring-data-event-hubs.md) ao parceiro SIEM e ferramentas de monitorização.


### <a name="logic-apps"></a>Aplicações Lógicas
[A Logic Apps](https://azure.microsoft.com/services/logic-apps) é um serviço que lhe permite automatizar tarefas e processos de negócio utilizando fluxos de trabalho que se integram com diferentes sistemas e serviços. Estão disponíveis atividades que lêem e escrevem métricas e registos no Azure Monitor, o que lhe permite construir fluxos de trabalho integrados com uma variedade de outros sistemas.


### <a name="api"></a>API
Estão disponíveis várias APIs para ler e escrever métricas e registos de e para o Azure Monitor, além de aceder a alertas gerados. Também pode configurar e recuperar alertas. Isto proporciona-lhe possibilidades essencialmente ilimitadas para construir soluções personalizadas que se integram com o Azure Monitor.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:

* [Métricas e registos](platform/data-platform.md) para os dados recolhidos pelo Azure Monitor.
* [Fontes de dados](platform/data-sources.md) para como os diferentes componentes da sua aplicação enviam telemetria.
* [Registar consultas](log-query/log-query-overview.md) para analisar dados recolhidos.
* [Melhores práticas](/azure/architecture/best-practices/monitoring) para monitorizar aplicações e serviços em nuvem.
