---
title: Gerir e monitorizar a importância da carga de trabalho
description: Saiba como gerenciar e monitorar a importância do nível de solicitação no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ee9acb873c5118733de142045457028c3f4d5f61
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692706"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Gerenciar e monitorar a importância da carga de trabalho no Azure SQL Data Warehouse

Gerencie e monitore a importância do nível de solicitação no Azure SQL Data Warehouse usando DMVs e exibições de catálogo.

## <a name="monitor-importance"></a>Importância do monitor

Monitore a importância usando a nova coluna importância na exibição de gerenciamento dinâmico [Sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) .
A consulta de monitoramento abaixo mostra o tempo de envio e a hora de início das consultas. Examine a hora de envio e a hora de início junto com a importância para ver como a importância influencia o agendamento.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Para ver mais detalhadamente como as consultas estão sendo agendadas, use as exibições do catálogo.

## <a name="manage-importance-with-catalog-views"></a>Gerenciar importância com exibições de catálogo

A exibição de catálogo sys. workload_management_workload_classifiers contém informações sobre classificadores em sua instância do SQL Data Warehouse do Azure. Para excluir os classificadores definidos pelo sistema que são mapeados para classes de recurso, execute o seguinte código:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A exibição de catálogo, [Sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), contém informações sobre os parâmetros usados na criação do classificador.  A consulta abaixo mostra que ExecReportsClassifier foi criado no parâmetro ```membername``` para valores com ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![resultados da consulta](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Para simplificar a solução de problemas de classificação indesejada, recomendamos que você remova os mapeamentos de função de classe de recurso ao criar classificadores de carga de trabalho. O código a seguir retorna associações de função de classe de recurso existentes. Execute sp_droprolemember para cada ```membername``` retornado da classe de recurso correspondente.
Abaixo está um exemplo de verificação de existência antes de remover um classificador de carga de trabalho:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre classificação, consulte [classificação de carga de trabalho](sql-data-warehouse-workload-classification.md).
- Para obter mais informações sobre importância, consulte [importância da carga de trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md)
