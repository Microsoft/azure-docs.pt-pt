---
title: Notas de versão do Azure SQL Data Warehouse de junho de 2018 | Microsoft Docs
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4348a634fd5b2b33f36d8e79f28caf659b82ccf4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "67626157"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>O que há de novo no Azure SQL Data Warehouse? junho de 2018
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e as alterações que foram introduzidas em junho de 2018. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário
SQL Data Warehouse automaticamente faz instantâneos de seu data warehouse a cada 8 horas, garantindo um RPO (objetivo de ponto de recuperação) de oito horas. Embora esses instantâneos automatizados facilitem a carga de gerenciamento de executar seu data warehouse, há a necessidade de tirar instantâneos em momentos críticos com base em suas necessidades de negócios. Por exemplo, tirar um instantâneo logo antes de um carregamento de dados significativo ou a implantação de novos scripts no data warehouse para habilitar um ponto de restauração logo antes da operação. 

SQL Data Warehouse agora dá suporte a [pontos de restauração definidos pelo usuário](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) por meio do cmdlet [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) .

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Segurança em nível de coluna
O gerenciamento de acesso a dados e segurança em sua data warehouse é essencial para a criação de confiança com seus clientes e parceiros. O SQL Data Warehouse [agora oferece suporte à segurança em nível de coluna (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) que permite que você ajuste as permissões para exibir dados confidenciais, limitando o acesso do usuário a colunas específicas em suas tabelas sem a necessidade de reprojetar o data warehouse.

O CLS permite que você controle o acesso a colunas de tabela com base no contexto de execução do usuário ou na associação de seu grupo usando a instrução T-SQL [Grant](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) padrão. A lógica de restrição de acesso está localizada na própria camada de banco de dados, em vez de fora dos dados em outro aplicativo, simplificando a implementação de segurança geral.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="object_schema_name"></a>OBJECT_SCHEMA_NAME
A função [OBJECT_SCHEMA_NAME ()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) retorna o nome do esquema de banco de dados para objetos com escopo de esquema. Essa função tornou-se comum em ferramentas de ETL ao fazer a validação do esquema de objeto. 

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

## <a name="support-for-the-systime_zone_info-view"></a>Suporte para a exibição sys. time_zone_info
A exibição [Sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) retorna informações sobre os fusos horários com suporte no SQL data warehouse do Azure.

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

## <a name="auto-stats-operations-appear-in-sysdm_pdw_exec_requests-behavior-change"></a>As operações de estatísticas automáticas aparecem em sys. dm _pdw_exec_requests (alteração de comportamento)

Com a introdução das [Estatísticas de criação automática](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic), o Azure SQL data warehouse gerará estatísticas para otimizar a execução da consulta. A versão de junho de 2018 adiciona a capacidade de monitorar quando as estatísticas são geradas automaticamente adicionando um registro à exibição [Sys. dm _pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) sempre que qualquer operação [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) é executada.

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

## <a name="next-steps"></a>Passos seguintes
Agora que você já sabe um pouco sobre SQL Data Warehouse, saiba como [criar rapidamente um SQL data warehouse][create a SQL Data Warehouse]. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] à medida que encontra terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

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
