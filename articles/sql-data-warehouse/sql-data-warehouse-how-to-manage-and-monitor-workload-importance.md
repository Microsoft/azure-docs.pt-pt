---
title: Gerir e monitorizar a importância da carga de trabalho no Azure SQL Data Warehouse | Documentos da Microsoft
description: Saiba como gerir e monitorizar a importância de nível de pedido.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 30afe1805748012b0a137c865c799580f79d31d8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588649"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Gerir e monitorizar a importância da carga de trabalho no Azure SQL Data Warehouse

Gerir e monitorizar a importância de nível de pedido no Azure SQL Data Warehouse com DMVs e exibições de catálogo.

## <a name="monitor-importance"></a>Importância do monitor

Monitorizar a importância usando a nova coluna de importância na [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) vista de gestão dinâmica.
A seguir a consulta mostra submeter tempo e a hora de início para consultas de monitorização. Reveja a hora de envio e a hora, juntamente com importância para ver como importância influenciou agendamento de início.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Para ver mais como consultas estão a ser agenda, utilize as vistas de catálogo.

## <a name="manage-importance-with-catalog-views"></a>Gerir importância com exibições de catálogo

A vista de catálogo sys.workload_management_workload_classifiers contém informações sobre classificadores na sua instância do Azure SQL Data Warehouse. Para excluir os classificadores de definidos pelo sistema de mensagens em fila que mapeiam para classes de recursos, execute o seguinte código:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A vista de catálogo [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), contém informações sobre os parâmetros utilizados na criação do classificador.  O abaixo mostra de consulta que ExecReportsClassifier foi criada no ```membername``` o parâmetro de valores com ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Resultados da consulta](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Para simplificar a resolução de problemas misclassification, recomendamos que remova mapeamentos de função de classe de recursos à medida que cria classificadores de carga de trabalho. O código a seguir devolve as associações de função de classe de recurso existente. Execute sp_droprolemember para cada ```membername``` retornado da classe de recurso correspondente.
Segue-se um exemplo de verificar a existência antes de remover um classificador de carga de trabalho:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre a classificação, consulte [classificação da carga de trabalho](sql-data-warehouse-workload-classification.md).
- Para obter mais informações sobre a importância, consulte [importância da carga de trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Aceda a configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md)
