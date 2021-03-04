---
title: Consulta de serviço cruzada entre Azure Monitor e Azure Data Explorer (pré-visualização)
description: Consultar os dados do Azure Data Explorer através das ferramentas Azure Log Analytics vice-versa para se juntar e analisar todos os seus dados num só local.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 7a259af17943643e722633592e53f219726c4437
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031213"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Consulta de serviço cross - Azure Monitor e Azure Data Explorer (Preview)
Criar consultas de serviço cruzada entre [Azure Data Explorer,](/azure/data-explorer/) [Application Insights](../app/app-insights-overview.md)e [Log Analytics](../logs/data-platform-logs.md).
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Consulta de serviço cross-service Azure Monitor e Azure Data Explorer
Esta experiência permite-lhe [criar consultas de serviço cruzada entre o Azure Data Explorer e o Azure Monitor](/azure/data-explorer/query-monitor-data) e criar consultas de serviço cruzada entre o [Azure Monitor e o Azure Data Explorer.](./azure-monitor-data-explorer-proxy.md)

Por exemplo, (consulta do Azure Data Explorer a partir de Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Quando a consulta exterior está consultando uma tabela no espaço de trabalho, e, em seguida, juntando-se com outra tabela em um cluster Azure Data Explorer (neste caso, clustername=help, databasename=samples) usando uma nova função "adx()", como como você pode fazer o mesmo para consultar outro espaço de trabalho a partir de texto de consulta interna.

> [!NOTE]
> * A capacidade de consultar os dados do Azure Monitor do Azure Data Explorer, quer diretamente a partir das ferramentas do cliente do Azure Data Explorer, quer indiretamente através de uma consulta num cluster Azure Data Explorer, encontra-se em modo de pré-visualização.
> * Contacte a equipa [de consulta de serviço cross](mailto:adxproxy@microsoft.com) com quaisquer perguntas.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Consulta exportado dados do Log Analytics a partir da conta de armazenamento Azure Blob

A exportação de dados do Azure Monitor para uma conta de armazenamento Azure permite a retenção de baixo custo e a capacidade de realocar registos para diferentes regiões.

Utilize o Azure Data Explorer para consultar dados que foram exportados dos seus espaços de trabalho Log Analytics. Uma vez configuradas, as tabelas suportadas que são enviadas dos seus espaços de trabalho para uma conta de armazenamento Azure estarão disponíveis como fonte de dados para o Azure Data Explorer. [Consulta dados exportados do Azure Monitor usando Azure Data Explorer (pré-visualização)](../logs/azure-data-explorer-query-storage.md).

[Consulta do Azure Data Explorer do fluxo de armazenamento](media\azure-data-explorer-query-storage\exported-data-query.png)

>[!tip] 
> * Para exportar todos os dados do seu espaço de trabalho Log Analytics para uma conta de armazenamento Azure ou centro de eventos, utilize a funcionalidade de exportação de dados do log Analytics do Monitor Azure. [Consulte a exportação de dados do espaço de trabalho Do Log Analytics no Azure Monitor (pré-visualização)](/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:
* [criar consultas de serviço cruzada entre O Explorador de Dados Azure e monitor Azure](/azure/data-explorer/query-monitor-data). Consulta Azure Monitor dados do Azure Data Explorer
* [criar consultas de serviço cruzada entre O Monitor Azure e Azure Data Explorer](./azure-monitor-data-explorer-proxy.md). Consulta Azure Data Explorer dados do Azure Monitor
* [Log Analytics exporta dados do espaço de trabalho em Azure Monitor (pré-visualização)](/azure/data-explorer/query-monitor-data). Link e consulta Conta de armazenamento Azure Blob com dados exportados do Log Analytics.