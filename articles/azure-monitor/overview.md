---
title: Visão geral do Monitor Azure | Microsoft Docs
description: Descrição geral dos serviços e das funcionalidades da Microsoft que contribuem para uma estratégia completa de monitorização para os seus serviços e aplicações do Azure.
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 544d6937e412e3e1cfc2cf4e520c02f3f804fc8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047167"
---
# <a name="azure-monitor-overview"></a>Descrição geral do Azure Monitor

O Azure Monitor ajuda-o a maximizar a disponibilidade e desempenho das suas aplicações e serviços. Oferece uma solução abrangente para recolher, analisar e agir em telemetria a partir dos seus ambientes de nuvem e no local. Esta informação ajuda-o a compreender como as suas aplicações estão a executar e a identificar proativamente questões que as afetam e os recursos de que dependem.

Apenas alguns exemplos do que pode fazer com o Azure Monitor incluem:

- Detetar e diagnosticar problemas entre aplicações e dependências com [Insights de Aplicação](app/app-insights-overview.md).
- Correlacionar problemas de infraestrutura com [insights de VM](vm/vminsights-overview.md) e [insights de contentores](containers/container-insights-overview.md).
- Faça a sua análise com [o Log Analytics](logs/log-query-overview.md) para resolução de problemas e diagnósticos profundos.
- Operações de suporte em escala com [alertas inteligentes](alerts/alerts-smartgroups-overview.md) e [ações automatizadas.](alerts/alerts-action-rules.md)
- Criar visualizações com [painéis](visualize/tutorial-logs-dashboards.md) e [livros](visualize/workbooks-overview.md)Azure .
- Recolher dados de [recursos monitorizados](./monitor-reference.md) utilizando [métricas do Monitor Azure](./essentials/data-platform-metrics.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Descrição geral

O seguinte diagrama fornece uma visão geral do Azure Monitor. No centro do diagrama estão as lojas de dados para métricas e registos, que são os dois tipos fundamentais de dados utilizados pelo Azure Monitor. À esquerda estão as [fontes de dados de monitorização](agents/data-sources.md) que povoam estas [lojas de dados.](data-platform.md) À direita estão as diferentes funções que o Azure Monitor desempenha com estes dados recolhidos. Isto inclui ações como análise, alerta e streaming para sistemas externos.

![Descrição geral do Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Plataforma de dados de monitorização

Todos os dados recolhidos pelo Azure Monitor enquadram-se num dos dois tipos fundamentais, [métricas e registos.](data-platform.md) [As métricas](essentials/data-platform-metrics.md) são valores numéricos que descrevem algum aspeto de um sistema num determinado momento do tempo. São leves e capazes de suportar cenários quase em tempo real. [Os registos](logs/data-platform-logs.md) contêm diferentes tipos de dados organizados em registos com diferentes conjuntos de propriedades para cada tipo. A telemetria, como eventos e vestígios, são armazenados como registos para além dos dados de desempenho, para que todos possam ser combinados para análise.

Para muitos recursos Azure, você verá dados recolhidos pelo Azure Monitor na sua página geral no portal Azure. Veja qualquer máquina virtual, por exemplo, e verá vários gráficos que exibem métricas de desempenho. Clique em qualquer um dos gráficos para abrir os dados no [explorador de métricas](essentials/metrics-charts.md) no portal Azure, o que lhe permite traçar os valores de várias métricas ao longo do tempo.  Pode ver os gráficos interativamente ou fixá-los num dashboard para os visualizar com outras visualizações.

![O diagrama mostra dados de métricas que fluem para o Explorador de Métricas para usar em visualizações.](media/overview/metrics.png)

Os dados de registo recolhidos pelo Azure Monitor podem ser [analisados](logs/log-query-overview.md) com consultas para recuperar, consolidar e analisar rapidamente os dados recolhidos.  Pode criar e testar consultas utilizando [o Log Analytics](./logs/log-query-overview.md) no portal Azure. Em seguida, pode analisar diretamente os dados utilizando diferentes ferramentas ou guardar consultas para [utilização](visualizations.md) com visualizações ou [regras de alerta](alerts/alerts-overview.md).

O Azure Monitor usa uma versão da linguagem de [consulta Kusto](/azure/kusto/query/) que é adequada para consultas simples de log, mas também inclui funcionalidades avançadas como agregações, juntas e análise inteligente. Pode aprender rapidamente a linguagem de consulta usando [várias lições.](logs/get-started-queries.md)  É fornecida orientação específica para os utilizadores que já estejam familiarizados com [SQL](/azure/data-explorer/kusto/query/sqlcheatsheet) e [Splunk](/azure/data-explorer/kusto/query/splunk-cheat-sheet).

![O diagrama mostra os dados dos Registos que fluem para o Log Analytics para análise.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Que dados é que o Azure Monitor recolhe?

O Azure Monitor pode recolher dados de [várias fontes](monitor-reference.md). Isto vai desde a sua aplicação, qualquer sistema operativo e serviços em que se baseie, até à própria plataforma. O Azure Monitor recolhe dados de cada um dos seguintes escalões:

- **Dados de monitorização** da aplicação : Dados sobre o desempenho e funcionalidade do código que tenha escrito, independentemente da sua plataforma.
- **Dados de monitorização do SISTEMA do Hóspede**: Dados sobre o sistema operativo em que a sua aplicação está em execução. Pode estar em execução no Azure, noutra cloud ou no local. 
- **Dados de monitorização de recurso do Azure**: Dados sobre o funcionamento de um recurso do Azure.
- **Dados de monitorização da subscrição Azure**: Dados sobre o funcionamento e gestão de uma subscrição da Azure, bem como dados sobre a saúde e funcionamento da própria Azure. 
- **Dados de monitorização do inquilino da Azure**: Dados sobre o funcionamento dos serviços Azure ao nível do inquilino, tais como diretório ativo Azure.

Assim que cria uma subscrição do Azure e começa a adicionar recursos, como máquinas virtuais e aplicações Web, o Azure Monitor começa a recolher dados.  [Os registos de atividade](essentials/platform-logs-overview.md) registam-se quando os recursos são criados ou modificados. [As métricas](data-platform.md) dizem-lhe como o recurso está a funcionar e os recursos que está a consumir. 

Permitir que os [diagnósticos](essentials/platform-logs-overview.md) aumentem os dados que está a recolher no funcionamento interno dos recursos.  [Adicione um agente](agents/agents-overview.md) para calcular recursos para recolher telemetria dos seus sistemas operativos de hóspedes. 

Permitir a monitorização da sua aplicação com [o Application Insights](app/app-insights-overview.md) para recolher informações detalhadas, incluindo visualizações de página, pedidos de aplicação e exceções. Verifique ainda a disponibilidade da sua aplicação configurando um [teste de disponibilidade](app/monitor-web-app-availability.md) para simular o tráfego do utilizador.

### <a name="custom-sources"></a>Fontes personalizadas

O Azure Monitor pode recolher dados de registo de qualquer cliente REST utilizando a [API do Colecionador de Dados](logs/data-collector-api.md). Isto permite-lhe criar cenários de monitorização personalizados e alargar a monitorização a recursos que não expõem a telemetria através de outras fontes.

## <a name="insights"></a>Informações
A monitorização dos dados só é útil se puder aumentar a sua visibilidade no funcionamento do seu ambiente informático. [Os insights](monitor-reference.md#insights-and-core-solutions) fornecem uma experiência de monitorização personalizada para determinados serviços Azure. Requerem uma configuração mínima e aumentam a sua visibilidade no funcionamento de recursos críticos.

### <a name="application-insights"></a>Application Insights
[O Application Insights](app/app-insights-overview.md) monitoriza a disponibilidade, desempenho e utilização das suas aplicações web, quer estejam hospedadas na nuvem ou no local. Aproveita a poderosa plataforma de análise de dados no Azure Monitor para lhe fornecer informações profundas sobre as operações da sua aplicação. Permite diagnosticar erros sem esperar que um utilizador os denuncie. O Application Insights inclui pontos de ligação para uma variedade de ferramentas de desenvolvimento e integra-se com o Visual Studio para suportar os seus processos de DevOps.

![AppInsights](media/overview/app-insights.png)

### <a name="container-insights"></a>Insights de contentores
[Os conhecimentos dos contentores](containers/container-insights-overview.md) monitorizam o desempenho das cargas de trabalho dos contentores que são implantados para os clusters geridos de Kubernetes alojados no Serviço Azure Kubernetes (AKS). Dá-lhe visibilidade de desempenho recolhendo métricas de controladores, nós e recipientes que estão disponíveis em Kubernetes através da API métricas. Os registos do contentor também são recolhidos.  Depois de ativar a monitorização dos clusters Kubernetes, estas métricas e registos são automaticamente recolhidos para si através de uma versão contentorizada do agente Log Analytics para o Linux.

![Saúde dos Contentores](media/overview/container-insights.png)

### <a name="vm-insights"></a>Insights VM
[Os conhecimentos VM](vm/vminsights-overview.md) monitorizam as suas máquinas virtuais Azure (VM) em escala. Analisa o desempenho e a saúde dos seus VMs Windows e Linux e identifica os seus diferentes processos e dependências interligadas em processos externos. A solução inclui suporte para monitorizar o desempenho e as dependências de aplicações para VMs hospedados no local ou outro fornecedor de nuvem.  


![VM Insights](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Respondendo a situações críticas
Além de permitir que analise interativamente os dados de monitorização, uma solução de monitorização eficaz deve ser capaz de responder proativamente às condições críticas identificadas nos dados que recolhe. Isto pode estar a enviar um sms ou correio a um administrador responsável pela investigação de um problema. Ou pode lançar um processo automatizado que tenta corrigir uma condição de erro.


### <a name="alerts"></a>Alertas
[Os alertas no Azure Monitor](alerts/alerts-overview.md) notificam-no proativamente de condições críticas e potencialmente tentam tomar medidas corretivas. As regras de alerta baseadas em métricas fornecem alertas em tempo real quase em tempo real baseados em valores numéricos. As regras baseadas em registos permitem uma lógica complexa entre dados de várias fontes.

As regras de alerta no Azure Monitor utilizam grupos de [ação,](alerts/action-groups.md)que contêm conjuntos únicos de destinatários e ações que podem ser partilhadas através de várias regras. Com base nos seus requisitos, os grupos de ação podem executar tais ações como usar webhooks para que os alertas iniciem ações externas ou para se integrarem com as suas ferramentas ITSM.

![A screenshot mostra alertas no Azure Monitor com gravidade, alertas totais e outras informações.](media/overview/alerts.png)

### <a name="autoscale"></a>Dimensionamento Automático
A autoescala permite-lhe ter a quantidade certa de recursos a correr para lidar com a carga na sua aplicação. Crie regras que utilizem métricas recolhidas pelo Azure Monitor para determinar quando adicionar automaticamente recursos quando a carga aumenta. Poupe dinheiro removendo recursos que estão parados. Especifica um número mínimo e máximo de ocorrências e a lógica para quando aumentar ou diminuir recursos.

![O diagrama mostra a escala automática, com vários servidores numa linha marcada como Tempo de Processador > 80% e dois servidores marcados como mínimo, três servidores como capacidade atual e cinco como máximo.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualizar dados de monitorização
[Visualizações](visualizations.md) como gráficos e tabelas são ferramentas eficazes para resumir os dados de monitorização e apresentá-lo a diferentes públicos. O Azure Monitor tem as suas próprias funcionalidades para visualizar dados de monitorização e aproveita outros serviços da Azure para publicá-lo para diferentes públicos.

### <a name="dashboards"></a>Dashboards
[Os dashboards Azure](../azure-portal/azure-portal-dashboards.md) permitem combinar diferentes tipos de dados num único painel no [portal Azure](https://portal.azure.com). Pode, opcionalmente, partilhar o painel com outros utilizadores do Azure. Adicione a saída de qualquer consulta de registo ou gráfico de métricas a um dashboard Azure. Por exemplo, pode criar um dashboard que combine azulejos que mostrem um gráfico de métricas, uma tabela de registos de atividade, um gráfico de utilização da Application Insights e a saída de uma consulta de registo.

![O Screenshot mostra um Painel Azure, que inclui telhas de aplicação e segurança, juntamente com outras informações personalizáveis.](media/overview/dashboard.png)

### <a name="workbooks"></a>Livros
[Os livros](visualize/workbooks-overview.md) fornecem uma tela flexível para análise de dados e a criação de relatórios visuais ricos no portal Azure. Permitem-lhe aceder a várias fontes de dados de todo o Azure e combiná-las em experiências interativas unificadas. Utilize livros de trabalho fornecidos com Insights ou crie os seus próprios modelos pré-finidos.


![Exemplo de livros de trabalho](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) é um serviço de análise de negócios que fornece visualizações interativas em várias fontes de dados. É um meio eficaz de disponibilizar dados a outros dentro e fora da sua organização. Pode configurar o Power BI para [importar automaticamente dados de registo do Azure Monitor](./visualize/powerbi.md) para tirar partido destas visualizações adicionais.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integração e dados de exportação
Muitas vezes terá a obrigação de integrar o Azure Monitor com outros sistemas e de construir soluções personalizadas que utilizem os seus dados de monitorização. Outros serviços da Azure trabalham com o Azure Monitor para proporcionar esta integração.

### <a name="event-hub"></a>Hub de Eventos
[O Azure Event Hubs](../event-hubs/index.yml) é uma plataforma de streaming e serviço de ingestão de eventos. Pode transformar e armazenar dados utilizando qualquer fornecedor de análise em tempo real ou adaptadores de lote/armazenamento. Utilize os Centros de Eventos para [transmitir dados do Azure Monitor](essentials/stream-monitoring-data-event-hubs.md) ao parceiro SIEM e ferramentas de monitorização.


### <a name="logic-apps"></a>Logic Apps
[A Logic Apps](https://azure.microsoft.com/services/logic-apps) é um serviço que lhe permite automatizar tarefas e processos de negócio utilizando fluxos de trabalho que se integram com diferentes sistemas e serviços. Estão disponíveis atividades que lêem e escrevem métricas e registos no Azure Monitor. Isto permite-lhe construir fluxos de trabalho integrados com uma variedade de outros sistemas.


### <a name="api"></a>API
Estão disponíveis várias APIs para ler e escrever métricas e registos de e para o Azure Monitor, além de aceder a alertas gerados. Também pode configurar e recuperar alertas. Isto proporciona-lhe possibilidades essencialmente ilimitadas para construir soluções personalizadas que se integram com o Azure Monitor.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:

* [Métricas e registos](https://docs.microsoft.com/azure/azure-monitor/data-platform#metrics) para os dados recolhidos pelo Azure Monitor.
* [Fontes de dados](agents/data-sources.md) para como os diferentes componentes da sua aplicação enviam telemetria.
* [Registar consultas](logs/log-query-overview.md) para analisar dados recolhidos.
* [Melhores práticas](/azure/architecture/best-practices/monitoring) para monitorizar aplicações e serviços em nuvem.
