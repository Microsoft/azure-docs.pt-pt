---
title: Monitorização de localizações de dados no Azure Monitor Microsoft Docs
description: Descreve os diferentes locais onde os dados de monitorização são armazenados no Azure, incluindo a plataforma de dados do Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77666620"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorização de localizações de dados no Azure Monitor

O Azure Monitor baseia-se numa plataforma de [dados](data-platform.md) de [Logs](data-platform-logs.md) e [Metrics,](data-platform-metrics.md) conforme descrito na [plataforma de dados do Azure Monitor](data-platform.md). No entanto, os dados de monitorização dos recursos Azure podem ser escritos para outros locais, quer antes de serem copiados para o Azure Monitor, quer para suportar cenários adicionais. 

## <a name="monitoring-data-locations"></a>Monitorização de localizações de dados

A tabela seguinte identifica os diferentes locais onde são enviados os dados de monitorização em Azure e os diferentes métodos de acesso aos mesmos.

| Localização | Descrição | Métodos de acesso |
|:---|:---|:---|:--|
| Métricas do Monitor Azure | Base de dados de séries de tempo que é otimizada para analisar dados com carimbo temporal. | [Explorador de Métricas](metrics-getting-started.md)<br>[Azure Monitor Métricas API](/rest/api/monitor/metrics) |
| Registos do Azure Monitor    | Log Analytics espaço de trabalho que é baseado no Azure Data Explorer que fornece um poderoso motor de análise e uma linguagem de consulta rica. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Insights de Aplicação API](https://dev.applicationinsights.io/reference/get-query) |
| Registo de atividades | Os dados do registo de Atividades são mais úteis quando enviados para os Registos do Monitor Azure para analisá-los com outros dados, mas também são recolhidos por si só para que possa ser visualizado diretamente no portal Azure. | [Portal do Azure](activity-log-view.md#azure-portal)<br>[Azure Monitor Eventos API](/rest/api/monitor/eventcategories) |
| Storage do Azure | Algumas fontes de dados escreverão diretamente para o armazenamento do Azure e requerem configuração para mover dados em Logs. Também pode enviar dados para o armazenamento do Azure para arquivar e para integração com sistemas externos.  | [Análise de Armazenamento](/rest/api/storageservices/storage-analytics)<br>[Explorador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Explorador de Armazenamento](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Hubs de Eventos | Envie dados para a Azure Event Hubs para transmiti-lo para outros locais. | [Captura para armazenamento](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor para VMs | O Azure Monitor para VMs armazena dados de saúde de carga de trabalho num local personalizado que é usado pela sua experiência de monitorização no portal Azure. | [Portal do Azure](../insights/vminsights-overview.md)<br>[Monitor de carga de trabalho REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alertas | Alertas criados pelo Azure Monitor. | [Portal do Azure](alerts-managing-alert-instances.md)<br>[Alertas Gestão REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Próximos passos

- Consulte as diferentes fontes de dados de [monitorização recolhidas pelo Azure Monitor](data-sources.md).
- Conheça os [tipos de dados de monitorização recolhidos pelo Azure Monitor](data-platform.md) e como visualizar e analisar estes dados.
