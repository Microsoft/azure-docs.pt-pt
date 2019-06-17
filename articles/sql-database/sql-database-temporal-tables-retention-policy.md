---
title: Gerir dados históricos em tabelas temporais com a política de retenção | Documentos da Microsoft
description: Saiba como utilizar a política de retenção temporal para manter os dados históricos sob o seu controlo.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 62e88d912c55015f87cc00f21527010ad01ee00c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615686"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gerir dados históricos em tabelas temporais com a política de retenção

Tabelas temporais podem aumentar o tamanho da base de dados mais do que as tabelas regulares, especialmente se mantém os dados históricos de um período mais longo do tempo. Portanto, a política de retenção para os dados históricos é um aspecto importante de planejar e gerenciar o ciclo de vida de cada tabela temporal. Tabelas temporais na SQL Database do Azure são fornecidos com o mecanismo de retenção de fácil de usar que ajuda a realizar essa tarefa.

Retenção de histórico temporal pode ser configurada ao nível da tabela individual, que permite aos utilizadores criar classificação por vencimento flexível políticas. Aplicação de retenção temporal é simples: requer apenas um parâmetro para ser definido durante a alteração de criação ou o esquema de tabela.

Depois de definir a política de retenção, a base de dados do Azure SQL inicia a verificar regularmente se existem históricas linhas que são elegíveis para a limpeza de dados automática. A identificação de linhas correspondentes e respetiva remoção da tabela do histórico de forma transparente, ocorrerem na tarefa em segundo plano que é agendada e executada pelo sistema. Condição de idade para linhas da tabela de histórico é verificada com base na coluna que representa o fim do período SYSTEM_TIME. Se o período de retenção, por exemplo, é definido como seis meses, linhas de tabela qualificadas para limpeza de satisfazem a seguinte condição:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

No exemplo anterior, assumimos que **ValidTo** coluna corresponde ao final do período SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Como configurar a política de retenção

Antes de configurar a política de retenção para uma tabela temporal, verifique primeiro se está ativada a retenção de histórico temporal *ao nível da base de dados*.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Sinalizador de base de dados **is_temporal_history_retention_enabled** está definido como ligado por predefinição, mas os usuários podem alterá-lo com a instrução ALTER DATABASE. É também automaticamente definida como OFF após [um ponto anterior no tempo dos](sql-database-recovery-using-backups.md) operação. Para ativar a limpeza de retenção de histórico temporais da base de dados, execute a declaração seguinte:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Pode configurar o período de retenção para o mesmo tabelas temporais **is_temporal_history_retention_enabled** está OFF, mas a limpeza automática para linhas Desatualizadas não é acionada nesse caso.

Política de retenção é configurada durante a criação de tabela, especificando o valor do parâmetro HISTORY_RETENTION_PERIOD:

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

Base de dados SQL do Azure permite-lhe especificar o período de retenção ao utilizar unidades de tempo diferentes: DIAS, semanas, meses e anos. Se o HISTORY_RETENTION_PERIOD for omitido, é assumida retenção INFINITA. Também pode utilizar palavra-chave INFINITO explicitamente.

Em alguns cenários, pode querer configurar a retenção após a criação de tabela ou alterar anteriormente configurado valor. Nesse caso utilize a instrução ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> A definição de SYSTEM_VERSIONING como OFF *não preserva a* valor do período de retenção. Definição de SYSTEM_VERSIONING como on sem HISTORY_RETENTION_PERIOD explicitamente especificado resultados no período de retenção INFINITO.

Para rever o estado atual da política de retenção, utilize a seguinte consulta que associa o sinalizador de ativação de retenção temporal ao nível da base de dados com os períodos de retenção para tabelas individuais:

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


## <a name="how-sql-database-deletes-aged-rows"></a>Como a base de dados SQL elimina linhas Desatualizadas

O processo de limpeza depende o esquema de índice da tabela do histórico. É importante notar que *apenas tabelas de histórico com um índice em cluster (árvore B ou columnstore) podem ter a política de retenção finito configurada*. É criada uma tarefa de segundo plano para executar a limpeza de dados antigos para todas as tabelas temporais com período de retenção finito.
Lógica de limpeza para o índice agrupado rowstore (árvore B) elimina desatualizada linha em segmentos mais pequenos (até 10 mil) minimizando pressão no registo de base de dados e do subsistema de e/s. Embora a lógica de limpeza utiliza o índice de árvore B necessário, a ordem de eliminações para as linhas mais antigas do que o período de retenção não pode ser garantido firmemente. Por conseguinte, *não realizar qualquer dependência sobre a ordem de limpeza em seus aplicativos*.

A tarefa de limpeza para o columnstore em cluster remove toda [linha grupos](https://msdn.microsoft.com/library/gg492088.aspx) ao mesmo tempo (normalmente, contêm 1 milhão de linhas, cada), que é muito eficiente, especialmente quando os dados históricos são gerados a um ritmo elevado.

![Retenção de columnstore em cluster](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Compressão de dados excelente e faz de limpeza de retenção eficiente em cluster índice columnstore uma opção perfeita para cenários de quando a carga de trabalho gera rapidamente elevada quantidade de dados históricos. Esse padrão é normal com muitos [cargas de trabalho de processamento de transações que utilizam tabelas temporais](https://msdn.microsoft.com/library/mt631669.aspx) para controlo de alterações e auditoria, análise de tendências ou ingestão de dados de IoT.

## <a name="index-considerations"></a>Considerações de índice

A tarefa de limpeza para tabelas de índice em cluster rowstore precisa de um índice para começar a coluna correspondente ao fim do período SYSTEM_TIME. Se esse índice não existir, não é possível configurar um período de retenção finito:

*Msg 13765, 16 de nível, 1 de estado <br> </br> definir o período de retenção finito falhou na tabela temporal com versão do sistema "temporalstagetestdb.dbo.WebsiteUserInfo' porque a tabela de histórico" temporalstagetestdb.dbo.WebsiteUserInfoHistory' não contém o índice em cluster necessário. Considere a criação de um columnstore em cluster ou o índice de árvore B, começando pela coluna que corresponde ao final de SYSTEM_TIME period, na tabela do histórico.*

É importante notar que a tabela de histórico do padrão criada pela base de dados do Azure SQL já tem um índice agrupado, que está em conformidade com a política de retenção. Se tentar remover esse índice numa tabela com o período de retenção finito, a operação falhar com o seguinte erro:

*Msg 13766, 16 de nível, 1 de estado <br> </br> não é possível remover o índice agrupado 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' porque está a ser utilizado para a limpeza automática dos dados antigos. Considere definir o HISTORY_RETENTION_PERIOD para INFINITE na tabela temporal com versão do sistema correspondente se necessitar de remover este índice.*

Limpeza no índice columnstore em cluster funciona de forma ideal se históricos linhas estão inseridas na ordem ascendente (ordenada no final da coluna de período), que é sempre o caso, quando a tabela de histórico é preenchida exclusivamente com o mecanismo SYSTEM_VERSIONIOING. Se as linhas da tabela de histórico não são ordenadas pelo final da coluna de período (que pode ser o caso, se tiver migrado os dados históricos existentes), deve voltar a criar índice columnstore em cluster com base no índice rowstore árvore B que está ordenado corretamente, conseguir ideal desempenho.

Evite reconstruir o índice columnstore em cluster na tabela do histórico com o período de retenção finito, porque eles podem mudar a ordem dos grupos de linhas naturalmente impostos pela operação de versão de sistema. Se precisar de recriar o índice columnstore em cluster na tabela do histórico, fazê-lo ao voltar a criar com base no índice de árvore B em conformidade, preservando a ordem dos grupos de linhas necessários para a limpeza de dados regulares. Deve ser levada a mesma abordagem, se criar a tabela temporal com a tabela de histórico existente que tem um índice de coluna, sem a ordem dos dados garantida agrupado:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Quando o período de retenção finito é configurado para a tabela de histórico com o índice columnstore em cluster, não é possível criar índices de árvore B não-clusterizados adicionais nessa tabela:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Uma tentativa de executar acima da declaração de falha com o erro seguinte:

*Msg 13772, 16 de nível, 1 de estado <br> </br> não é possível criar índice não agrupado numa tabela de histórico temporal 'WebsiteUserInfoHistory' uma vez que é o período de retenção finito e o índice columnstore em cluster definidos.*

## <a name="querying-tables-with-retention-policy"></a>Consultar as tabelas com a política de retenção

Todas as consultas na tabela temporal automaticamente filtram as linhas históricas correspondência de política de retenção finito, para evitar resultados imprevisíveis e inconsistentes, uma vez que linhas Desatualizadas podem ser eliminadas pela tarefa de limpeza, *em qualquer ponto no tempo e no arbitrário ordem*.

A imagem seguinte mostra o plano de consulta para uma consulta simples:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

O plano de consulta inclui o filtro adicional aplicado ao final da coluna de período (ValidTo) no operador Clustered Index Scan na tabela do histórico (realçado). Este exemplo assume que esse período de retenção de um mês foi definido na tabela de WebsiteUserInfo.

![Filtro de consulta de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

No entanto, se consultar a tabela de histórico diretamente, poderá ver linhas que têm mais de retenção especificado período, mas sem nenhuma garantia para resultados da consulta passível de repetição. A imagem seguinte mostra o plano de execução de consulta para a consulta na tabela do histórico sem adicionais filtros aplicados:

![Histórico de consulta sem filtro de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Não confie sua lógica de negócio na tabela de histórico, além do período de retenção de leitura, como pode obter resultados inconsistentes ou inesperados. Recomendamos que utilize consultas temporais com a cláusula FOR system_time temporária de análise de dados em tabelas temporais.

## <a name="point-in-time-restore-considerations"></a>Um ponto anterior no considerações de restauro de tempo

Quando cria uma nova base de dados por [restaurar base de dados existente para um ponto específico anterior no tempo](sql-database-recovery-using-backups.md), ele tem retenção temporal desabilitada ao nível da base de dados. (**is_temporal_history_retention_enabled** sinalizador definido como OFF). Esta funcionalidade permite que examine todas as linhas históricas após o restauro, sem se preocupar que desatualizados linhas são removidas antes que possa consultá-los. Pode usá-lo para *inspecionar os dados históricos, além do período de retenção configurado*.

Digamos que uma tabela temporal tem retenção de um mês período especificada. Se a sua base de dados foi criada na camada de serviço Premium, seria capaz de criar a cópia da base de dados com o estado da base de dados até 35 dias no passado. Isso efetivamente permitiria que analise históricas linhas que têm o até 65 dias através de consultas diretas a tabela de histórico.

Se pretender ativar a limpeza de retenção temporal, execute a seguinte instrução de Transact-SQL após o ponto no tempo:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Passos Seguintes

Para saber como usar as tabelas temporais em seus aplicativos, confira [introdução às tabelas temporais na base de dados do Azure SQL](sql-database-temporal-tables.md).

Visite o Channel 9 para ouvir uma [história de sucesso de implementação temporal de clientes reais](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e ver um [live demonstração temporal](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Para obter informações detalhadas sobre tabelas temporais, reveja [documentação do MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
