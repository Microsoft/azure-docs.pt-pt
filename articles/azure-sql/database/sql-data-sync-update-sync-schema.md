---
title: Automatizar a replicação de alterações de esquema no SQL Data Sync
description: Saiba como automatizar a replicação de alterações de esquema no Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: fb64348e53dc92ef8f5830f2649afdf9eca3a9ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616487"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatizar a replicação de alterações de esquema no Azure SQL Data Sync
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O SQL Data Sync permite aos utilizadores sincronizar dados entre bases de dados em Azure SQL Database e SQL Server em instâncias de uma direção ou em ambas as direções. Uma das limitações atuais do SQL Data Sync é a falta de suporte para a replicação de alterações de esquemas. Sempre que alterar o esquema de tabela, tem de aplicar as alterações manualmente em todos os pontos finais, incluindo o hub e todos os membros, e depois atualizar o esquema de sincronização.

Este artigo introduz uma solução para replicar automaticamente as alterações de esquema em todos os pontos finais do SQL Data Sync.

1. Esta solução utiliza um gatilho DDL para rastrear as alterações de esquema.
1. O gatilho insere os comandos de alteração de esquema numa tabela de rastreio.
1. Esta tabela de rastreio está sincronizada com todos os pontos finais utilizando o serviço Data Sync.
1. Os gatilhos DML após a inserção são utilizados para aplicar as alterações de esquema nos outros pontos finais.

Este artigo utiliza o ALTER TABLE como exemplo de uma mudança de esquema, mas esta solução também funciona para outros tipos de alterações de esquemas.

> [!IMPORTANT]
> Recomendamos que leia atentamente este artigo, especialmente as secções sobre [resolução de problemas](#troubleshoot) e [outras considerações,](#other)antes de começar a implementar a replicação automática de alterações de esquemas no seu ambiente de sincronização. Recomendamos também que leia [dados de Sincronização em várias bases de dados de nuvem e no local com SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md). Algumas operações de base de dados podem quebrar a solução descrita neste artigo. Podem ser necessários conhecimentos adicionais de domínio do SQL Server e da Transact-SQL para resolver esses problemas.

![Automatizar a replicação das alterações do esquema](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Configurar a replicação automática de alterações de esquemas

### <a name="create-a-table-to-track-schema-changes"></a>Criar uma tabela para rastrear as alterações de esquema

Criar uma tabela para rastrear as alterações de esquema em todas as bases de dados do grupo de sincronização:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Esta tabela tem uma coluna de identidade para rastrear a ordem das alterações de esquema. Pode adicionar mais campos para registar mais informações, se necessário.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Criar uma tabela para acompanhar a história das mudanças de esquema

Em todos os pontos finais, crie uma tabela para rastrear o ID do comando de alteração de esquemas mais recentemente aplicado.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Criar um gatilho ALTER TABLE DDL na base de dados onde são feitas alterações de esquemas

Crie um gatilho DDL para operações ALTER TABLE. Basta criar este gatilho na base de dados onde são feitas alterações de esquema. Para evitar conflitos, apenas permita alterações de esquema numa base de dados num grupo de sincronização.

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

O gatilho insere um registo na tabela de rastreio de alteração de esquema para cada comando ALTER TABLE. Este exemplo adiciona um filtro para evitar a replicação de alterações de esquemas feitas no esquema **DataSync**, porque estas são provavelmente feitas pelo serviço Data Sync. Adicione mais filtros se quiser apenas replicar certos tipos de alterações de esquema.

Também pode adicionar mais gatilhos para replicar outros tipos de alterações de esquema. Por exemplo, criar gatilhos CREATE_PROCEDURE, ALTER_PROCEDURE e DROP_PROCEDURE para replicar alterações nos procedimentos armazenados.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Criar um gatilho em outros pontos finais para aplicar alterações de esquema durante a inserção

Este gatilho executa o comando de alteração de esquema quando é sincronizado com outros pontos finais. É necessário criar este gatilho em todos os pontos finais, exceto aquele em que são feitas alterações de esquema (isto é, na base de dados onde o gatilho DDL `AlterTableDDLTrigger` é criado no passo anterior).

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

Este gatilho corre após a inserção e verifica se o comando atual deve ser executado em seguida. A lógica do código garante que nenhuma declaração de alteração de esquema é ignorada, e todas as alterações são aplicadas mesmo que a inserção esteja fora de ordem.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Sincronize a tabela de rastreio de alteração de esquemas para todos os pontos finais

Pode sincronizar a tabela de rastreio de alterações de esquemas para todos os pontos finais utilizando o grupo de sincronização existente ou um novo grupo de sincronização. Certifique-se de que as alterações na tabela de rastreio podem ser sincronizadas em todos os pontos finais, especialmente quando estiver a utilizar uma sincronização de uma direção.

Não sincronize a tabela de histórico de mudança de esquema, uma vez que esta tabela mantém um estado diferente em diferentes pontos finais.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Aplicar as alterações de esquema num grupo de sincronização

Apenas as alterações de esquema es feitas na base de dados onde o gatilho DDL é criado são replicadas. As alterações de esquemas efetuadas noutras bases de dados não são replicadas.

Depois de as alterações de esquema serem replicadas em todos os pontos finais, também é necessário tomar medidas adicionais para atualizar o esquema de sincronização para iniciar ou deixar de sincronizar as novas colunas.

#### <a name="add-new-columns"></a>Adicionar novas colunas

1.  Faça o esquema mudar.

1.  Evite qualquer alteração de dados em que as novas colunas estejam envolvidas até completar o passo que cria o gatilho.

1.  Aguarde até que as alterações do esquema sejam aplicadas a todos os pontos finais.

1.  Refresque o esquema da base de dados e adicione a nova coluna ao esquema de sincronização.

1.  Os dados da nova coluna são sincronizados durante a próxima operação de sincronização.

#### <a name="remove-columns"></a>Remover colunas

1.  Retire as colunas do esquema de sincronização. O Data Sync deixa de sincronizar dados nestas colunas.

1.  Faça o esquema mudar.

1.  Refresque o esquema da base de dados.

#### <a name="update-data-types"></a>Atualizar tipos de dados

1.  Faça o esquema mudar.

1.  Aguarde até que as alterações do esquema sejam aplicadas a todos os pontos finais.

1.  Refresque o esquema da base de dados.

1.  Se os novos e antigos tipos de dados não forem totalmente compatíveis - por exemplo, se mudar de `int` `bigint` para - a sincronização poderá falhar antes de serem concluídos os passos que criam os gatilhos. Sync sucede após uma nova mente.

#### <a name="rename-columns-or-tables"></a>Rebatize colunas ou tabelas

Renomear colunas ou tabelas faz com que o Data Sync deixe de funcionar. Crie uma nova tabela ou coluna, encha os dados e, em seguida, apague a tabela ou coluna antiga em vez de renomear.

#### <a name="other-types-of-schema-changes"></a>Outros tipos de alterações de esquema

Para outros tipos de alterações de esquema - por exemplo, criar procedimentos armazenados ou deixar cair um índice- não é necessário atualizar o esquema de sincronização.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a> Resolução de problemas replicação automática de alterações de esquemas

A lógica de replicação descrita neste artigo deixa de funcionar em algumas situações, por exemplo, se fizer uma alteração de esquema numa base de dados no local que não é suportada na Base de Dados Azure SQL. Nesse caso, a sincronização da tabela de rastreio de mudança de esquema falha. Precisa de corrigir este problema manualmente:

1.  Desative o gatilho DDL e evite alterações de esquemas adicionais até que o problema seja corrigido.

1.  Na base de dados de ponto final onde o problema está a acontecer, desative o gatilho DE INSERÇÃO NO ponto final onde a alteração do esquema não pode ser feita. Esta ação permite sincronizar o comando de alteração de esquemas.

1.  Desencadear sincronização para sincronizar a tabela de rastreio de alterações de esquema.

1.  Na base de dados de ponto final onde o problema está a acontecer, consulte a tabela de histórico de alteração de esquema para obter o ID do último comando de alteração de esquema aplicado.

1.  Consultar a tabela de rastreio de alteração de esquema para listar todos os comandos com um ID maior do que o valor de ID que recuperou no passo anterior.

    a.  Ignore os comandos que não podem ser executados na base de dados do ponto final. Tens de lidar com a inconsistência do esquema. Reverta o esquema original se a inconsistência tiver impacto na sua aplicação.

    b.  Aplique manualmente os comandos que devem ser aplicados.

1.  Atualize a tabela de histórico de alteração de esquema e descreva o último ID aplicado ao valor correto.

1.  Verifique se o esquema está atualizado.

1.  Volte a ativar o gatilho INSERADOR INSactivado no segundo passo.

1.  Reativar o gatilho DDL desativado no primeiro passo.

Se pretender limpar os registos na tabela de rastreio de alteração de esquema, utilize DELETE em vez de TRUNCATE. Nunca volte a utilizar a coluna de identidade na tabela de rastreio de alteração de esquema utilizando o DBCC CHECKIDENT. Pode criar novas tabelas de rastreio de alterações de esquema e atualizar o nome da tabela no gatilho DDL se for necessário reseeding.

## <a name="other-considerations"></a><a name="other"></a> Outras Considerações

-   Os utilizadores de bases de dados que configuram o centro e as bases de dados dos membros precisam de ter permissão suficiente para executar os comandos de alteração de esquemas.

-   Pode adicionar mais filtros no gatilho DDL para apenas replicar a alteração do esquema em tabelas ou operações selecionadas.

-   Só é possível escriminar as alterações de esquema na base de dados onde o gatilho DDL é criado.

-   Se estiver a fazer uma alteração numa base de dados do SQL Server, certifique-se de que a alteração do esquema é suportada na Base de Dados Azure SQL.

-   Se as alterações de esquema forem efetuadas em bases de dados que não sejam a base de dados onde o gatilho DDL é criado, as alterações não são replicadas. Para evitar este problema, pode criar gatilhos DDL para bloquear alterações noutros pontos finais.

-   Se precisar de alterar o esquema da tabela de rastreio de alteração de esquema, desative o gatilho DDL antes de escoar a alteração e, em seguida, aplique manualmente a alteração em todos os pontos finais. A atualização do esquema num gatilho DE INSERÇÃO NA mesma tabela não funciona.

-   Não reseje a coluna de identidade utilizando dBCC CHECKIDENT.

-   Não utilize o TRUNCATE para limpar dados na tabela de rastreio de alterações de esquema.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Visão geral - [Sync dados em várias bases de dados de nuvem e no local com Azure SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar o Data Sync
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados SQL do Azure e o SqL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Utilize o PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Utilize o PowerShell para sincronizar entre uma base de dados na Base de Dados Azure SQL e uma base de dados num caso de Servidor SQL](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-data-sync-agent-overview.md)
-   Melhores práticas - [Melhores práticas para Azure SQL Data Sync](sql-data-sync-best-practices.md)
-   Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](sql-data-sync-monitor-sync.md)
-   Resolução de problemas - [Problemas de resolução de problemas com Azure SQL Data Sync]()
-   Atualizar o esquema de sincronização
    -   Com o PowerShell - [Use o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)
