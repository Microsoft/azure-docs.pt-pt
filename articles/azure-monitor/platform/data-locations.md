---
title: Monitorização de localizações de dados no Azure Monitor | Documentos da Microsoft
description: Descreve as diferentes localizações onde os dados de monitorização são armazenados no Azure, incluindo a plataforma de dados do Azure Monitor.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416908"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Localizações de dados de monitorização no Azure Monitor

Monitor do Azure se baseia numa [plataforma de dados](data-platform.md) de [registos](data-platform-logs.md) e [métricas](data-platform-metrics.md) conforme descrito no [plataforma de dados do Azure Monitor](data-platform.md). A monitorização dos dados dos recursos do Azure pode ser escrita para outras localizações no entanto, qualquer um antes de eles são copiados para o Azure Monitor ou para dar suporte a cenários adicionais. 

## <a name="monitoring-data-locations"></a>Localizações de dados de monitorização

A tabela seguinte identifica as diferentes localizações onde os dados no Azure de monitorização é enviado e os diferentes métodos para acessá-lo.

| Location | Descrição | Métodos de acesso |
|:---|:---|:---|:--|
| Métricas de Monitor do Azure | Série de tempo base de dados que está otimizada para analisar os dados de horários. | [Explorador de métricas](metrics-getting-started.md)<br>[API de métricas de Monitor do Azure](/rest/api/monitor/metrics) |
| Registos do Azure Monitor    | Inicie a sessão de área de trabalho de análise baseada no Explorador de dados do Azure que fornece uma linguagem de consulta poderosa de análise avançados e de motor. | [Log Analytics](../log-query/portals.md)<br>[API do log Analytics](https://dev.loganalytics.io/)<br>[API do Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Registo de atividades | Dados de registo de Atividades são mais útil quando enviado para os registos do Azure Monitor para analisá-los com outros dados, mas também são recolhido por conta própria, de modo que pode ser exibido diretamente no portal do Azure. | [Portal do Azure](activity-log-view.md#azure-portal)<br>[API de eventos de Monitor do Azure](/rest/api/monitor/eventcategories) |
| Storage do Azure | Algumas origens de dados irão escrever diretamente no armazenamento do Azure e necessita de configuração para mover dados para os registos. Também pode enviar dados para o armazenamento do Azure para arquivar e para integração com sistemas externos.  | [Análise de Armazenamento](/rest/api/storageservices/storage-analytics)<br>[Explorador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Explorador de armazenamento](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Hubs de Eventos | Envie dados para os Hubs de eventos do Azure para transmitir para outros locais. | [Captura para o armazenamento](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor para VMs | Monitor do Azure para VMs armazena dados de estado de funcionamento da carga de trabalho num local personalizado que é utilizado pela sua experiência de monitorização no portal do Azure. | [Portal do Azure](../insights/vminsights-overview.md)<br>[Monitor de carga de trabalho REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[O Azure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alertas | Alertas criados pelo Azure Monitor. | [Portal do Azure](alerts-managing-alert-instances.md)<br>[REST API de gestão de alertas](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Passos Seguintes

- Consulte as origens diferentes das [dados de monitorização recolhidos pelo Azure Monitor](data-sources.md).
- Saiba mais sobre o [tipos de dados recolhidos pelo Azure Monitor](data-platform.md) e como visualizar e analisar estes dados.
