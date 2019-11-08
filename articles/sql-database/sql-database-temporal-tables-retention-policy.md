---
title: Gerenciar dados históricos em tabelas temporais
description: Saiba como usar a política de retenção temporal para manter os dados históricos sob seu controle.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820691"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gerenciar dados históricos em tabelas temporais com política de retenção

As tabelas temporais podem aumentar o tamanho do banco de dados mais do que as tabelas normais, especialmente se você mantiver o histórico de um período de tempo maior. Portanto, a política de retenção para dados históricos é um aspecto importante do planejamento e gerenciamento do ciclo de vida de cada tabela temporal. As tabelas temporais no banco de dados SQL do Azure são fornecidas com um mecanismo de retenção fácil de usar que ajuda você a realizar essa tarefa.

A retenção de histórico temporal pode ser configurada no nível de tabela individual, o que permite aos usuários criar políticas de envelhecimento flexíveis. A aplicação da retenção temporal é simples: ela requer que apenas um parâmetro seja definido durante a criação da tabela ou alteração de esquema.

Depois de definir a política de retenção, o banco de dados SQL do Azure começa a verificar regularmente se há linhas históricas qualificadas para limpeza automática de dados. A identificação de linhas correspondentes e sua remoção da tabela de histórico ocorrem de forma transparente, na tarefa em segundo plano que é agendada e executada pelo sistema. A condição de idade para as linhas da tabela de histórico é verificada com base na coluna que representa o final do período de SYSTEM_TIME. Se o período de retenção, por exemplo, for definido como seis meses, as linhas da tabela qualificadas para limpeza atenderão à seguinte condição:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

No exemplo anterior, supomos que a coluna **ValidTo** corresponde ao final do período de SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Como configurar a política de retenção

Antes de configurar a política de retenção para uma tabela temporal, verifique primeiro se a retenção de histórico temporal está habilitada *no nível do banco de dados*.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

O sinalizador de banco de dados **is_temporal_history_retention_enabled** é definido como ativado por padrão, mas os usuários podem alterá-lo com a instrução ALTER DATABASE. Ele também é definido automaticamente como OFF após a operação [de restauração pontual](sql-database-recovery-using-backups.md) . Para habilitar a limpeza de retenção de histórico temporal para seu banco de dados, execute a seguinte instrução:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Você pode configurar a retenção para tabelas temporais mesmo se **is_temporal_history_retention_enabled** estiver desativada, mas a limpeza automática para linhas antigas não será disparada nesse caso.

A política de retenção é configurada durante a criação da tabela especificando o valor para o parâmetro HISTORY_RETENTION_PERIOD:

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

O banco de dados SQL do Azure permite especificar o período de retenção usando unidades de tempo diferentes: dias, semanas, meses e anos. Se HISTORY_RETENTION_PERIOD for omitido, a retenção infinita será assumida. Você também pode usar a palavra-chave infinita explicitamente.

Em alguns cenários, talvez você queira configurar a retenção após a criação da tabela ou alterar o valor configurado anteriormente. Nesse caso, use a instrução ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Definir SYSTEM_VERSIONING como OFF não *preserva o* valor do período de retenção. A configuração de SYSTEM_VERSIONING como ON sem HISTORY_RETENTION_PERIOD especificada resulta explicitamente no período de retenção infinito.

Para examinar o estado atual da política de retenção, use a seguinte consulta que une o sinalizador de habilitação de retenção temporal no nível do banco de dados com períodos de retenção para tabelas individuais:

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


## <a name="how-sql-database-deletes-aged-rows"></a>Como o banco de dados SQL exclui linhas antigas

O processo de limpeza depende do layout do índice da tabela de histórico. É importante observar que *apenas tabelas de histórico com um índice clusterizado (árvore B ou columnstore) podem ter uma política de retenção finita configurada*. Uma tarefa em segundo plano é criada para executar uma limpeza de dados antiga para todas as tabelas temporais com um período de retenção finito.
A lógica de limpeza para o índice clusterizado (árvore B) exclui a linha antiga em partes menores (até 10K) minimizando a pressão no log do banco de dados e no subsistema de e/s. Embora a lógica de limpeza utilize o índice de árvore B necessário, a ordem das exclusões das linhas anteriores ao período de retenção não pode ser garantida firmemente. Portanto, *não assuma nenhuma dependência na ordem de limpeza em seus aplicativos*.

A tarefa de limpeza para o columnstore clusterizado remove [grupos de linhas](https://msdn.microsoft.com/library/gg492088.aspx) inteiros de uma vez (normalmente contém 1 milhão de linhas cada), o que é muito eficiente, especialmente quando dados históricos são gerados em um ritmo alto.

![Retenção de columnstore clusterizado](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

A excelente compactação de dados e a limpeza de retenção eficiente tornam o índice columnstore clusterizado uma opção perfeita para cenários quando sua carga de trabalho gera rapidamente uma grande quantidade de dados históricos. Esse padrão é típico para [cargas de trabalho de processamento transacionais intensivas que usam tabelas temporais](https://msdn.microsoft.com/library/mt631669.aspx) para controle de alterações e auditoria, análise de tendência ou ingestão de dados de IOT.

## <a name="index-considerations"></a>Considerações de índice

A tarefa de limpeza para tabelas com o índice clusterizado do repositório de linha requer que o índice comece com a coluna correspondente ao final do período de SYSTEM_TIME. Se esse índice não existir, você não poderá configurar um período de retenção finito:

*MSG 13765, nível 16, estado 1 <br></br> a configuração do período de retenção finito falhou na tabela temporal com controle de versão do sistema ' temporalstagetestdb. dbo. WebsiteUserInfo ' porque a tabela de histórico ' temporalstagetestdb. dbo. WebsiteUserInfoHistory ' não contém o índice clusterizado necessário. Considere criar um índice columnstore ou árvore B clusterizado começando com a coluna que corresponde ao fim do período de SYSTEM_TIME, na tabela de histórico.*

É importante observar que a tabela de histórico padrão criada pelo banco de dados SQL do Azure já tem um índice clusterizado, que é compatível com a política de retenção. Se você tentar remover esse índice em uma tabela com um período de retenção finito, a operação falhará com o seguinte erro:

*MSG 13766, nível 16, estado 1 <br></br> não é possível descartar o índice clusterizado ' WebsiteUserInfoHistory. IX_WebsiteUserInfoHistory ' porque ele está sendo usado para limpeza automática de dados antigos. Considere definir HISTORY_RETENTION_PERIOD como infinito na tabela temporal com versão do sistema correspondente se você precisar descartar esse índice.*

A limpeza no índice columnstore clusterizado funcionará de forma ideal se as linhas históricas forem inseridas na ordem crescente (ordenadas pelo final da coluna de período), que é sempre o caso quando a tabela de histórico é preenchida exclusivamente pelo mecanismo de SYSTEM_VERSIONIOING. Se as linhas na tabela de histórico não forem ordenadas pelo fim da coluna de período (que pode ser o caso se você migrar os dados históricos existentes), você deverá recriar o índice columnstore clusterizado na parte superior do índice de armazenamento de linha da árvore B que está corretamente ordenado, para obter o ideal desempenho.

Evite recriar o índice columnstore clusterizado na tabela de histórico com o período de retenção finito, pois ele pode alterar a ordenação dos grupos de linhas naturalmente impostos pela operação de controle de versão do sistema. Se você precisar recriar o índice columnstore clusterizado na tabela de histórico, faça isso recriando-o na parte superior do índice de árvore B em conformidade, preservando a ordenação nos grupos de funções necessários para a limpeza de dados regular. A mesma abordagem deve ser tomada se você criar uma tabela temporal com a tabela de histórico existente que tem índice de coluna clusterizado sem a ordem de dados garantida:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Quando o período de retenção finito estiver configurado para a tabela de histórico com o índice columnstore clusterizado, você não poderá criar índices de árvore B adicionais não clusterizados nessa tabela:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Falha na tentativa de executar a instrução acima com o seguinte erro:

*MSG 13772, nível 16, estado 1 <br></br> não pode criar um índice não clusterizado em uma tabela de histórico temporal ' WebsiteUserInfoHistory ', pois ela tem um período de retenção finito e um índice columnstore clusterizado definido.*

## <a name="querying-tables-with-retention-policy"></a>Consultando tabelas com política de retenção

Todas as consultas na tabela temporal filtram automaticamente as linhas históricas que correspondem à política de retenção finita, para evitar resultados imprevisíveis e inconsistentes, pois as linhas antigas podem ser excluídas pela tarefa de limpeza, *em qualquer ponto no tempo e em ordem arbitrária*.

A figura a seguir mostra o plano de consulta para uma consulta simples:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

O plano de consulta inclui filtro adicional aplicado ao fim da coluna de período (ValidTo) no operador Clustered Index Scan na tabela de histórico (realçado). Este exemplo supõe que o período de retenção de um mês foi definido na tabela WebsiteUserInfo.

![Filtro de consulta de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

No entanto, se você consultar a tabela de histórico diretamente, poderá ver linhas mais antigas do que o período de retenção especificado, mas sem nenhuma garantia para resultados de consulta repetíveis. A figura a seguir mostra o plano de execução de consulta para a consulta na tabela de histórico sem filtros adicionais aplicados:

![Consultando histórico sem filtro de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Não confie em sua lógica de negócios na tabela de histórico de leitura além do período de retenção, pois você pode obter resultados inconsistentes ou inesperados. Recomendamos que você use consultas temporais com a cláusula FOR SYSTEM_TIME para analisar dados em tabelas temporais.

## <a name="point-in-time-restore-considerations"></a>Considerações de restauração pontual

Quando você cria um novo banco de dados [restaurando o banco de dados existente para um ponto específico no tempo](sql-database-recovery-using-backups.md), ele tem retenção temporal desabilitada no nível do banco de dados. (**is_temporal_history_retention_enabled** sinalizador definido como desativado). Essa funcionalidade permite examinar todas as linhas históricas na restauração, sem se preocupar que as linhas antigas são removidas antes de você consultá-las. Você pode usá-lo para *inspecionar dados históricos além do período de retenção configurado*.

Digamos que uma tabela temporal tenha um período de retenção de um mês especificado. Se o banco de dados tiver sido criado na camada de serviço Premium, você poderá criar uma cópia de banco de dados com o estado do banco de dados de até 35 dias no passado. Isso efetivamente permitiria que você analisasse linhas históricas com até 65 dias de idade consultando a tabela de histórico diretamente.

Se você quiser ativar a limpeza de retenção temporal, execute a seguinte instrução Transact-SQL após a restauração pontual:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Passos seguintes

Para saber como usar tabelas temporais em seus aplicativos, confira [introdução com tabelas temporais no banco de dados SQL do Azure](sql-database-temporal-tables.md).

Visite o Channel 9 para ouvir uma [história real de sucesso de implementação temporal do cliente](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e assistir a uma [demonstração temporal ao vivo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Para obter informações detalhadas sobre tabelas temporais, consulte a [documentação do MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
