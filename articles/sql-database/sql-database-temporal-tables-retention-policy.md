---
title: Gerir dados históricos em Tabelas Temporais
description: Aprenda a usar a política de retenção temporal para manter os dados históricos sob o seu controlo.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 3c2460c6f5e0905f45106148ecc3e8a949cf221f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820691"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gerir dados históricos em Tabelas Temporais com política de retenção

As Tabelas Temporais podem aumentar o tamanho da base de dados mais do que as tabelas normais, especialmente se conservar dados históricos por um período de tempo mais longo. Por conseguinte, a política de retenção de dados históricos é um aspeto importante do planeamento e gestão do ciclo de vida de cada tabela temporal. As Tabelas Temporais na Base de Dados SQL Azure vêm com um mecanismo de retenção fácil de usar que o ajuda a realizar esta tarefa.

A retenção do histórico temporal pode ser configurada ao nível individual da tabela, o que permite aos utilizadores criar polícias de envelhecimento flexível. A aplicação da retenção temporal é simples: requer apenas um parâmetro a definir durante a criação de mesa ou a alteração do esquema.

Depois de definir a política de retenção, a Base de Dados Azure SQL começa a verificar regularmente se existem linhas históricas elegíveis para limpeza automática de dados. A identificação das linhas correspondentes e a sua remoção da tabela de história ocorrem de forma transparente, na tarefa de fundo que é programada e executada pelo sistema. A condição de idade para as linhas de tabela de história é verificada com base na coluna que representa o final do período de SYSTEM_TIME. Se o período de retenção, por exemplo, for fixado para seis meses, as linhas de mesa elegíveis para limpeza satisfazem a seguinte condição:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

No exemplo anterior, assumimos que a coluna **ValidTo** corresponde ao final do período SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Como configurar a política de retenção

Antes de configurar a política de retenção para uma tabela temporal, verifique primeiro se a retenção histórica temporal está ativada *ao nível da base*de dados .

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

A **is_temporal_history_retention_enabled de** bandeira de base de dados está definida para ON por padrão, mas os utilizadores podem alterá-lo com a declaração alter DATABASE. Também é automaticamente definido para OFF após [o ponto no tempo restabelecer](sql-database-recovery-using-backups.md) o funcionamento. Para permitir a limpeza da conservação do histórico temporal para a sua base de dados, execute a seguinte declaração:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Pode configurar a retenção para tabelas temporais mesmo **que is_temporal_history_retention_enabled** esteja DESLIGADO, mas a limpeza automática para as filas envelhecidas não é desencadeada nesse caso.

A política de retenção é configurada durante a criação de tabelas, especificando o valor para o parâmetro HISTORY_RETENTION_PERIOD:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

A Base de Dados Azure SQL permite-lhe especificar o período de retenção utilizando diferentes unidades de tempo: DIAS, SEMANAS, MESES e ANOS. Se HISTORY_RETENTION_PERIOD for omitida, assume-se a retenção INFINITE. Também pode utilizar explicitamente a palavra-chave INFINITE.

Em alguns cenários, é possível configurar a retenção após a criação de tabelas, ou alterar o valor previamente configurado. Nesse caso, utilize a declaração ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> A definição de SYSTEM_VERSIONING para OFF *não preserva* o valor do período de retenção. A definição SYSTEM_VERSIONING on sem HISTORY_RETENTION_PERIOD especificada resulta explicitamente no período de retenção INFINITE.

Para rever o estado atual da política de retenção, utilize a seguinte consulta que une a bandeira de segurança temporal ao nível da base de dados com períodos de retenção para tabelas individuais:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```


## <a name="how-sql-database-deletes-aged-rows"></a>Como a Base de Dados SQL elimina as filas envelhecidas

O processo de limpeza depende do layout do índice da tabela de história. É importante notar que *apenas tabelas de história com um índice agrupado (árvore B ou loja de colunas) podem ter uma política de retenção finita configurada*. Uma tarefa de fundo é criada para realizar limpeza de dados envelhecidos para todas as tabelas temporais com período de retenção finita.
A lógica de limpeza para o índice agrupado (Árvore B) elimina a linha envelhecida em pedaços menores (até 10K) minimizando a pressão no registo de bases de dados e no subsistema IO. Embora a lógica de limpeza utilize o índice b-tree necessário, a ordem de supressão para as linhas mais antigas do que o período de retenção não pode ser firmemente garantida. Por isso, *não assuma qualquer dependência da ordem de limpeza nas suas aplicações.*

A tarefa de limpeza para a loja de colunas agrupada remove [grupos inteiros](https://msdn.microsoft.com/library/gg492088.aspx) de fila ao mesmo tempo (normalmente contêm 1 milhão de linhas cada), o que é muito eficiente, especialmente quando os dados históricos são gerados a um ritmo elevado.

![Retenção de lojas de colunas agrupadas](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Excelente compressão de dados e limpeza eficiente da retenção faz do índice de colunas agrupadas uma escolha perfeita para cenários quando a sua carga de trabalho gera rapidamente uma elevada quantidade de dados históricos. Este padrão é típico para cargas de trabalho intensivas de [processamento de transações que utilizam tabelas temporais](https://msdn.microsoft.com/library/mt631669.aspx) para o rastreio e auditoria de alterações, análise de tendências ou ingestão de dados ioT.

## <a name="index-considerations"></a>Considerações de índice

A tarefa de limpeza das tabelas com índice agrupado de rowstore requer que o índice comece com a coluna correspondente ao final do período de SYSTEM_TIME. Se tal índice não existir, não pode configurar um período de retenção finita:

*Msg 13765, Nível 16, Estado 1 <br> </br> Definição do período de retenção finita falhou na tabela temporal versina 'temporalstagetestdb.dbo.WebsiteUserInfo' porque a tabela de história 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' não contém índice clusterizado exigido. Considere criar uma loja de colunas agrupada ou índice de árvore B a partir da coluna que corresponde ao final do período de SYSTEM_TIME, na tabela de história.*

É importante notar que a tabela de história sql definida pela Azure SQL Base de Dados já tem um índice agrupado, que está em conformidade com a política de retenção. Se tentar remover esse índice numa tabela com período de retenção finita, o funcionamento falha com o seguinte erro:

*Msg 13766, Nível 16, Estado 1 <br> </br> Não pode deixar cair o índice agrupado 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' porque está a ser usado para limpeza automática de dados envelhecidos. Considere colocar HISTORY_RETENTION_PERIOD ao INFINITE na tabela temporal versão do sistema correspondente se precisar de deixar cair este índice.*

A limpeza do índice de colunas agrupadas funciona da melhor forma se as linhas históricas forem inseridas na ordem ascendente (ordenada pelo final da coluna do período), o que é sempre o caso quando a tabela de história é povoada exclusivamente pelo mecanismo SYSTEM_VERSIONIOING. Se as filas na tabela de história não forem encomendadas por coluna de fim de período (o que pode acontecer se migrar dados históricos existentes), deve recriar o índice de lojas de colunas agrupadas em cima do índice de loja de remo de árvore B que é devidamente encomendado, para alcançar o ideal desempenho.

Evite reconstruir o índice de colunas agrupadas na tabela de história com o período de retenção finita, pois pode alterar a encomenda nos grupos de linhas naturalmente impostas pela operação de versão do sistema. Se precisar de reconstruir o índice de lojas de colunas agrupadas na tabela de história, faça-o recriando-o em cima do índice b-tree compatível, preservando a encomenda nos grupos de remo necessários para a limpeza regular de dados. A mesma abordagem deve ser tomada se criar uma tabela temporal com uma tabela de história existente que tenha agrupado o índice de colunas sem ordem de dados garantida:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Quando o período de retenção finita é configurado para a tabela de história com o índice de colunas agrupadas, não é possível criar índices adicionais de árvores B não agrupados nessa tabela:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Uma tentativa de executar acima da declaração falha com o seguinte erro:

*Msg 13772, Nível 16, Estado 1 <br> </br> Não Pode criar um índice não agrupado numa tabela de história temporal 'WebsiteUserInfoHistory', uma vez que tem um período de retenção finita e um índice de colunas agrupado definido.*

## <a name="querying-tables-with-retention-policy"></a>Tabelas de consulta com política de retenção

Todas as consultas na tabela temporal filtram automaticamente linhas históricas correspondentes à política de retenção finita, para evitar resultados imprevisíveis e inconsistentes, uma vez que as filas envelhecidas podem ser eliminadas pela tarefa de limpeza, *em qualquer momento e por ordem arbitrária.*

A imagem seguinte mostra o plano de consulta para uma simples consulta:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

O plano de consulta inclui filtro adicional aplicado à coluna final do período (ValidTo) no operador de digitalização de índice clustered na tabela de histórico (realçado). Este exemplo pressupõe que um período de retenção de um mês foi definido na tabela WebsiteUserInfo.

![Filtro de consulta de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

No entanto, se consultar diretamente a tabela de história, poderá ver linhas mais antigas do que o período de retenção especificado, mas sem qualquer garantia para resultados de consulta repetível. A imagem seguinte mostra o plano de execução da consulta na tabela de história sem filtros adicionais aplicados:

![Consulta da história sem filtro de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Não confie na sua lógica de negócio na leitura da tabela de história para além do período de retenção, pois pode obter resultados inconsistentes ou inesperados. Recomendamos que utilize consultas temporais com cláusula DE SYSTEM_TIME PARA para analisar dados em tabelas temporais.

## <a name="point-in-time-restore-considerations"></a>Questões de restabelecimento do ponto no tempo

Quando cria uma nova base de [dados, restaurando a base](sql-database-recovery-using-backups.md)de dados existente num determinado momento, tem a retenção temporal desativada ao nível da base de dados. **(is_temporal_history_retention_enabled** bandeira definida para OFF). Esta funcionalidade permite examinar todas as linhas históricas após restaurar, sem se preocupar que as filas envelhecidas sejam removidas antes de as consultar. Pode usá-lo para inspecionar dados históricos para além do período de *retenção configurado*.

Diga que uma tabela temporal tem um período de retenção de um mês especificado. Se a sua base de dados fosse criada no nível de Serviço Premium, seria capaz de criar cópias de base de dados com o estado da base de dados até 35 dias atrás no passado. Isso efetivamente permitir-lhe-ia analisar linhas históricas que têm até 65 dias, consultando diretamente a tabela de história.

Se pretender ativar a limpeza da retenção temporal, faça a seguinte declaração Transact-SQL após o ponto no tempo restaurar:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar tabelas temporais nas suas aplicações, confira [Getting Started with Temporal Tables in Azure SQL Database](sql-database-temporal-tables.md).

Visite o Canal 9 para ouvir uma verdadeira história de sucesso de [implementação temporal](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) do cliente e assistir a uma [demonstração temporal ao vivo.](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)

Para obter informações detalhadas sobre tabelas temporais, reveja a [documentação da MSDN.](https://msdn.microsoft.com/library/dn935015.aspx)
