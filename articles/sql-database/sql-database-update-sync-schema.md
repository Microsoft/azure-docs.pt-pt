---
title: Automatizar a replicação de alterações de esquema no Sincronização de Dados SQL
description: Saiba como automatizar a replicação de alterações de esquema no Azure Sincronização de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/14/2018
ms.openlocfilehash: 639901975bbb66b9f410bea297d9e48cd96d6d1b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822439"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatizar a replicação de alterações de esquema no Azure Sincronização de Dados SQL

Sincronização de Dados SQL permite que os usuários sincronizem dados entre os bancos de dados SQL do Azure e locais SQL Server em uma direção ou em ambas as direções. Uma das limitações atuais do Sincronização de Dados SQL é a falta de suporte para a replicação de alterações de esquema. Sempre que você altera o esquema de tabela, precisa aplicar as alterações manualmente em todos os pontos de extremidade, incluindo o Hub e todos os membros e, em seguida, atualizar o esquema de sincronização.

Este artigo apresenta uma solução para replicar automaticamente as alterações de esquema para todos os Sincronização de Dados SQL pontos de extremidade.
1. Essa solução usa um gatilho DDL para acompanhar as alterações de esquema.
1. O gatilho insere os comandos de alteração de esquema em uma tabela de rastreamento.
1. Esta tabela de acompanhamento é sincronizada com todos os pontos de extremidade usando o serviço de sincronização de dados.
1. Os gatilhos DML após a inserção são usados para aplicar as alterações de esquema nos outros pontos de extremidade.

Este artigo usa ALTER TABLE como um exemplo de uma alteração de esquema, mas essa solução também funciona para outros tipos de alterações de esquema.

> [!IMPORTANT]
> Recomendamos que você leia este artigo com cuidado, especialmente as seções sobre [solução de problemas](#troubleshoot) e [outras considerações](#other), antes de começar a implementar a replicação de alteração de esquema automatizada em seu ambiente de sincronização. Também recomendamos que você leia [dados de sincronização em vários bancos de dados locais e de nuvem com sincronização de dados SQL](sql-database-sync-data.md). Algumas operações de banco de dados podem interromper a solução descrita neste artigo. O conhecimento adicional de domínio do SQL Server e do Transact-SQL pode ser necessário para solucionar esses problemas.

![Automatizando a replicação de alterações de esquema](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Configurar a replicação de alteração de esquema automatizada

### <a name="create-a-table-to-track-schema-changes"></a>Criar uma tabela para acompanhar as alterações de esquema

Crie uma tabela para acompanhar as alterações de esquema em todos os bancos de dados no grupo de sincronização:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Esta tabela tem uma coluna de identidade para acompanhar a ordem das alterações de esquema. Você pode adicionar mais campos para registrar mais informações, se necessário.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Criar uma tabela para acompanhar o histórico de alterações de esquema

Em todos os pontos de extremidade, crie uma tabela para acompanhar a ID do comando de alteração de esquema aplicado mais recentemente.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Criar um gatilho ALTER TABLE DDL no banco de dados em que são feitas alterações de esquema

Crie um gatilho DDL para operações ALTER TABLE. Você só precisa criar esse gatilho no banco de dados em que são feitas alterações de esquema. Para evitar conflitos, permita apenas alterações de esquema em um banco de dados em um grupo de sincronização.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

O gatilho insere um registro na tabela de controle de alterações de esquema para cada comando ALTER TABLE. Este exemplo adiciona um filtro para evitar a replicação de alterações de esquema feitas sob o esquema de **sincronização**de dados, pois elas são mais provavelmente feitas pelo serviço de sincronização. Adicione mais filtros se você quiser replicar determinados tipos de alterações de esquema.

Você também pode adicionar mais gatilhos para replicar outros tipos de alterações de esquema. Por exemplo, crie CREATE_PROCEDURE, ALTER_PROCEDURE e gatilhos DROP_PROCEDURE para replicar as alterações em procedimentos armazenados.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Criar um gatilho em outros pontos de extremidade para aplicar alterações de esquema durante a inserção

Esse gatilho executa o comando de alteração de esquema quando ele é sincronizado com outros pontos de extremidade. Você precisa criar esse gatilho em todos os pontos de extremidade, exceto aquele em que as alterações de esquema são feitas (ou seja, no banco de dados em que o gatilho DDL `AlterTableDDLTrigger` é criado na etapa anterior).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Esse gatilho é executado após a inserção e verifica se o comando atual deve ser executado em seguida. A lógica de código garante que nenhuma instrução de alteração de esquema seja ignorada e todas as alterações sejam aplicadas mesmo se a inserção estiver fora de ordem.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Sincronizar a tabela de controle de alterações de esquema com todos os pontos de extremidade

Você pode sincronizar a tabela de controle de alterações de esquema com todos os pontos de extremidade usando o grupo de sincronização existente ou um novo grupo de sincronização. Certifique-se de que as alterações na tabela de rastreamento possam ser sincronizadas com todos os pontos de extremidade, especialmente quando você estiver usando a sincronização de uma direção.

Não sincronize a tabela de histórico de alterações de esquema, pois essa tabela mantém o estado diferente em pontos de extremidade diferentes.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Aplicar as alterações de esquema em um grupo de sincronização

Somente as alterações de esquema feitas no banco de dados em que o gatilho DDL é criado são replicadas. As alterações de esquema feitas em outros bancos de dados não são replicadas.

Depois que as alterações de esquema forem replicadas para todos os pontos de extremidade, você também precisará executar etapas adicionais para atualizar o esquema de sincronização para iniciar ou parar a sincronização das novas colunas.

#### <a name="add-new-columns"></a>Adicionar novas colunas

1.  Faça a alteração do esquema.

1.  Evite qualquer alteração de dados em que as novas colunas estejam envolvidas até que você conclua a etapa que cria o gatilho.

1.  Aguarde até que as alterações de esquema sejam aplicadas a todos os pontos de extremidade.

1.  Atualize o esquema de banco de dados e adicione a nova coluna ao esquema de sincronização.

1.  Os dados na nova coluna são sincronizados durante a próxima operação de sincronização.

#### <a name="remove-columns"></a>Remover colunas

1.  Remova as colunas do esquema de sincronização. A sincronização de dados interrompe a sincronização de dados nessas colunas.

1.  Faça a alteração do esquema.

1.  Atualize o esquema de banco de dados.

#### <a name="update-data-types"></a>Atualizar tipos de dados

1.  Faça a alteração do esquema.

1.  Aguarde até que as alterações de esquema sejam aplicadas a todos os pontos de extremidade.

1.  Atualize o esquema de banco de dados.

1.  Se os tipos de dados novos e antigos não forem totalmente compatíveis, por exemplo, se você alterar de `int` para `bigint`-Sync poderá falhar antes que as etapas que criam os gatilhos sejam concluídas. A sincronização é realizada com sucesso após uma nova tentativa.

#### <a name="rename-columns-or-tables"></a>Renomear colunas ou tabelas

A renomeação de colunas ou tabelas torna a sincronização de dados parar de funcionar. Crie uma nova tabela ou coluna, aterrando os dados e, em seguida, exclua a tabela ou coluna antiga em vez de renomear.

#### <a name="other-types-of-schema-changes"></a>Outros tipos de alterações de esquema

Para outros tipos de alterações de esquema, por exemplo, a criação de procedimentos armazenados ou descartar um índice, não é necessário atualizar o esquema de sincronização.

## <a name="troubleshoot"></a>Solucionar problemas de replicação de alteração de esquema automatizado

A lógica de replicação descrita neste artigo para de funcionar em algumas situações – por exemplo, se você fez uma alteração de esquema em um banco de dados local que não tem suporte no banco de dados SQL do Azure. Nesse caso, a sincronização da tabela de controle de alterações de esquema falha. Você precisa corrigir esse problema manualmente:

1.  Desabilite o gatilho DDL e evite outras alterações de esquema até que o problema seja corrigido.

1.  No banco de dados do ponto de extremidade em que o problema está ocorrendo, desabilite o gatilho após inserir no ponto de extremidade em que a alteração de esquema não pode ser feita. Essa ação permite que o comando de alteração de esquema seja sincronizado.

1.  Disparar a sincronização para sincronizar a tabela de controle de alterações de esquema.

1.  No banco de dados de ponto de extremidade em que o problema está acontecendo, consulte a tabela de histórico de alterações de esquema para obter a ID do último comando de alteração de esquema aplicado.

1.  Consulte a tabela de controle de alterações de esquema para listar todos os comandos com uma ID maior que o valor de ID recuperado na etapa anterior.

    a.  Ignore os comandos que não podem ser executados no banco de dados do ponto de extremidade. Você precisa lidar com a inconsistência do esquema. Reverta as alterações de esquema originais se a inconsistência afetar seu aplicativo.

    b.  Aplique manualmente os comandos que devem ser aplicados.

1.  Atualize a tabela de histórico de alterações de esquema e defina a última ID aplicada para o valor correto.

1.  Verifique novamente se o esquema está atualizado.

1.  Habilite novamente o gatilho após inserir desabilitado na segunda etapa.

1.  Habilite novamente o gatilho DDL desabilitado na primeira etapa.

Se você quiser limpar os registros na tabela de controle de alterações de esquema, use DELETE em vez de TRUNCATE. Nunca propague a coluna de identidade na tabela de controle de alterações de esquema usando DBCC CHECKIDENT. Você pode criar novas tabelas de controle de alterações de esquema e atualizar o nome da tabela no gatilho DDL se a repropagação for necessária.

## <a name="other"></a>Outras considerações

-   Os usuários de banco de dados que configuram o Hub e os bancos de dados de membros precisam ter permissão suficiente para executar os comandos de alteração de esquema.

-   Você pode adicionar mais filtros no gatilho DDL para replicar apenas alterações de esquema em tabelas ou operações selecionadas.

-   Você só pode fazer alterações de esquema no banco de dados em que o gatilho DDL é criado.

-   Se você estiver fazendo uma alteração em um banco de dados SQL Server local, verifique se a alteração de esquema tem suporte no banco de dados SQL do Azure.

-   Se forem feitas alterações de esquema em bancos de dados que não sejam o banco de dados em que o gatilho DDL é criado, as alterações não serão replicadas. Para evitar esse problema, você pode criar gatilhos DDL para bloquear alterações em outros pontos de extremidade.

-   Se você precisar alterar o esquema da tabela de controle de alterações de esquema, desabilite o gatilho DDL antes de fazer a alteração e, em seguida, aplique manualmente a alteração a todos os pontos de extremidade. Atualizar o esquema em um gatilho AFTER INSERT na mesma tabela não funciona.

-   Não refaça a propagação da coluna de identidade usando DBCC CHECKIDENT.

-   Não use Truncate para limpar os dados na tabela de controle de alterações de esquema.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Visão geral – [sincronizar dados entre vários bancos de dados locais e de nuvem com o Azure sincronização de dados SQL](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal- [tutorial: configurar sincronização de dados SQL para sincronizar os dados entre o Azure SQL Database e o SQL Server local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de sincronização de dados- [agente de sincronização de dados para Azure sincronização de dados SQL](sql-database-data-sync-agent.md)
-   Práticas recomendadas- [práticas recomendadas para o Azure sincronização de dados SQL](sql-database-best-practices-data-sync.md)
-   Monitorar [sincronização de dados SQL monitorar com Azure monitor logs](sql-database-sync-monitor-oms.md)
-   Solucionar problemas- [solucionar problema com o Azure sincronização de dados SQL](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o PowerShell- [use o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)
