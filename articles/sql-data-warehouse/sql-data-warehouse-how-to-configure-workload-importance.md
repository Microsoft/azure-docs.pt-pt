---
title: Configurar a importância da carga de trabalho
description: Saiba como definir a importância do nível de solicitação.
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
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692685"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Configurar a importância da carga de trabalho no Azure SQL Data Warehouse

A definição da importância no SQL Data Warehouse permite que você influencie o agendamento de consultas. As consultas com maior importância serão agendadas para serem executadas antes das consultas com menor importância. Para atribuir importância a consultas, você precisa criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Criar um classificador de carga de trabalho com importância

Geralmente, em um cenário de data warehouse, você tem usuários que precisam de suas consultas para serem executados rapidamente.  O usuário poderia ser executivos da empresa que precisam executar relatórios ou o usuário pode ser um analista executando uma consulta adhoc. Você cria um classificador de carga de trabalho para atribuir importância a uma consulta.  Os exemplos a seguir usam a nova sintaxe [criar classificação de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) para criar dois classificadores.  MemberName pode ser um único usuário ou um grupo. As classificações de usuário individuais têm precedência sobre as classificações de função. Para localizar usuários existentes do data warehouse, execute:

```sql
Select name from sys.sysusers
```

Para criar um classificador de carga de trabalho, para um usuário com maior importância de execução:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Para criar um classificador de carga de trabalho para um usuário executando consultas adhoc com menor importância de execução:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Próximos Passos
- Para obter mais informações sobre o gerenciamento de carga de trabalho, consulte [classificação de carga de trabalho](sql-data-warehouse-workload-classification.md)
- Para obter mais informações sobre importância, consulte [importância da carga de trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para gerenciar e monitorar a importância da carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
