---
title: Configure o grau máximo de paralelismo (MAXDOP)
titleSuffix: Azure SQL Database
description: Saiba mais sobre o grau máximo de paralelismo (MAXDOP).
ms.date: 03/29/2021
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
ms.openlocfilehash: 31ddf15975abdce70ea02b5de64ea5611e7e72b3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112043"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Configure o grau máximo de paralelismo (MAXDOP) na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Este artigo descreve o **grau máximo de paralelismo (MAXDOP)** na Base de Dados Azure SQL e como pode ser configurado. 

> [!NOTE]
> **Este conteúdo está focado na Base de Dados Azure SQL.** O Azure SQL Database baseia-se na versão mais recente e estável do motor de base de dados do Microsoft SQL Server, pelo que grande parte do conteúdo é similar, embora as opções de resolução de problemas e configurações sejam diferentes. Para obter mais informações sobre MAXDOP no SQL Server, consulte [configurar o grau máximo de paralelismo A opção de configuração do servidor](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Descrição Geral
  Na Base de Dados Azure SQL, a definição MAXDOP predefinida para cada nova base de dados única e base de dados de piscina elástica é 8. Isto significa que o motor da base de dados pode executar consultas utilizando vários fios. Ao contrário do SQL Server, onde o MAXDOP em largura do servidor predefinido é 0 (ilimitado), por predefinição novas bases de dados na Base de Dados Azure SQL estão definidas para MAXDOP 8. Este padrão impede a utilização desnecessária de recursos e garante uma experiência consistente do cliente. Não é normalmente necessário configurar ainda mais o MAXDOP em cargas de trabalho da Base de Dados Azure SQL, mas pode proporcionar benefícios como um exercício avançado de afinação de desempenho.

> [!Note]
>   Em setembro de 2020, com base em anos de telemetria no serviço de base de dados Azure SQL [MAXDOP 8 foi escolhido](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528) como padrão para novas bases de dados como um valor ideal para a maior variedade de cargas de trabalho dos clientes. Este incumprimento ajudou a prevenir problemas de desempenho devido ao paralelismo excessivo. Antes disso, a definição padrão para novas bases de dados era MAXDOP 0. A opção de configuração da base de dados MAXDOP não foi alterada para as bases de dados existentes criadas antes de setembro de 2020.

  Em geral, se o motor de base de dados optar por executar uma consulta utilizando o paralelismo, o tempo de execução é mais rápido. No entanto, o paralelismo excessivo pode consumir recursos de processador em excesso sem melhorar o desempenho da consulta. Em escala, o paralelismo excessivo pode afetar negativamente o desempenho da consulta para todas as consultas que executam na mesma instância do motor de base de dados, pelo que definir um limite superior para o paralelismo tem sido um exercício comum de afinação de desempenho nas cargas de trabalho do SQL Server.

  A tabela a seguir descreve o comportamento do motor da base de dados ao executar consultas com diferentes valores MAXDOP:

| MAXDOP | Comportamento | 
|--|--|
| = 1 | O motor de base de dados não executa consultas utilizando vários fios simultâneos. | 
| > 1 | O motor da base de dados estabelece um limite superior para o número de fios paralelos. O motor da base de dados escolhe o número de fios de trabalho extra para utilizar. O número total de fios de trabalhador utilizados para executar uma consulta pode ser superior ao valor MAXDOP especificado. |
| = 0 | O motor de base de dados pode utilizar uma série de fios paralelos com um limite superior dependente do número total de processadores lógicos. O motor da base de dados escolhe o número de fios paralelos a utilizar.| 
| | |
  
##  <a name="considerations"></a><a name="Considerations"></a> Considerações  

-   Na Base de Dados Azure SQL, pode alterar o valor MAXDOP predefinido:
    -   Ao nível da consulta, utilizando a [sugestão de consulta](/sql/t-sql/queries/hints-transact-sql-query) **MAXDOP** .     
    -   Ao nível da base de dados, utilizando a configuração de âmbito da [base de dados](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) **MAXDOP** .

-   Considerações e recomendações MAXDOP do servidor SQL de longa data são aplicáveis à Base de [Dados](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) Azure SQL. 

-   MAXDOP é aplicado por [tarefa](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql). Não é aplicada por [pedido](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ou por consulta. Isto significa que durante uma execução de consulta paralela, um único pedido pode desovar várias tarefas com um limite superior determinado pelo MAXDOP. Para mais informações, consulte a secção *de tarefas paralelas de Agendamento* no Guia de [Arquitetura de Linha e Tarefa.](/sql/relational-databases/thread-and-task-architecture-guide) 
  
-   As operações indexadas que criam ou reconstroem um índice, ou que baixam um índice agrupado, podem ser intensivas em recursos. Pode sobrepor o grau máximo de valor do paralelismo da base de dados para as operações de índice, especificando a opção de índice MAXDOP na `CREATE INDEX` ou `ALTER INDEX` na declaração. O valor MAXDOP é aplicado à declaração no momento da execução e não é armazenado nos metadados de índice. Para obter mais informações, consulte [Configure Parallel Index Operations](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Para além de consultas e operações de índice, a opção de configuração de âmbito de base de dados para MAXDOP também controla o paralelismo da DBCC CHECKTABLE, DBCC CHECKDB e DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Security"></a> Recomendações  

  A alteração do MAXDOP para a base de dados pode ter um grande impacto no desempenho da consulta e na utilização de recursos, tanto positiva como negativa. No entanto, não existe um único valor MAXDOP que seja ideal para todas as cargas de trabalho. As recomendações para a definição do MAXDOP são matizadas e dependem de muitos fatores. 

  Algumas cargas de trabalho em simultâneo podem funcionar melhor com um MAXDOP diferente do que outros. Um MAXDOP devidamente configurado deve reduzir o risco de incidentes de desempenho e disponibilidade e, em alguns casos, reduzir os custos ao ser capaz de evitar a utilização desnecessária de recursos e, assim, reduzir-se a um objetivo de serviço mais baixo.

### <a name="excessive-parallelism"></a>Paralelismo excessivo

  Um MAXDOP mais elevado reduz frequentemente a duração das consultas intensivas de CPU. No entanto, o paralelismo excessivo pode agravar outros desempenhos simultâneos de carga de trabalho, famintos de outras consultas de CPU e recursos de thread operários. Em casos extremos, o paralelismo excessivo pode consumir toda a base de dados ou recursos elásticos da piscina, causando intervalos de consulta, erros e interrupções de aplicação. 

  Recomendamos que os clientes evitem MAXDOP 0 mesmo que não pareça causar problemas atualmente. O paralelismo excessivo torna-se mais problemático quando a CPU e os fios dos trabalhadores recebem pedidos mais simultâneos do que podem ser apoiados pelo objetivo de serviço. Evite MAXDOP 0 para reduzir o risco de potenciais problemas futuros devido ao paralelismo excessivo se uma base de dados for ampliada, ou se as gerações futuras de hardware na Base de Dados Azure SQL fornecerem mais núcleos para o mesmo objetivo de serviço de base de dados.

### <a name="modifying-maxdop"></a>Modificação do MAXDOP 

  Se determinar que uma definição MAXDOP diferente é ideal para a carga de trabalho da Base de Dados Azure SQL, pode utilizar a `ALTER DATABASE SCOPED CONFIGURATION` declaração T-SQL. Por exemplo, consulte os Exemplos utilizando a secção [Transact-SQL](#examples) abaixo. Adicione este passo ao processo de implementação para alterar MAXDOP após a criação da base de dados.

  Se o MAXDOP não padrão beneficiar apenas um subconjunto de consultas na carga de trabalho, pode sobrepor MAXDOP ao nível da consulta adicionando a sugestão OPTION (MAXDOP). Por exemplo, consulte os Exemplos utilizando a secção [Transact-SQL](#examples) abaixo. 

  Teste minuciosamente as alterações de configuração MAXDOP com testes de carga envolvendo cargas de consulta simultânea realistas. 

  O MAXDOP para as réplicas primárias e secundárias pode ser configurado independentemente para tirar partido de diferentes configurações MAXDOP ideais para trabalhos de leitura e leitura. Isto [aplica-se](read-scale-out.md)às réplicas secundárias de escala de leitura Azure SQL, [geo-replicação](active-geo-replication-overview.md)e [Azure SQL Database.](service-tier-hyperscale.md) Por predefinição, todas as réplicas secundárias herdam a configuração MAXDOP da réplica primária.

## <a name="security"></a><a name="Security"></a> Segurança  
  
###  <a name="permissions"></a><a name="Permissions"></a> Permissões  
  A `ALTER DATABASE SCOPED CONFIGURATION` declaração deve ser executada como administrador do servidor, como membro da função de base de dados , ou como utilizador a quem foi concedida a `db_owner` `ALTER ANY DATABASE SCOPED CONFIGURATION` permissão.
 
## <a name="examples"></a>Exemplos   

  Estes exemplos utilizam a mais recente base de dados de **amostras AdventureWorksLT** quando a `SAMPLE` opção é escolhida para uma nova base de dados única da Base de Dados Azure SQL.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Configuração de mira da base de dados MAXDOP   

  Este exemplo mostra como utilizar a declaração [de configuração DO ÂMBITO DE BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para configurar a `max degree of parallelism` opção de `2` . A regulação entra em vigor imediatamente. O cmdlet [PowerShell Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) executa as consultas T-SQL para definir e devolver a configuração de âmbito da base de dados MAXDOP. 

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

1.  Ligue-se à Base de Dados Azure SQL. Não é possível alterar as configurações de âmbito de base de dados na base de dados principal.
  
2.  A partir da barra Standard, **selecione New Consulta.**   
  
3.  Copie e cole o exemplo seguinte na janela de consulta e selecione **Executar**. 


#### <a name="maxdop-database-scoped-configuration"></a>Configuração de mira da base de dados MAXDOP

  Este exemplo mostra como determinar a configuração de uma base de dados MAXDOP atual através da [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) vista do catálogo do sistema.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Este exemplo mostra como utilizar a declaração [de configuração DO ÂMBITO DE BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para configurar a `max degree of parallelism` opção de `8` . A regulação entra em vigor imediatamente.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Este exemplo destina-se a ser utilizado com bases de dados Azure SQL com [réplicas de escala ativadas,](read-scale-out.md) [geo-replicação](active-geo-replication-overview.md)e [réplicas secundárias de hiperescala Azure SQL](service-tier-hyperscale.md)Database . Como exemplo, a réplica primária é definida como uma réplica MAXDOP diferente como a réplica secundária, antecipando que pode haver diferenças entre uma leitura-escrita e uma carga de trabalho apenas de leitura. A `value_for_secondary` coluna contém `sys.database_scoped_configurations` definições para a réplica secundária.

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
