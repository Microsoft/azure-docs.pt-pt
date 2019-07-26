---
title: Instruções T-SQL-Azure SQL Data Warehouse | Microsoft Docs
description: Links para a documentação para instruções T-SQL com suporte no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 5704ee4bf84b396dcef5a4f9edd887128ba95fb1
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479399"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Instruções T-SQL com suporte no Azure SQL Data Warehouse
Links para a documentação para instruções T-SQL com suporte no Azure SQL Data Warehouse.

## <a name="data-definition-language-ddl-statements"></a>Instruções DDL (linguagem de definição de dados)
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [alterar exibição materializada](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) Apresentação 
* [ALTERAR PROCEDIMENTO](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTERAR ESQUEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [CRIAR ÍNDICE COLUMNSTORE](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [CRIAR CREDENCIAL NO ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt270260.aspx)
* [CRIAR FONTE DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)
* [CRIAR FUNÇÃO](https://msdn.microsoft.com/library/mt203952.aspx)
* [CRIAR ÍNDICE](https://msdn.microsoft.com/library/ms188783.aspx)
* [criar exibição materializada como SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) Apresentação 
* [CRIAR PROCEDIMENTO](https://msdn.microsoft.com/library/ms187926.aspx)
* [CRIAR ESQUEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [CRIAR MODO DE EXIBIÇÃO](https://msdn.microsoft.com/library/ms187956.aspx)
* [CRIAR CLASSIFICADOR DE CARGA DE TRABALHO](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [REMOVER FONTE DE DADOS EXTERNA](https://msdn.microsoft.com/library/mt146367.aspx)
* [REMOVER FORMATO DE ARQUIVO EXTERNO](https://msdn.microsoft.com/library/mt146379.aspx)
* [REMOVER TABELA EXTERNA](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [REMOVER PROCEDIMENTO](https://msdn.microsoft.com/library/ms174969.aspx)
* [REMOVER ESTATÍSTICAS](https://msdn.microsoft.com/library/ms175075.aspx)
* [REMOVER TABELA](https://msdn.microsoft.com/library/ms173790.aspx)
* [DESCARTAR ESQUEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [REMOVER EXIBIÇÃO](https://msdn.microsoft.com/library/ms173492.aspx)
* [REMOVER CLASSIFICADOR DE CARGA DE TRABALHO](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [NOME](https://msdn.microsoft.com/library/mt631611.aspx)
* [SET RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Instruções DML (linguagem de manipulação de dados)
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [CUMULATIVO](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Comandos do console de banco de dados
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) Apresentação
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) Apresentação
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Instruções de consulta
* [NÃO](https://msdn.microsoft.com/library/ms189499.aspx)
* [COM common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT e INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [EXPLAIN](https://msdn.microsoft.com/library/mt631615.aspx)
* [FROM](https://msdn.microsoft.com/library/ms177634.aspx)
* [Usando PIVOT e UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [AGRUPAR POR](https://msdn.microsoft.com/library/ms177673.aspx)
* [QUE](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDENAR POR](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNIDA](https://msdn.microsoft.com/library/ms180026.aspx)
* [POSIÇÃO](https://msdn.microsoft.com/library/ms188047.aspx)
* [INÍCIO](https://msdn.microsoft.com/library/ms189463.aspx)
* [Alias](https://msdn.microsoft.com/library/mt631614.aspx)
* [Critério de pesquisa](https://msdn.microsoft.com/library/ms173545.aspx)
* [Subconsultas](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Declarações de segurança
* Permissões: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ALTERAR AUTORIZAÇÃO](https://msdn.microsoft.com/library/ms187359.aspx)
* [ALTERAR CERTIFICADO](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTERAR CHAVE DE CRIPTOGRAFIA DO BANCO DE DADOS](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTERAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms186937.aspx)
* [ALTERAR FUNÇÃO](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTERAR USUÁRIO](https://msdn.microsoft.com/library/ms176060.aspx)
* [CERTIFICADO DE BACKUP](https://msdn.microsoft.com/library/ms178578.aspx)
* [FECHAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms188387.aspx)
* [CRIAR CERTIFICADO](https://msdn.microsoft.com/library/ms187798.aspx)
* [CRIAR CHAVE DE CRIPTOGRAFIA DE BANCO DE DADOS](https://msdn.microsoft.com/library/bb677241.aspx)
* [CRIAR LOGON](https://msdn.microsoft.com/library/ms189751.aspx)
* [CRIAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174382.aspx)
* [CRIAR FUNÇÃO](https://msdn.microsoft.com/library/ms187936.aspx)
* [CRIAR USUÁRIO](https://msdn.microsoft.com/library/ms173463.aspx)
* [REMOVER CERTIFICADO](https://msdn.microsoft.com/library/ms179906.aspx)
* [REMOVER CHAVE DE CRIPTOGRAFIA DO BANCO DE DADOS](https://msdn.microsoft.com/library/bb630256.aspx)
* [REMOVER LOGON](https://msdn.microsoft.com/library/ms188012.aspx)
* [REMOVER CHAVE MESTRA](https://msdn.microsoft.com/library/ms180071.aspx)
* [REMOVER FUNÇÃO](https://msdn.microsoft.com/library/ms174988.aspx)
* [REMOVER USUÁRIO](https://msdn.microsoft.com/library/ms189438.aspx)
* [ABRIR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações de referência, consulte [elementos da linguagem T-SQL no azure SQL data warehouse](sql-data-warehouse-reference-tsql-language-elements.md)e exibições do [sistema no SQL data warehouse do Azure](sql-data-warehouse-reference-tsql-system-views.md).
