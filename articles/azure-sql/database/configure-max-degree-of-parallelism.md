---
title: Configure o grau máximo de paralelismo (MAXDOP)
titleSuffix: Azure SQL Database
description: Saiba mais sobre o grau máximo de paralelismo (MAXDOP).
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: c9b8e916c82a42df7addb3c49b4452c0eb403023
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536914"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Configure o grau máximo de paralelismo (MAXDOP) na Base de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Este artigo descreve o grau máximo de definição **de paralelismo (MAXDOP)** na Base de Dados Azure SQL. 

> [!NOTE]
> **Este conteúdo está focado na Base de Dados Azure SQL.** O Azure SQL Database baseia-se na versão mais recente e estável do motor de base de dados do Microsoft SQL Server, pelo que grande parte do conteúdo é similar, embora as opções de resolução de problemas e configurações sejam diferentes. Para obter mais informações sobre MAXDOP no SQL Server, consulte [configurar o grau máximo de paralelismo A opção de configuração do servidor](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Descrição Geral
  O MAXDOP controla o paralelismo intra-consulta no motor da base de dados. Valores MAXDOP mais elevados geralmente resultam em fios mais paralelos por consulta, e execução de consulta mais rápida. 

  Na Base de Dados Azure SQL, a definição MAXDOP predefinida para cada nova base de dados única e base de dados de piscina elástica é 8. Este padrão impede a utilização desnecessária de recursos, ao mesmo tempo que permite que o motor da base de dados execute consultas mais rapidamente utilizando fios paralelos. Não é normalmente necessário configurar maxDOP em cargas de trabalho de base de dados Azure SQL, embora possa proporcionar benefícios como um exercício avançado de afinação de desempenho.

> [!Note]
>   Em setembro de 2020, com base em anos de telemetria no serviço de base de dados Azure SQL MAXDOP 8 foi feito o [padrão para novas bases de dados,](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)como o valor ideal para a maior variedade de cargas de trabalho dos clientes. Este padrão ajudou a prevenir problemas de desempenho devido ao paralelismo excessivo. Antes disso, a definição padrão para novas bases de dados era MAXDOP 0. O MAXDOP não foi automaticamente alterado para as bases de dados existentes criadas antes de setembro de 2020.

  Em geral, se o motor de base de dados optar por executar uma consulta utilizando o paralelismo, o tempo de execução é mais rápido. No entanto, o paralelismo excessivo pode consumir recursos adicionais do processador sem melhorar o desempenho da consulta. Em escala, o paralelismo excessivo pode afetar negativamente o desempenho da consulta para todas as consultas executadas na mesma caixa de dados do motor. Tradicionalmente, definir um limite superior para o paralelismo tem sido um exercício comum de afinação de desempenho nas cargas de trabalho do SQL Server.

  A tabela a seguir descreve o comportamento do motor da base de dados ao executar consultas com diferentes valores MAXDOP:

| MAXDOP | Comportamento | 
|--|--|
| = 1 | O motor da base de dados usa um único fio em série para executar consultas. Não são utilizados fios paralelos. | 
| > 1 | O motor de base de dados define o número de [programadores](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) adicionais a utilizar por fios paralelos ao valor MAXDOP, ou o número total de processadores lógicos, o que for menor. |
| = 0 | O motor de base de dados define o número de [programadores](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) adicionais a utilizar por fios paralelos para o número total de processadores lógicos ou 64, o que for menor. | 
| | |

> [!Note]
> Cada consulta executa com pelo menos um agendador, e um fio de trabalhador no horário.
>
> Uma consulta executada com paralelismo usa agendadores adicionais, e fios paralelos adicionais. Como várias linhas paralelas podem ser executadas no mesmo programador, o número total de fios utilizados para executar uma consulta pode ser superior ao valor MAXDOP especificado ou ao número total de processadores lógicos. Para obter mais informações, consulte [agendar tarefas paralelas.](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)

##  <a name="considerations"></a><a name="Considerations"></a> Considerações  

-   Na Base de Dados Azure SQL, pode alterar o valor MAXDOP predefinido:
    -   Ao nível da consulta, utilizando a [sugestão de consulta](/sql/t-sql/queries/hints-transact-sql-query) **MAXDOP** .     
    -   Ao nível da base de dados, utilizando a configuração de âmbito da [base de dados](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) **MAXDOP** .

-   Considerações e recomendações MAXDOP do servidor SQL de longa data são aplicáveis à Base de [Dados](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) Azure SQL. 

-   As operações indexadas que criam ou reconstroem um índice, ou que baixam um índice agrupado, podem ser intensivas em recursos. Pode sobrepor o valor MAXDOP da base de dados para operações de índice especificando a opção de índice MAXDOP na `CREATE INDEX` ou `ALTER INDEX` na declaração. O valor MAXDOP é aplicado à declaração no momento da execução e não é armazenado nos metadados de índice. Para obter mais informações, consulte [Configure Parallel Index Operations](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Para além de consultas e operações de índice, a opção de configuração de âmbito de base de dados para MAXDOP também controla o paralelismo de outras declarações que podem utilizar execuções paralelas, tais como DBCC CHECKTABLE, DBCC CHECKDB e DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Recomendações  

  A alteração do MAXDOP para a base de dados pode ter um grande impacto no desempenho da consulta e na utilização de recursos, tanto positiva como negativa. No entanto, não existe um único valor MAXDOP que seja ideal para todas as cargas de trabalho. As [recomendações](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) para a definição do MAXDOP são matizadas e dependem de muitos fatores. 

  Algumas cargas de trabalho em simultâneo podem funcionar melhor com um MAXDOP diferente do que outros. Um MAXDOP devidamente configurado deve reduzir o risco de incidentes de desempenho e disponibilidade, e em alguns casos pode reduzir os custos ao ser capaz de evitar a utilização desnecessária de recursos, e assim reduzir-se a um objetivo de serviço mais baixo.

### <a name="excessive-parallelism"></a>Paralelismo excessivo

  Um MAXDOP mais elevado reduz frequentemente a duração das consultas intensivas de CPU. No entanto, o paralelismo excessivo pode agravar outros desempenhos simultâneos de carga de trabalho, famintos de outras consultas de CPU e recursos de thread operários. Em casos extremos, o paralelismo excessivo pode consumir toda a base de dados ou recursos elásticos da piscina, causando intervalos de consulta, erros e interrupções de aplicação. 

> [!Tip]
> Recomendamos que os clientes evitem configurar MAXDOP para 0 mesmo que não pareça causar problemas atualmente.

  O paralelismo excessivo torna-se mais problemático quando há pedidos mais simultâneos do que os que podem ser apoiados pela CPU e pelos recursos de thread dos trabalhadores fornecidos pelo objetivo de serviço. Evite MAXDOP 0 para reduzir o risco de potenciais problemas futuros devido ao paralelismo excessivo se uma base de dados for ampliada, ou se as gerações futuras de hardware na Base de Dados Azure SQL fornecerem mais núcleos para o mesmo objetivo de serviço de base de dados.

### <a name="modifying-maxdop"></a>Modificação do MAXDOP 

  Se determinar que uma definição MAXDOP diferente do padrão é ideal para a sua carga de trabalho de Base de Dados Azure SQL, pode utilizar a `ALTER DATABASE SCOPED CONFIGURATION` declaração T-SQL. Por exemplo, consulte os Exemplos utilizando a secção [Transact-SQL](#examples) abaixo. Para alterar o MAXDOP para um valor não padrão para cada nova base de dados que criar, adicione este passo ao processo de implementação da sua base de dados.

  Se o MAXDOP não padrão beneficiar apenas um pequeno subconjunto de consultas na carga de trabalho, pode sobrepor-se ao MAXDOP ao nível da consulta adicionando a sugestão OPTION (MAXDOP). Por exemplo, consulte os Exemplos utilizando a secção [Transact-SQL](#examples) abaixo. 

  Teste minuciosamente as alterações de configuração MAXDOP com testes de carga envolvendo cargas de consulta simultânea realistas. 

  MAXDOP para as réplicas primárias e secundárias pode ser configurado independentemente se diferentes configurações MAXDOP forem ideais para as suas cargas de trabalho de leitura e leitura. Isto aplica-se a Azure SQL Database [ler escala-out,](read-scale-out.md) [geo-replicação](active-geo-replication-overview.md)e réplicas secundárias [de Hiperescala.](service-tier-hyperscale.md) Por predefinição, todas as réplicas secundárias herdam a configuração MAXDOP da réplica primária.

## <a name="security"></a><a name="Security"></a> Segurança  
  
###  <a name="permissions"></a><a name="Permissions"></a> Permissões  
  A `ALTER DATABASE SCOPED CONFIGURATION` declaração deve ser executada como administrador do servidor, como membro da função de base de dados , ou como utilizador a quem foi concedida a `db_owner` `ALTER ANY DATABASE SCOPED CONFIGURATION` permissão.
 
## <a name="examples"></a>Exemplos   

  Estes exemplos utilizam a mais recente base de dados de **amostras AdventureWorksLT** quando a `SAMPLE` opção é escolhida para uma nova base de dados única da Base de Dados Azure SQL.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Configuração de mira da base de dados MAXDOP   

  Este exemplo mostra como utilizar a declaração [de configuração DO ÂMBITO DE BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para definir `MAXDOP` a configuração para `2` . A definição entra em vigor imediatamente para novas consultas. O cmdlet [PowerShell Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) executa as consultas T-SQL para definir e devolver a configuração de âmbito da base de dados MAXDOP. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Este exemplo destina-se a ser utilizado com bases de dados Azure SQL com [réplicas de escala ativadas,](read-scale-out.md) [geo-replicação](active-geo-replication-overview.md)e [réplicas secundárias de hiperescala Azure SQL Database](service-tier-hyperscale.md). Como exemplo, a réplica primária é definida como uma réplica MAXDOP diferente como a réplica secundária, antecipando que pode haver diferenças entre uma leitura-escrita e uma carga de trabalho apenas de leitura.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  Pode utilizar o [editor de consulta do portal Azure,](connect-query-portal.md)o [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)ou [o Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) para executar consultas T-SQL contra a sua Base de Dados Azure SQL.

1.  Abra uma nova janela de consulta.

2.  Ligue-se à base de dados onde pretende alterar o MAXDOP. Não é possível alterar as configurações de âmbito de base de dados na base de dados principal.
  
3.  Copie e cole o exemplo seguinte na janela de consulta e selecione **Executar**. 

#### <a name="maxdop-database-scoped-configuration"></a>Configuração de mira da base de dados MAXDOP

  Este exemplo mostra como determinar a configuração de uma base de dados MAXDOP atual através da [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) vista do catálogo do sistema.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Este exemplo mostra como utilizar a declaração [de configuração DO ÂMBITO DE BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para definir `MAXDOP` a configuração para `8` . A regulação entra em vigor imediatamente.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Este exemplo destina-se a ser utilizado com bases de dados Azure SQL com [réplicas de escala ativadas,](read-scale-out.md) [geo-replicação](active-geo-replication-overview.md)e réplicas secundárias [de Hiperescala.](service-tier-hyperscale.md) Como exemplo, a réplica primária é definida para um MAXDOP diferente da réplica secundária, antecipando que pode haver diferenças entre as cargas de trabalho de leitura e leitura. Todas as declarações são executadas na réplica primária. A `value_for_secondary` coluna contém `sys.database_scoped_configurations` definições para a réplica secundária.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>Dica de consulta MAXDOP

  Este exemplo mostra como executar uma consulta utilizando a sugestão de consulta para forçar o `max degree of parallelism` `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>Opção de índice MAXDOP

  Este exemplo mostra como reconstruir um índice usando a opção de índice para forçar `max degree of parallelism` a `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Ver também  
* [ALTERAR CONFIGURAÇÃO SCOPED &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Configure operações de índice paralelo](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Sugestões de consulta &#40;&#41;Transact-SQL ](/sql/t-sql/queries/hints-transact-sql-query)     
* [Definir opções de índice](/sql/relational-databases/indexes/set-index-options)     
* [Compreender e resolver problemas de bloqueio da base de dados Azure SQL](understand-resolve-blocking.md)

## <a name="next-steps"></a>Passos seguintes

* [Monitor e Sintonização para Desempenho](/sql/relational-databases/performance/monitor-and-tune-for-performance)
