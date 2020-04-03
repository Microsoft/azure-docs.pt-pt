---
title: Declarações do T-SQL
description: Links para a documentação para declarações T-SQL suportadas na piscina SYnapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 011a850a44948bf5eee28a40d59dcf7672d866e8
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586375"
---
# <a name="t-sql-statements-supported-in-synapse-sql-pool"></a>Declarações T-SQL suportadas na piscina SYnapse SQL

Links para a documentação para declarações T-SQL suportadas na piscina SYnapse SQL.

## <a name="data-definition-language-ddl-statements"></a>Declarações de Linguagem de Definição de Dados (DDL)

* [ALTERAR BASE DE DADOS](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [ALTERAR VISÃO MATERIALIZADA](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (Pré-visualização) 
* [ALTERAR PROCEDIMENTO](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTER SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [CRIAR ÍNDICE DE LOJA DE COLUNAS](https://msdn.microsoft.com/library/gg492153.aspx)
* [CRIAR BASE DE DADOS](https://msdn.microsoft.com/library/mt204021.aspx)
* [CRIAR BASE DE DADOS CREDENCIAL SCOPED](https://msdn.microsoft.com/library/mt270260.aspx)
* [CRIAR FONTE DE DADOS EXTERNAS](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)
* [CRIAR FUNÇÃO](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [CRIAR VISTA MATERIALIZADA COMO SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (Pré-visualização) 
* [CRIAR PROCEDIMENTO](https://msdn.microsoft.com/library/ms187926.aspx)
* [CRIAR SCHEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CRIAR TABELA](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [CRIAR VISTA](https://msdn.microsoft.com/library/ms187956.aspx)
* [CRIAR CLASSIFIER DE CARGA DE TRABALHO](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [LARGAR FONTE DE DADOS EXTERNOS](https://msdn.microsoft.com/library/mt146367.aspx)
* [DROP FORMATO DE FICHEIRO EXTERNO](https://msdn.microsoft.com/library/mt146379.aspx)
* [TABELA EXTERNA DROP](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [PROCEDIMENTO DE ENTREGA](https://msdn.microsoft.com/library/ms174969.aspx)
* [ESTATÍSTICAS DE QUEDA](https://msdn.microsoft.com/library/ms175075.aspx)
* [MESA DE LANÇAMENTO](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [VISTA DROP](https://msdn.microsoft.com/library/ms173492.aspx)
* [DROP WORKLOAD CLASSIFIER](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [RENOME](https://msdn.microsoft.com/library/mt631611.aspx)
* [SET RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [Tabela TRUNCATE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Declarações de linguagem de manipulação de dados (DML)

* [ELIMINAR](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERIR](https://msdn.microsoft.com/library/ms174335.aspx)
* [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Comandos de Consola de Base de Dados

* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (Pré-visualização)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (Pré-visualização)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Declarações de consulta

* [SELECIONE](https://msdn.microsoft.com/library/ms189499.aspx)
* [COM common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCETO E INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [EXPLICAR](https://msdn.microsoft.com/library/mt631615.aspx)
* [De](https://msdn.microsoft.com/library/ms177634.aspx)
* [Utilização de PIVOT e UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [GRUPO POR](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDENAR POR](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPÇÃO](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNIÃO](https://msdn.microsoft.com/library/ms180026.aspx)
* [ONDE](https://msdn.microsoft.com/library/ms188047.aspx)
* [Início](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasing](https://msdn.microsoft.com/library/mt631614.aspx)
* [Condição de pesquisa](https://msdn.microsoft.com/library/ms173545.aspx)
* [Subqueides](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Declarações de segurança

* Permissões: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY,](https://msdn.microsoft.com/library/ms188338.aspx) [REVOGAÇÃO](https://msdn.microsoft.com/library/ms187728.aspx)
* [ALTERAR AUTORIZAÇÃO](https://msdn.microsoft.com/library/ms187359.aspx)
* [CERTIFICADO DE ALTERAÇÃO](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTERAR CHAVE DE ENCRIPTAÇÃO DA BASE DE DADOS](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTERAR LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTER CHAVE MASTER](https://msdn.microsoft.com/library/ms186937.aspx)
* [ALTERAR O PAPEL](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTERAR UTILIZADOR](https://msdn.microsoft.com/library/ms176060.aspx)
* [CERTIFICADO DE BACKUP](https://msdn.microsoft.com/library/ms178578.aspx)
* [CLOSE MASTER KEY](https://msdn.microsoft.com/library/ms188387.aspx)
* [CRIAR CERTIFICADO](https://msdn.microsoft.com/library/ms187798.aspx)
* [CRIAR CHAVE DE ENCRIPTAÇÃO DE BASE DE DADOS](https://msdn.microsoft.com/library/bb677241.aspx)
* [CRIAR LOGIN](https://msdn.microsoft.com/library/ms189751.aspx)
* [CRIAR CHAVE MESTRE](https://msdn.microsoft.com/library/ms174382.aspx)
* [CRIAR PAPEL](https://msdn.microsoft.com/library/ms187936.aspx)
* [CRIAR O UTILIZADOR](https://msdn.microsoft.com/library/ms173463.aspx)
* [CERTIFICADO DE ENTREGA](https://msdn.microsoft.com/library/ms179906.aspx)
* [CHAVE DE ENCRIPTAÇÃO DROP DATABASE](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [CHAVE MESTRE DROP](https://msdn.microsoft.com/library/ms180071.aspx)
* [PAPEL DE QUEDA](https://msdn.microsoft.com/library/ms174988.aspx)
* [UTILIZADOR DROP](https://msdn.microsoft.com/library/ms189438.aspx)
* [CHAVE MASTER ABERTA](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações de referência, consulte [elementos linguísticos T-SQL na piscina Synapse SQL](sql-data-warehouse-reference-tsql-language-elements.md)e vistas do [sistema na piscina Synapse SQL](sql-data-warehouse-reference-tsql-system-views.md).
