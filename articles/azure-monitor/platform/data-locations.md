---
title: Monitorando locais de dados no Azure Monitor | Microsoft Docs
description: Descreve os diferentes locais em que os dados de monitoramento são armazenados no Azure, incluindo a plataforma de dados Azure Monitor.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 6f6071bc1d3d7514d22658a07810690ec8a8056b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972749"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorando locais de dados no Azure Monitor

O Azure Monitor é baseado em [uma plataforma de dados](data-platform.md) de [logs](data-platform-logs.md) e [métricas](data-platform-metrics.md) , conforme descrito em [Azure monitor plataforma de dados](data-platform.md). No entanto, os dados de monitoramento dos recursos do Azure podem ser gravados em outros locais, seja antes de serem copiados para Azure Monitor ou para oferecer suporte a cenários adicionais. 

## <a name="monitoring-data-locations"></a>Locais de dados de monitoramento

A tabela a seguir identifica os diferentes locais em que os dados de monitoramento no Azure são enviados e os diferentes métodos para acessá-los.

| Location | Descrição | Métodos de acesso |
|:---|:---|:---|:--|
| Métricas de Azure Monitor | O banco de dados de série temporal que é otimizado para analisar data com carimbo de hora. | [Metrics Explorer](metrics-getting-started.md)<br>[API de métricas Azure Monitor](/rest/api/monitor/metrics) |
| Registos do Azure Monitor    | Log Analytics espaço de trabalho baseado no Azure Data Explorer que fornece um avançado mecanismo de análise e uma linguagem de consulta avançada. | [Log Analytics](../log-query/portals.md)<br>[API de Log Analytics](https://dev.loganalytics.io/)<br>[API de Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Registo de atividades | Os dados do log de atividades são mais úteis quando enviados para Azure Monitor logs para analisá-los com outros dados, mas também são coletados por conta própria para que possam ser exibidos diretamente no portal do Azure. | [Azure portal](activity-log-view.md#azure-portal)<br>[API de eventos de Azure Monitor](/rest/api/monitor/eventcategories) |
| Storage do Azure | Algumas fontes de dados serão gravadas diretamente no armazenamento do Azure e exigem configuração para mover dados para logs. Você também pode enviar dados para o armazenamento do Azure para arquivamento e para integração com sistemas externos.  | [Análise de Armazenamento](/rest/api/storageservices/storage-analytics)<br>[Explorador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Explorador de Armazenamento](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Hubs de Eventos | Envie dados para os hubs de eventos do Azure para transmiti-los para outros locais. | [Capturar para armazenamento](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor para VMs | O Azure Monitor para VMs armazena dados de integridade da carga de trabalho em um local personalizado que é usado por sua experiência de monitoramento no portal do Azure. | [Azure portal](../insights/vminsights-overview.md)<br>[API REST do monitor de carga de trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[API REST do Azure Resource Health](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alertas | Alertas criados por Azure Monitor. | [Azure portal](alerts-managing-alert-instances.md)<br>[API REST de gerenciamento de alertas](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Passos seguintes

- Consulte as diferentes fontes de [monitoramento de dados coletadas por Azure monitor](data-sources.md).
- Saiba mais sobre os [tipos de dados de monitoramento coletados por Azure monitor](data-platform.md) e como exibir e analisar esses dados.
