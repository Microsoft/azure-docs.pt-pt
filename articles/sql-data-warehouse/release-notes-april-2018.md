---
title: Notas de versão de armazém de dados SQL do Azure, Abril de 2018 | Documentos da Microsoft
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: a9fae37acd653b05502737756baf463188ef631d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861711"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>O que há de novo no Azure SQL Data Warehouse? Abril de 2018
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em Abril de 2018.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Capacidade para truncar uma partição antes de um comutador
Os clientes utilizam frequentemente como um padrão da mudança de partições para carregar dados de uma tabela para outra, alterando os metadados da tabela por meio do `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` sintaxe. O SQL Data Warehouse não suporta a troca de partição quando a partição de destino contém dados. Se a partição de destino já contiver dados, o cliente precisaria truncar a partição de destino e, em seguida, executar o comutador.

Agora o SQL Data Warehouse suporta esta operação numa única instrução de T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Para obter mais informações, consulte a [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) artigo.

## <a name="improved-query-compilation-performance"></a>Desempenho de compilação de consultas melhorado
O SQL Data Warehouse introduziu um conjunto de alterações para melhorar a etapa de compilação de consulta de consultas distribuídas. Estas alterações melhorar os tempos de compilação de consulta até **10 x** geral reduzindo consultar runtimes de execução. Estas alterações são mais evidentes em armazéns de dados com um grande número de objetos (tabelas, funções, Exibições, procedimentos).

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>Comandos DBCC não consomem ranhuras de simultaneidade (alteração de comportamento)
O SQL Data Warehouse suporta um subconjunto do T-SQL [comandos DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) como [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Anteriormente, esses comandos seriam consumir um [ranhura de simultaneidade](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) reduzindo o número de carregamentos/consultas do utilizador que pode ser executado. O `DBCC` comandos agora são executados numa fila local que não consomem um bloco de recursos, melhorando o desempenho de execução de consulta global.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Mensagem de erro atualizada para literais excessivas (alteração de comportamento)
Anteriormente, o SQL Data Warehouse incluiria uma *aproximado* contar quando uma consulta continha demasiados literais.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

A mensagem de erro foi atualizada para indicar apenas que atingiu o limite literal.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Para obter mais informações, consulte a [consultas](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) secção a [limites de capacidade](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) artigo para obter mais informações sobre os limites máximos.

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Remover a SYS. Vista PDW_DATABASE_MAPPINGS (alteração de comportamento)
Isso `sys.pdw_database_mappings` vista é não utilizada no armazém de dados SQL. Anteriormente, um, SELECIONE desta vista não retornaria nenhum resultado. A vista foi removida. 

## <a name="next-steps"></a>Passos Seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como rapidamente [criar um SQL Data Warehouse][create a SQL Data Warehouse]. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] quando se deparar com terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Fórum do Stack Overflow]
* [Twitter]


[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
