---
title: Gerir e monitorizar a importância da carga de trabalho
description: Saiba como gerir e monitorizar a importância do nível de pedido no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fdac8ebc56291292559f6718de5f0092c7d8e063
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350473"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Gerir e monitorizar a importância da carga de trabalho no Azure Synapse Analytics

Gerir e monitorizar a importância do nível de pedido da SQL Analytics no Azure Synapse utilizando DMVs e vistas de catálogo.

## <a name="monitor-importance"></a>Monitorizar a importância

Monitorize a importância utilizando a nova coluna de importância na visão dinâmica de gestão da [sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)
A consulta de monitorização abaixo mostra a hora de submissão e a hora de início para consultas. Reveja o tempo de apresentação e o tempo de início juntamente com a importância para ver a importância do agendamento.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Para ver mais aprofundadamente como as consultas estão a ser agendadas, utilize as vistas do catálogo.

## <a name="manage-importance-with-catalog-views"></a>Gerir a importância com vistas para o catálogo

A vista de catálogo sys.workload_management_workload_classifiers contém informações sobre os classificadores. Para excluir os classificadores definidos pelo sistema que mapeiam as classes de recursos executam o seguinte código:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A vista do catálogo, [sys.workload_management_workload_classifier_details,](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)contém informações sobre os parâmetros utilizados na criação do classificador.  A consulta abaixo mostra que o ExecReportsClassifier foi criado no ```membername``` parâmetro para valores com Relatórios Executivos:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![resultados de consulta](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Para simplificar a resolução de problemas, recomendamos que remova mapeamentos de papéis de classe de recursos à medida que cria classificadores de carga de trabalho. O código abaixo retorna os membros de classe de recursos existentes. Executar sp_droprolemember ```membername``` para cada um devolvido da classe de recursos correspondente.
Abaixo está um exemplo de verificação da existência antes de deixar cair um classificador de carga de trabalho:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre classificação, consulte [classificação da carga de trabalho](sql-data-warehouse-workload-classification.md).
- Para mais informações sobre Importância, consulte [A Importância da Carga de Trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md)
