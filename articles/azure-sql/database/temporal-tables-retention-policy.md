---
title: Gerir dados históricos em tabelas temporais
description: Aprenda a usar a política de retenção temporal para manter os dados históricos sob o seu controlo.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/25/2018
ms.openlocfilehash: 1d68163a9fba3ba3bcd4c0c0f3fb5f442296e781
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91619394"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gerir dados históricos em tabelas temporais com política de retenção
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

As tabelas temporais podem aumentar o tamanho da base de dados mais do que as tabelas normais, especialmente se reter dados históricos por um período mais longo. Assim, a política de retenção de dados históricos é um aspeto importante do planeamento e gestão do ciclo de vida de cada tabela temporal. As tabelas temporais na Base de Dados Azure SQL e na Azure SQL Managed Instance vêm com um mecanismo de retenção fácil de usar que o ajuda a realizar esta tarefa.

A retenção do histórico temporal pode ser configurada a nível de mesa individual, o que permite aos utilizadores criar polícias flexíveis de envelhecimento. A aplicação da retenção temporal é simples: requer apenas um parâmetro a ser definido durante a criação de mesa ou mudança de esquema.

Depois de definir a política de retenção, a Azure SQL Database e a Azure SQL Managed Instance começam a verificar regularmente se existem linhas históricas elegíveis para limpeza automática de dados. A identificação das linhas correspondentes e a sua remoção da tabela de história ocorrem de forma transparente, na tarefa de fundo que é programada e executada pelo sistema. A condição etária para as linhas de tabela de histórico é verificada com base na coluna que representa o fim do período de SYSTEM_TIME. Se o período de retenção, por exemplo, for fixado em seis meses, as filas de mesa elegíveis para limpeza satisfazem a seguinte condição:

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

No exemplo anterior, assumimos que a coluna **ValidTo** corresponde ao fim do período de SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Como configurar a política de retenção

Antes de configurar a política de retenção para uma tabela temporal, verifique primeiro se a retenção histórica temporal está ativada *ao nível da base de dados*.

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

A **is_temporal_history_retention_enabled** de bandeira de base de dados está definida para ON por padrão, mas os utilizadores podem alterá-la com a declaração ALTER DATABASE. Também é automaticamente programado para OFF após [ponto em tempo restaurar](recovery-using-backups.md) a operação. Para ativar a limpeza da retenção de histórico temporal para a sua base de dados, execute a seguinte declaração:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Pode configurar a retenção para as tabelas temporais mesmo **que is_temporal_history_retention_enabled** esteja desligado, mas a limpeza automática para filas envelhecidas não é desencadeada nesse caso.

A política de retenção é configurada durante a criação da tabela, especificando o valor para o parâmetro HISTORY_RETENTION_PERIOD:

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

Azure SQL Database e Azure SQL Managed Instance permitem especificar o período de retenção utilizando diferentes unidades de tempo: DIAS, SEMANAS, MESES e ANOS. Se HISTORY_RETENTION_PERIOD for omitida, assume-se a retenção INFINITA. Também pode usar a palavra-chave INFINITE explicitamente.

Em alguns cenários, pode querer configurar a retenção após a criação da tabela, ou alterar o valor previamente configurado. Nesse caso, utilize a declaração ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> A definição SYSTEM_VERSIONING a OFF *não preserva o* valor do período de retenção. A definição SYSTEM_VERSIONING on sem HISTORY_RETENTION_PERIOD especificada explicitamente resulta no período de retenção INFINITE.

Para rever o estado atual da política de retenção, utilize a seguinte consulta que une o pavilhão de habilitação de retenção temporal ao nível da base de dados com períodos de retenção para tabelas individuais:

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

## <a name="how-ages-rows-are-deleted"></a>Como as linhas de idade são eliminadas

O processo de limpeza depende da disposição do índice da tabela de história. É importante notar que *apenas as tabelas de história com um índice agrupado (árvore B ou loja de colunas) podem ter a política de retenção finita configurada*. Uma tarefa de fundo é criada para executar a limpeza de dados envelhecido para todas as tabelas temporais com período de retenção finito.
A lógica de limpeza do índice agrupado da rowstore (árvore B) elimina a linha envelhecida em pedaços menores (até 10K) minimizando a pressão no registo de base de dados e no subsistema IO. Embora a lógica de limpeza utilize o índice de árvore B necessário, a ordem de supressões para as linhas mais antigas do que o período de retenção não pode ser firmemente garantida. Por isso, *não assuma qualquer dependência da ordem de limpeza nas suas aplicações*.

A tarefa de limpeza da loja de colunas agrupadas remove [grupos](/sql/relational-databases/indexes/columnstore-indexes-overview) de linha inteiros ao mesmo tempo (normalmente contêm 1 milhão de linhas cada), o que é muito eficiente, especialmente quando os dados históricos são gerados a um ritmo elevado.

![Retenção de lojas de colunas agrupadas](./media/temporal-tables-retention-policy/cciretention.png)

Uma excelente compressão de dados e uma limpeza eficiente da retenção fazem do índice de colunas agrupados uma escolha perfeita para cenários quando a sua carga de trabalho gera rapidamente uma elevada quantidade de dados históricos. Este padrão é típico para cargas de trabalho de [processamento transacional intensivo que usam tabelas temporais](/sql/relational-databases/tables/temporal-table-usage-scenarios) para rastrear e auditar alterações, análise de tendências ou ingestão de dados de IoT.

## <a name="index-considerations"></a>Considerações de índice

A tarefa de limpeza das tabelas com índice agrupado de lojas de linha requer que o índice comece com a coluna correspondente ao fim do período de SYSTEM_TIME. Se tal índice não existir, não é possível configurar um período de retenção finita:

*Msg 13765, Nível 16, Estado 1 <br> </br> O período de retenção finita falhou na tabela temporal 'temporalstagetestdb.dbo.WebsiteUserInfo' porque a tabela de história 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' não contém o índice agrupado necessário. Considere criar uma loja de colunas agrupada ou índice de árvore B começando com a coluna que corresponde ao fim do período SYSTEM_TIME, na tabela de história.*

É importante notar que a tabela de histórico padrão criada pela Azure SQL Database e pela Azure SQL Managed Instance já tem um índice agrupado, o que está em conformidade com a política de retenção. Se tentar remover esse índice numa tabela com um período de retenção finito, a operação falha com o seguinte erro:

*Msg 13766, Nível 16, Estado 1 <br> </br> Não pode deixar cair o índice agrupado 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' porque está a ser usado para limpeza automática de dados envelhecidos. Considere a definição HISTORY_RETENTION_PERIOD para INFINITE na tabela temporal correspondente, se precisar de deixar cair este índice.*

A limpeza no índice de colunas agrupadas funciona perfeitamente se as linhas históricas forem inseridas na ordem ascendente (encomendada pela coluna final do período), o que acontece sempre quando a tabela de história é povoada exclusivamente pelo mecanismo SYSTEM_VERSIONIOING. Se as linhas na tabela de história não forem encomendadas até ao final da coluna do período (o que pode acontecer se migrar dados históricos existentes), deve recriar o índice de loja de colunas agrupadas em cima do índice de loja de árvores B que é devidamente ordenado, para obter o melhor desempenho.

Evite reconstruir o índice de lojas de colunas agrupados na tabela de história com o período de retenção finito, pois pode alterar a encomenda nos grupos de linha naturalmente impostos pela operação de versão do sistema. Se precisar de reconstruir o índice de lojas de colunas agrupados na tabela de história, faça-o recriando-o em cima do índice b-tree em conformidade, preservando a encomenda nos grupos de linha necessários para a limpeza regular de dados. A mesma abordagem deve ser adotada se criar uma tabela temporal com a tabela de história existente que tenha índice de coluna agrupado sem ordem de dados garantida:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Quando o período de retenção finito é configurado para a tabela de história com o índice de colunas agrupadas, não é possível criar índices adicionais de árvore B não agrupadas nessa tabela:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Uma tentativa de executar a declaração acima falha com o seguinte erro:

*Msg 13772, Nível 16, Estado 1 <br> </br> Não pode criar índice não agrupado numa tabela de história temporal 'WebsiteUserInfoHistory' uma vez que tem um período de retenção finito e índice de colunas agrupado definido.*

## <a name="querying-tables-with-retention-policy"></a>Consultas tabelas com política de retenção

Todas as consultas na tabela temporal filtram automaticamente linhas históricas correspondentes à política de retenção finita, para evitar resultados imprevisíveis e inconsistentes, uma vez que as linhas envelhecidas podem ser eliminadas pela tarefa de limpeza, *em qualquer momento e por ordem arbitrária.*

A imagem a seguir mostra o plano de consulta para uma consulta simples:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

O plano de consulta inclui filtro adicional aplicado à coluna de fim de período (ValidTo) no operador de Index Scan Agrupado na tabela de história (realçada). Este exemplo pressupõe que um período de retenção de um MÊS foi estabelecido na tabela WebsiteUserInfo.

![Filtro de consulta de retenção](./media/temporal-tables-retention-policy/queryexecplanwithretention.png)

No entanto, se consultar diretamente a tabela de história, poderá ver linhas mais antigas do que o período de retenção especificado, mas sem qualquer garantia para resultados de consulta repetíveis. A seguinte imagem mostra o plano de execução de consulta para a consulta na tabela de história sem filtros adicionais aplicados:

![Consulta da história sem filtro de retenção](./media/temporal-tables-retention-policy/queryexecplanhistorytable.png)

Não confie na sua lógica de negócio na leitura da tabela de história para além do período de retenção, pois poderá obter resultados inconsistentes ou inesperados. Recomendamos que utilize consultas temporais com cláusula de SYSTEM_TIME FOR para analisar dados em tabelas temporais.

## <a name="point-in-time-restore-considerations"></a>Ponto no tempo restaurar considerações

Quando cria uma nova base de [dados, restaurando a base de dados existente a um ponto específico no tempo,](recovery-using-backups.md)tem a retenção temporal desativada ao nível da base de dados. **(is_temporal_history_retention_enabled** bandeira definida para OFF). Esta funcionalidade permite-lhe examinar todas as linhas históricas após a restauração, sem se preocupar que as filas envelhecidas sejam removidas antes de as consultar. Pode usá-lo para *inspecionar dados históricos para além do período de retenção configurado.*

Digamos que uma tabela temporal tem um período de retenção de um mês especificado. Se a sua base de dados foi criada no nível de Serviço Premium, poderá criar uma cópia de base de dados com o estado da base de dados até 35 dias atrás no passado. Isso, efetivamente, permitir-lhe-ia analisar linhas históricas que têm até 65 dias de idade consultando a tabela de história diretamente.

Se pretender ativar a limpeza da retenção temporal, execute a seguinte declaração Transact-SQL após tempo, restabeleça:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar tabelas temporais nas suas aplicações, confira [Como Começar com tabelas temporais.](../temporal-tables.md)

Visite o Canal 9 para ouvir uma [história de sucesso de implementação temporal](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) do cliente e assista a uma [demonstração temporal ao vivo.](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)

Para obter informações detalhadas sobre tabelas [temporais, reveja as tabelas temporais.](/sql/relational-databases/tables/temporal-tables)
