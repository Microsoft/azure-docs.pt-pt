---
title: Automatizar a replicação de alterações de esquema sql no SQL Data Sync
description: Saiba automatizar a replicação de alterações de esquemas no Azure SQL Data Sync.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822439"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatizar a replicação de alterações de esquemas no SQL Data Sync do Azure

O SQL Data Sync permite que os utilizadores sincronizem os dados entre as bases de dados Azure SQL e o SQL Server no local numa direção ou em ambas as direções. Uma das limitações atuais do SQL Data Sync é a falta de suporte para a replicação de alterações de esquema. Sempre que muda o esquema da tabela, tem de aplicar as alterações manualmente em todos os pontos finais, incluindo o hub e todos os membros, e depois atualizar o esquema de sincronização.

Este artigo introduz uma solução para replicar automaticamente as alterações de esquema em todos os pontos finais do SQL Data Sync.
1. Esta solução utiliza um gatilho DDL para rastrear as alterações de esquema.
1. O gatilho insere os comandos de alteração de esquemanuma mesa de rastreio.
1. Esta tabela de rastreio está sincronizada em todos os pontos finais utilizando o serviço Data Sync.
1. Os gatilhos DML após a inserção são utilizados para aplicar as alterações de esquema nos outros pontos finais.

Este artigo utiliza a ALTER TABLE como um exemplo de uma mudança de esquema, mas esta solução também funciona para outros tipos de alterações de esquemas.

> [!IMPORTANT]
> Recomendamos que leia este artigo atentamente, especialmente as secções sobre resolução de [problemas](#troubleshoot) e [outras considerações,](#other)antes de começar a implementar a replicação automatizada de alterações de esquemas no seu ambiente de sincronização. Recomendamos também que leia dados sync em várias bases de dados de [nuvem e no local com SQL Data Sync](sql-database-sync-data.md). Algumas operações de base de dados podem quebrar a solução descrita neste artigo. O conhecimento adicional de domínio do SQL Server e do Transact-SQL pode ser necessário para resolver estes problemas.

![Automatizar a replicação das alterações do esquema](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Configurar replicação automatizada de mudança de esquema

### <a name="create-a-table-to-track-schema-changes"></a>Criar uma tabela para acompanhar as alterações do esquema

Crie uma tabela para acompanhar as alterações de esquemaema em todas as bases de dados do grupo de sincronização:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Esta tabela tem uma coluna de identidade para rastrear a ordem das mudanças de esquema. Pode adicionar mais campos para registar mais informações, se necessário.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Criar uma tabela para acompanhar a história das mudanças de esquema

Em todos os pontos finais, crie uma tabela para rastrear a identificação do comando de mudança de esquema mais recentemente aplicado.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Criar um gatilho DDL alter table na base de dados onde são feitas alterações de esquema

Crie um gatilho DDL para operações ALTER TABLE. Basta criar este gatilho na base de dados onde são feitas alterações de esquemas. Para evitar conflitos, apenas permita alterações de esquema numa base de dados num grupo de sincronização.

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

O gatilho insere um registo na tabela de rastreio de alterações de esquemas para cada comando ALTER TABLE. Este exemplo adiciona um filtro para evitar replicar alterações de esquemas feitas no âmbito do Schema **DataSync,** porque estas são provavelmente feitas pelo serviço Data Sync. Adicione mais filtros se quiser apenas replicar certos tipos de alterações de esquema.

Também pode adicionar mais gatilhos para replicar outros tipos de alterações de esquemas. Por exemplo, criar CREATE_PROCEDURE, ALTER_PROCEDURE e DROP_PROCEDURE gatilhos para replicar alterações aos procedimentos armazenados.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Criar um gatilho em outros pontos finais para aplicar alterações de esquema durante a inserção

Este gatilho executa o comando de alteração de esquemas quando está sincronizado com outros pontos finais. É necessário criar este gatilho em todos os pontos finais, exceto aquele em que são feitas `AlterTableDDLTrigger` alterações de esquema (isto é, na base de dados onde o gatilho DDL é criado no passo anterior).

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

Este gatilho funciona após a inserção e verifica se o comando atual deve ser executado a seguir. A lógica do código garante que nenhuma declaração de alteração de esquemas é ignorada, e todas as alterações são aplicadas mesmo que a inserção esteja fora de ordem.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Sincronizar a tabela de rastreio de alterações de esquemas para todos os pontos finais

Pode sincronizar a tabela de rastreio de alterações de esquemas em todos os pontos finais utilizando o grupo de sincronização existente ou um novo grupo de sincronização. Certifique-se de que as alterações na tabela de rastreio podem ser sincronizadas em todos os pontos finais, especialmente quando estiver a utilizar uma sincronização de uma direção.

Não sincronize a tabela de história de mudança de esquemas, uma vez que essa tabela mantém um estado diferente em diferentes pontos finais.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Aplicar as alterações de esquema num grupo de sincronização

Apenas são replicadas alterações de esquema sintetizadas na base de dados onde o gatilho DDL é criado. As alterações de esquemas feitas noutras bases de dados não são replicadas.

Depois de as alterações de esquema serem replicadas em todos os pontos finais, também precisa de tomar medidas adicionais para atualizar o esquema de sincronização para iniciar ou parar de sincronizar as novas colunas.

#### <a name="add-new-columns"></a>Adicione novas colunas

1.  Faça o esquema mudar.

1.  Evite qualquer alteração de dados onde as novas colunas estejam envolvidas até completar o passo que cria o gatilho.

1.  Aguarde até que as alterações de esquema sejam aplicadas em todos os pontos finais.

1.  Refresque o esquema da base de dados e adicione a nova coluna ao esquema de sincronização.

1.  Os dados da nova coluna são sincronizados durante a próxima operação de sincronização.

#### <a name="remove-columns"></a>Remover colunas

1.  Retire as colunas do esquema de sincronização. Data Sync deixa de sincronizar dados nestas colunas.

1.  Faça o esquema mudar.

1.  Refresque o esquema da base de dados.

#### <a name="update-data-types"></a>Atualizar tipos de dados

1.  Faça o esquema mudar.

1.  Aguarde até que as alterações de esquema sejam aplicadas em todos os pontos finais.

1.  Refresque o esquema da base de dados.

1.  Se os novos e antigos tipos de dados não forem totalmente compatíveis - por exemplo, se mudar de `int` - `bigint` a sincronização pode falhar antes de os passos que criam os gatilhos estiverem concluídos. Sync sucede depois de uma nova tentativa.

#### <a name="rename-columns-or-tables"></a>Renomear colunas ou tabelas

Renomear colunas ou tabelas faz com que o Data Sync deixe de funcionar. Crie uma nova tabela ou coluna, encha os dados e, em seguida, elimine a tabela ou coluna antiga em vez de renomear.

#### <a name="other-types-of-schema-changes"></a>Outros tipos de alterações de esquemas

Para outros tipos de alterações de esquemas - por exemplo, não é necessário criar procedimentos armazenados ou deixar cair um índice- atualizar o esquema de sincronização.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a>Resolução de problemas de esquema automatizado de alteração replicação

A lógica de replicação descrita neste artigo deixa de funcionar em algumas situações, por exemplo, se fizer uma alteração de esquema numa base de dados no local que não é suportada na Base de Dados Azure SQL. Nesse caso, sincronizar a tabela de rastreio de alterações de esquemas falha. Precisa de resolver este problema manualmente:

1.  Desative o gatilho DDL e evite alterações adicionais de esquemaaté que o problema seja corrigido.

1.  Na base de dados de ponto final onde o problema está a acontecer, desative o gatilho AFTER INSERT no ponto final onde a alteração do esquema não pode ser feita. Esta ação permite sincronizar o comando de mudança de esquema.

1.  Trigger sync para sincronizar a tabela de rastreio de alterações de esquemas.

1.  Na base de dados final onde o problema está a acontecer, consulte a tabela de história siníma para obter a identificação do último comando de mudança de esquema aplicado.

1.  Consultar a tabela de rastreio de alterações de esquemas para listar todos os comandos com uma identificação superior ao valor de id que recuperou no passo anterior.

    a.  Ignore os comandos que não podem ser executados na base de dados final. Tens de lidar com a inconsistência do esquema. Reverta as alterações de esquema original se a inconsistência afetar a sua aplicação.

    b.  Aplique manualmente os comandos que devem ser aplicados.

1.  Atualize a tabela de história sinopse e coloque o último ID aplicado no valor correto.

1.  Verifique se o esquema está atualizado.

1.  Volte a ativar o gatilho AFTER INSERT desativado no segundo passo.

1.  Reativar o gatilho DDL desativado no primeiro passo.

Se pretender limpar os registos na tabela de rastreio de alterações de esquemas, utilize delete em vez de TRUNCATE. Nunca reseed a coluna de identidade em schema change tracking table utilizando dBCC CHECKIDENT. Pode criar novas tabelas de rastreio de alterações de esquemas e atualizar o nome da tabela no gatilho DDL se for necessário reseeding.

## <a name="other-considerations"></a><a name="other"></a>Outras Considerações

-   Os utilizadores de bases de dados que configuram o hub e as bases de dados dos membros precisam de ter permissão suficiente para executar os comandos de alteração de esquemas.

-   Pode adicionar mais filtros no gatilho DDL para apenas replicar a alteração de esquemas em tabelas ou operações selecionadas.

-   Só é possível fazer alterações de esquema na base de dados onde o gatilho DDL é criado.

-   Se estiver a fazer uma alteração numa base de dados do SQL Server no local, certifique-se de que a alteração de esquema é suportada na Base de Dados Azure SQL.

-   Se forem efetuadas alterações de esquemaema em bases de dados que não sejam a base de dados onde o gatilho DDL é criado, as alterações não são replicadas. Para evitar este problema, pode criar gatilhos DDL para bloquear alterações noutros pontos finais.

-   Se precisar de alterar o esquema da tabela de rastreio de alterações de esquemas, desative o gatilho DDL antes de fazer a alteração e, em seguida, aplique manualmente a alteração em todos os pontos finais. Atualizar o esquema num gatilho PÓS-INSERT na mesma mesa não funciona.

-   Não resemente a coluna de identidade utilizando o DBCC CHECKIDENT.

-   Não utilize o TRUNCATE para limpar os dados na tabela de rastreio de alterações de esquemas.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Visão geral - Sync dados em várias bases de dados de nuvem e no local com O Sincronizado de [Dados Azure SQL](sql-database-sync-data.md)
-   Configurar o Sincronizado de Dados
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o Servidor SQL no local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Boas práticas - [Boas práticas para O Sincronização de Dados Azure SQL](sql-database-best-practices-data-sync.md)
-   Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](sql-database-sync-monitor-oms.md)
-   Troubleshoot - [Problemas com O Sincronizado de Dados Azure SQL](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com powerShell - [Use powerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)
