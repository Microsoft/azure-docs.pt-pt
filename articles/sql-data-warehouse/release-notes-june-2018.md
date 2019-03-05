---
title: Notas de versão de armazém de dados SQL do Azure, Junho de 2018 | Documentos da Microsoft
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
ms.openlocfilehash: accb2b730d5b165e29a1ff050cbb2c0ffa2f379d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317174"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>O que há de novo no Azure SQL Data Warehouse? junho de 2018
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em Junho de 2018. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Pontos de restauro definido pelo utilizador
O SQL Data Warehouse tira automaticamente instantâneos do seu armazém de dados, garantindo um objetivo de ponto de recuperação de oito horas (RPO) a cada 8 horas. Enquanto isso automatizada instantâneos facilidade o fardo da gestão da execução do seu armazém de dados, há a necessidade de criar instantâneos em momentos críticos com base nas necessidades da sua empresa. Por exemplo, um instantâneo antes uma carga significativa de dados ou a implementação de novos scripts para o armazém de dados para ativar um ponto de restauro logo antes da operação. 

Agora suporta o SQL Data Warehouse [pontos de restauro definidas pelo utilizador](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) através da [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) cmdlet.

```PowerShell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Segurança ao nível da coluna
Gerenciamento de acesso a dados e de segurança no seu armazém de dados é crítico para a criação de confiança com seus clientes e parceiros. O SQL Data Warehouse [agora oferece suporte à segurança de nível de coluna (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) que permite-lhe ajustar as permissões para ver dados confidenciais, ao limitar o acesso de utilizador para colunas específicas das tabelas sem ter de redesenhar o seu armazém de dados.

CLS permite-lhe controlar o acesso às colunas de tabela com base no contexto de execução do utilizador ou a respetiva associação a grupos com a norma [concessão](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) instrução T-SQL. A lógica de restrição de acesso está localizada na camada de base de dados em si, em vez de longe os dados em outro aplicativo, simplificando a implementação de segurança geral.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
O [OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) função devolve o nome do esquema de base de dados para objetos no âmbito do esquema. Essa função se tornou comum nas ferramentas ETL quando o objeto a validação do esquema. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Resultados de exemplo**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Suporte para a vista de sys.time_zone_info
O [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) devolve as informações sobre os fusos horários suportados no Azure SQL Data Warehouse.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Resultados de exemplo**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Operações de estatísticas de automática são apresentados no sys.dm_pdw_exec_requests (alteração de comportamento)

Com a introdução da [automática Create Statistics](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics), Azure SQL Data Warehouse irá gerar estatísticas para otimizar a execução da consulta. A versão de Junho de 2018 adiciona a capacidade de monitorar quando estatísticas são geradas automaticamente ao adicionar um registo para o [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) ver sempre que qualquer [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) operação é executada.

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Resultados de exemplo**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

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
[Fórum do Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
