---
title: Gerir e monitorizar a importância da carga de trabalho
description: Aprenda a gerir e monitorizar a importância do nível de pedido no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 43006456142728287ddf4adba1fbb9b45f5ccc89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211974"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Gerir e monitorizar a importância da carga de trabalho no Azure Synapse Analytics

Gerir e monitorizar a importância do nível de pedido synapse em Azure Synapse usando DMVs e vistas de catálogo.

## <a name="monitor-importance"></a>Monitorizar a importância

Monitorize a importância utilizando a nova coluna de importância na visão dinâmica [do sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
A consulta de monitorização abaixo mostra a hora de envio e a hora de início para consultas. Reveja o tempo de envio e a hora de início, juntamente com importância para ver como a importância influenciou o agendamento.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Para aprofundar a forma como as consultas estão a ser agendadas, use as vistas do catálogo.

## <a name="manage-importance-with-catalog-views"></a>Gerir importância com vistas de catálogo

A vista de catálogo sys.workload_management_workload_classifiers contém informações sobre classificadores. Excluir os classificadores definidos pelo sistema que mapeiam para classes de recursos executam o seguinte código:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A vista do catálogo, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), contém informações sobre os parâmetros utilizados na criação do classificador.  A consulta abaixo mostra que o ExecReportsClassifier foi criado no ```membername``` parâmetro para valores com Relatórios Executivos:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![resultados de consulta](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Para simplificar a classificação errada da resolução de problemas, recomendamos que remova mapeamentos de funções de classe de recursos à medida que cria classificadores de carga de trabalho. O código abaixo devolve os membros de funções de classe de recursos existentes. Executar sp_droprolemember para cada ```membername``` um devolvido da classe de recursos correspondente.
Abaixo está um exemplo de verificação da existência antes de deixar cair um classificador de carga de trabalho:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre a classificação, consulte [a Classificação da Carga de Trabalho.](sql-data-warehouse-workload-classification.md)
- Para mais informações sobre importância, consulte [a Importância da Carga de Trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para a Configure Workload Important](sql-data-warehouse-how-to-configure-workload-importance.md)
