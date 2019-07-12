---
title: Configure a importância de carga de trabalho no Azure SQL Data Warehouse | Documentos da Microsoft
description: Saiba como configurar a importância de nível de pedido.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e4d410f32068b4d3035dcab0c61b7b9205103690
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588681"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Configure a importância de carga de trabalho no Azure SQL Data Warehouse

A definição de importância no armazém de dados SQL permite-lhe influenciar o agendamento de consultas. As consultas com maior importância irão ser agendadas para ser executada antes de consultas com importância inferior. Para atribuir importância às consultas, terá de criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Criar um classificador de carga de trabalho com importância

Muitas vezes num cenário de armazém de dados tiver utilizadores que necessitam de suas consultas a serem executadas rapidamente.  O utilizador pode ser executivos da empresa que precisam de executar relatórios ou o utilizador podem ser um analista de execução de uma consulta ad hoc. Criar um classificador de carga de trabalho para atribuir importância a uma consulta.  Os exemplos abaixo utilizam o novo [classificador de carga de trabalho de criar](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) sintaxe para criar dois classificadores.  Membername pode ser um único utilizador ou um grupo. Classificações de utilizador individuais têm precedência sobre as classificações de função. Para encontrar utilizadores do armazém de dados existentes, execute:

```sql
Select name from sys.sysusers
```

Para criar um classificador de carga de trabalho, para um utilizador com maior importância execute:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Para criar um classificador de carga de trabalho para um utilizador a executar consultas ad hoc com importância inferior, execute:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Próximos Passos
- Para obter mais informações sobre a gestão da carga de trabalho, consulte [classificação da carga de trabalho](sql-data-warehouse-workload-classification.md)
- Para obter mais informações sobre a importância, consulte [importância da carga de trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Vá para gerir e monitorizar a importância da carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
