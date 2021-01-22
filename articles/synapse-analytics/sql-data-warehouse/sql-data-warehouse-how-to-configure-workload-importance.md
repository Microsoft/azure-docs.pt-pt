---
title: Configure a importância da carga de trabalho para piscina SQL dedicada
description: Saiba como definir a importância do nível de pedido no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 38fb842cf90c110266f53b79a9ab2ef6157025b4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681290"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Configure a importância da carga de trabalho na piscina dedicada SQL para Azure Synapse Analytics

Definir importância na piscina de SQL dedicada para Azure Synapse permite-lhe influenciar o agendamento de consultas. As consultas com maior importância serão agendadas para serem executadas antes de consultas com menor importância. Para atribuir importância às consultas, é necessário criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Criar um classificador de carga de trabalho com importância

Muitas vezes, num cenário de armazém de dados, temos utilizadores, num sistema movimentado, que precisam de executar as suas consultas rapidamente.  O utilizador pode ser executivo da empresa que precisa de executar relatórios ou o utilizador pode ser um analista que executa uma consulta de adhoc. Para atribuir importância, cria-se um classificador de carga de trabalho e a importância é atribuída a uma consulta.  Os exemplos abaixo utilizam a sintaxe  [do classificador de criar carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para criar dois classificadores. `Membername` pode ser um único utilizador ou um grupo.  Para encontrar os utilizadores de piscinas SQL dedicados existentes, corra:

```sql
Select name from sys.sysusers
```

Para criar um classificador de carga de trabalho, para um utilizador com maior importância é executado:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Para criar um classificador de carga de trabalho para um utilizador que executa consultas de adesivo com menor importância:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre a gestão da carga de trabalho, consulte [classificação da carga de trabalho](sql-data-warehouse-workload-classification.md)
- Para mais informações sobre importância, consulte [a Importância da Carga de Trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Vá para gerir e monitorize a importância da carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
