---
title: Monitorização de localizações de dados no Monitor Do Azure Microsoft Docs
description: Descreve os diferentes locais onde os dados de monitorização são armazenados em Azure, incluindo a plataforma de dados Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666620"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorização de localizações de dados no Monitor Azure

O Monitor Azure baseia-se numa plataforma de [dados](data-platform.md) de [Registos](data-platform-logs.md) e [Métricas,](data-platform-metrics.md) conforme descrito na plataforma de [dados Do Monitor Do Azure.](data-platform.md) No entanto, os dados de monitorização dos recursos do Azure podem ser escritos para outros locais, quer antes de serem copiados para o Monitor Azure, quer para apoiar cenários adicionais. 

## <a name="monitoring-data-locations"></a>Monitorização de localizações de dados

A tabela que se segue identifica os diferentes locais onde são enviados os dados de monitorização no Azure e os diferentes métodos de acesso aos mesmos.

| Localização | Descrição | Métodos de acesso |
|:---|:---|:---|:--|
| Métricas do Monitor Azure | Base de dados da série de tempo que está otimizada para analisar dados carimbados no tempo. | [Explorador de Métricas](metrics-getting-started.md)<br>[API de Métricas de Monitor Azure](/rest/api/monitor/metrics) |
| Registos do Azure Monitor    | Log Analytics workspace que é baseado no Azure Data Explorer que fornece um poderoso motor de análise e linguagem de consulta rica. | [Log Analytics](../log-query/portals.md)<br>[API de Análise de Log](https://dev.loganalytics.io/)<br>[Insights de aplicação API](https://dev.applicationinsights.io/reference/get-query) |
| Registo de atividades | Os dados do registo de Atividade saem mais úteis quando enviados para o Azure Monitor Logs para analisá-lo com outros dados, mas também é recolhido por si só para que possa ser visualizado diretamente no portal Azure. | [Portal do Azure](activity-log-view.md#azure-portal)<br>[API de eventos de monitor estoque](/rest/api/monitor/eventcategories) |
| Storage do Azure | Algumas fontes de dados escreverão diretamente para o armazenamento do Azure e exigirão a configuração para mover dados para Registos. Também pode enviar dados para o armazenamento azure para arquivamento e integração com sistemas externos.  | [Análise de Armazenamento](/rest/api/storageservices/storage-analytics)<br>[Explorador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Explorador de Armazenamento](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Hubs de Eventos | Envie dados para o Azure Event Hubs para os transmitir para outros locais. | [Captura para Armazenamento](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor para VMs | O Azure Monitor para VMs armazena dados de saúde de carga de trabalho num local personalizado que é utilizado pela sua experiência de monitorização no portal Azure. | [Portal do Azure](../insights/vminsights-overview.md)<br>[Carga de trabalho monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[API DE Saúde de Recursos Azure](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alertas | Alertas criados pelo Azure Monitor. | [Portal do Azure](alerts-managing-alert-instances.md)<br>[Alertas Gestão REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Passos seguintes

- Consulte as diferentes fontes de [monitorização dos dados recolhidos pelo Monitor Azure](data-sources.md).
- Conheça os [tipos de dados de monitorização recolhidos pelo Azure Monitor](data-platform.md) e como visualizar e analisar estes dados.
