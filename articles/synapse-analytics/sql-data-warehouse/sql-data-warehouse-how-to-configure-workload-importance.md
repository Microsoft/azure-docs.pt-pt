---
title: Configurar a importância da carga de trabalho
description: Saiba como definir a importância do nível de pedido no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9440d5a13973a245320bc465e3997e3cdf414b3f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660318"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configure importância da carga de trabalho no Azure Synapse Analytics

Definir importância no Synapse SQL para O Synapse Azure permite-lhe influenciar o agendamento de consultas. Consultas com maior importância serão agendadas antes de consultas com menor importância. Para atribuir importância a consultas, é necessário criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Criar um Classificador de Carga de Trabalho com Importância

Muitas vezes, num cenário de armazém de dados, temos utilizadores, num sistema movimentado, que precisam de executar as suas consultas rapidamente.  O utilizador pode ser executivo da empresa que precisa de executar relatórios ou o utilizador pode ser um analista a executar uma consulta adhoc. Para atribuir importância, cria-se um classificador de carga de trabalho e a importância é atribuída a uma consulta.  Os exemplos abaixo usam a sintaxe [de classificação de carga](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) de trabalho para criar dois classificadores. `Membername`pode ser um único utilizador ou um grupo.  Para encontrar utilizadores de armazéns de dados existentes, executar:

```sql
Select name from sys.sysusers
```

Para criar um classificador de carga de trabalho, para um utilizador com maior importância executada:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Para criar um classificador de carga de trabalho para um utilizador que executa consultas adhoc com execução de menor importância:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Passos Seguintes

- Para mais informações sobre a gestão da carga de trabalho, consulte [Classificação da Carga de Trabalho](sql-data-warehouse-workload-classification.md)
- Para mais informações sobre Importância, consulte [A Importância da Carga de Trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para gerir e monitorizar a importância da carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
