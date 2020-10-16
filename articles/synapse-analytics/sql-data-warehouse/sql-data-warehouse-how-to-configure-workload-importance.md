---
title: Configurar a importância da carga de trabalho
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
ms.openlocfilehash: 83170f4090909e3edcc163312383773d088d8c57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212127"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configure a importância da carga de trabalho no Azure Synapse Analytics

Definir importância no Sinapse SQL para Azure Synapse permite-lhe influenciar o agendamento de consultas. As consultas com maior importância serão agendadas para serem executadas antes de consultas com menor importância. Para atribuir importância às consultas, é necessário criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Criar um classificador de carga de trabalho com importância

Muitas vezes, num cenário de armazém de dados, temos utilizadores, num sistema movimentado, que precisam de executar as suas consultas rapidamente.  O utilizador pode ser executivo da empresa que precisa de executar relatórios ou o utilizador pode ser um analista que executa uma consulta de adhoc. Para atribuir importância, cria-se um classificador de carga de trabalho e a importância é atribuída a uma consulta.  Os exemplos abaixo utilizam a sintaxe  [do classificador de criar carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para criar dois classificadores. `Membername` pode ser um único utilizador ou um grupo.  Para encontrar os utilizadores de armazém de dados existentes, corra:

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
