---
title: Serviços de Monitorização de Meios
description: Comece aqui para aprender a monitorizar os Serviços de Comunicação Social
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.date: 03/17/2021
ms.openlocfilehash: 783d9e1b4ab86f6580cf3418a0676921aef2db6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598206"
---
# <a name="monitor-media-services"></a>Monitorizar serviços de mídia

Quando você tem aplicações críticas e processos de negócio contando com recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pelos Media Services e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar sobre estes dados.

## <a name="metrics-are-useful"></a>As métricas são úteis

Aqui estão exemplos de como monitorizar as métricas dos Media Services pode ajudá-lo a entender como as suas aplicações estão a funcionar. Algumas questões que podem ser abordadas com as métricas dos Serviços de Comunicação Social são:

- Como monitorizo o meu Standard Streaming Endpoint para saber quando ultrapassei os limites?
- Como é que eu sei se tenho unidades de escala de endpoint de streaming premium suficientes?
- Como posso definir um alerta para saber quando aumentar os meus Pontos Finais de Streaming?
- Como posso alertar para saber quando a saída máxima configurada na conta foi atingida?
- Como posso ver a quebra de pedidos a falhar e o que está a causar a falha?
- Como posso ver quantos pedidos de HLS ou DASH estão a ser retirados do embalador?
- Como posso alertar para saber quando é que atingi o valor limiar dos pedidos falhados?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

Os Media Services criam dados de monitorização utilizando [o Azure Monitor,](https://docs.microsoft.com/azure/azure-monitor/overview)que é um serviço de monitorização de stacks completo em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure, além de recursos em outras nuvens e no local.

Comece a ler o artigo [Monitorizar os recursos do Azure com o Azure Monitor,](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)que descreve os seguintes conceitos:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Recolha de dados configurado
- Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

## <a name="monitoring-data"></a>Monitorizar dados

Os Serviços de Comunicação Social recolhem os mesmos tipos de dados de monitorização que outros recursos da Azure que são descritos na [monitorização de dados a partir de recursos Azure.](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources)

Todos os dados recolhidos pelo Azure Monitor enquadram-se num de dois tipos fundamentais: métricas e registos. Com estes dois tipos pode:

- Visualizar e analisar os dados das métricas utilizando o explorador de métricas.
- Monitorize registos de diagnóstico dos Serviços de Comunicação Social e crie alertas e notificações para os mesmos.
- Com registos pode:
  - Envie-os para o Azure Storage
  - Transmita-os para Azure Event Hubs
  - Exporte-os para Log Analytics
  - Utilizar serviços de terceiros

Consulte o artigo [Monitorização de dados dos Serviços de Comunicação](monitor-media-services-data-reference.md) social referência para informação detalhada sobre as métricas e métricas de registos criadas pelos Media Services.

## <a name="collection-and-routing"></a>Recolha e encaminhamento

*As métricas da plataforma* e o *registo de Atividade* são recolhidos e armazenados automaticamente, mas podem ser encaminhados para outros locais utilizando uma definição de diagnóstico.  

*Os Registos de Recursos* **não** são recolhidos e armazenados até criar uma definição de diagnóstico e encaminhá-los para um ou mais locais.

Consulte o artigo [Crie a definição de diagnóstico para recolher registos e métricas da plataforma em Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) para o processo detalhado de criação de uma definição de diagnóstico utilizando o portal Azure, CLI ou PowerShell.

Quando cria uma definição de diagnóstico, especifica quais as categorias de registos a recolher. As categorias de Serviços de Mídia estão listadas na [referência de dados de monitorização dos Serviços de Comunicação Social.](monitor-media-services-data-reference.md)

## <a name="analyzing-metrics"></a>Análise de métricas

Pode analisar métricas de Serviços de Mídia com métricas de outros serviços Azure utilizando métricas exploradores, abrindo **métricas** a partir do menu **Azure Monitor.** Consulte [o Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) para obter detalhes sobre a utilização desta ferramenta.

Para obter uma lista das métricas recolhidas para serviços de mídia, consulte [a Referência de Dados dos Serviços de Comunicação Social](monitor-media-services-data-reference.md)de Monitorização .

## <a name="analyzing-logs"></a>Análise de registos

Os dados em Registos monitores Azure são armazenados em tabelas onde cada mesa tem o seu próprio conjunto de propriedades únicas.  

Todos os registos de recursos no Azure Monitor têm os mesmos campos seguidos por campos específicos de serviço. O esquema comum é delineado no [esquema de registo de recursos do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema).

O esquema dos registos de recursos dos Serviços de Mídia encontra-se na [Monitorização dos Dados dos Serviços de Comunicação Social.](monitor-media-services-data-reference.md)

O [registo de Atividades](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log) é um registo de plataforma no Azure que fornece informações sobre eventos de nível de subscrição. Pode vê-lo de forma independente ou encaminhá-lo para Registos do Monitor Azure, onde pode fazer consultas muito mais complexas usando o Log Analytics.

Para obter uma lista dos tipos de registos de recursos recolhidos para serviços de mídia, consulte [a referência de dados dos Serviços de Comunicação de Monitorização](monitor-media-services-data-reference.md).

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Por que iria querer usar registos de diagnóstico?

Algumas coisas que pode examinar com registos de diagnóstico são:

- O número de licenças entregues por tipo DRM.
- O número de licenças entregues por apólice.
- Erros por DRM ou tipo de política.
- O número de pedidos de licença não autorizados de clientes.

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas no seu sistema antes que os seus clientes os percebam. Pode definir alertas em [métricas,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview) [registos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)e no registo de [atividades](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts).

As métricas dos Serviços de Comunicação Social são recolhidas a intervalos regulares, quer o valor mude ou não. São úteis para alertar porque podem ser amostrados com frequência. Um alerta pode ser disparado rapidamente com uma lógica relativamente simples.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
