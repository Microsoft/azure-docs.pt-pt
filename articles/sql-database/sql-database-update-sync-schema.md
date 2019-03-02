---
title: Automatizar a replicação das alterações de esquema na sincronização de dados SQL do Azure | Documentos da Microsoft
description: Saiba como automatizar a replicação das alterações de esquema na sincronização de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/14/2018
ms.openlocfilehash: f53652256dc2357309f5131e543d27f85589f63c
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215848"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatizar a replicação das alterações de esquema na sincronização de dados SQL do Azure

Sincronização de dados SQL permite que os utilizadores a sincronizar dados entre bases de dados SQL do Azure e no local do SQL Server numa direção ou em ambas as direções. Uma das atuais limitações de sincronização de dados SQL é a falta de suporte para a replicação de alterações de esquema. Sempre que alterar o esquema da tabela, terá de aplicar as alterações manualmente em todos os pontos finais, incluindo o hub e todos os membros e, em seguida, atualizar o esquema de sincronização.

Este artigo apresenta uma solução de replicar automaticamente as alterações de esquema para todos os pontos finais de sincronização de dados SQL.
1. Esta solução utiliza um acionador DDL para controlar as alterações de esquema.
1. O acionador insere os comandos de alteração de esquema numa tabela de controle.
1. Esta tabela de controle está sincronizada com todos os pontos finais com o serviço de sincronização de dados.
1. Acionadores DML após a inserção são utilizados para aplicar as alterações de esquema em outros pontos de extremidade.

Este artigo utiliza ALTER TABLE como um exemplo de uma alteração de esquema, mas esta solução também funciona para outros tipos de alterações de esquema.

> [!IMPORTANT]
> Recomendamos que leia este artigo com cuidado, especialmente as secções sobre [resolução de problemas](#troubleshoot) e [outras considerações](#other), antes de começar a implementar a replicação de alteração de esquema automatizada em o ambiente de sincronização. Também recomendamos que leia [sincronizar dados em várias bases de dados na cloud e no local com a sincronização de dados SQL](sql-database-sync-data.md). Algumas operações de base de dados poderão interromper a solução descrita neste artigo. Dados de conhecimento de domínio adicional do SQL Server e o Transact-SQL podem ser necessário para resolver esses problemas.

![Automatizar a replicação de alterações de esquema](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Configurar a replicação de alteração de esquema automatizada

### <a name="create-a-table-to-track-schema-changes"></a>Criar uma tabela para controlar as alterações de esquema

Crie uma tabela para controlar as alterações de esquema em todas as bases de dados no grupo de sincronização:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Esta tabela tem uma coluna de identidade para controlar a ordem das alterações de esquema. Pode adicionar mais campos para obter mais informações de registo, se necessário.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Criar uma tabela para controlar o histórico de alterações de esquema

Em todos os pontos finais, crie uma tabela para controlar o ID do comando de alteração de esquema mais recentemente aplicado.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Criar um acionador de DDL ALTER de tabela na base de dados em que são efetuadas alterações de esquema

Crie um acionador DDL para operações de ALTER TABLE. Só precisa de criar este acionador na base de dados em que são efetuadas alterações de esquema. Para evitar conflitos, permitir apenas as alterações de esquema num banco de dados num grupo de sincronização.

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

O acionador insere um registo na tabela para cada comando ALTER TABLE do registo de alterações de esquema. Este exemplo adiciona um filtro para evitar a replicação de alterações de esquema feitas no esquema **DataSync**, porque estes são mais prováveis feitas pelo serviço de sincronização de dados. Adicione mais filtros se pretender replicar determinados tipos de alterações de esquema.

Também pode adicionar mais acionadores para replicar a outros tipos de alterações de esquema. Por exemplo, crie acionadores CREATE_PROCEDURE, ALTER_PROCEDURE e DROP_PROCEDURE para replicar as alterações para procedimentos armazenados.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Criar acionadores em outros pontos de extremidade para aplicar as alterações de esquema durante a inserção

Este acionador é executado o comando de alteração de esquema, quando ele está sincronizado com outros pontos de extremidade. Tem de criar este acionador em todos os pontos finais, exceto aquele em que são efetuadas alterações de esquema (ou seja, na base de dados em que a DDL acionar `AlterTableDDLTrigger` é criado no passo anterior).

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

Este acionador é executado após a inserção e verifica se o comando atual deve executar o seguinte. A lógica de código garante que nenhuma declaração de alteração de esquema é ignorada e todas as alterações são aplicadas, mesmo que a inserção está fora de ordem.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>O controlo de tabela para todos os pontos finais de alteração de esquema de sincronização

Pode sincronizar o controlo de tabela para todos os pontos finais com o grupo de sincronização existente ou um novo grupo de sincronização de alterações de esquema. Certifique-se de que podem ser sincronizadas as alterações na tabela de controle para todos os pontos finais, especialmente quando estiver a utilizar uma direção sincronização.

Não sincronize a tabela de histórico de alterações de esquema, uma vez que essa tabela mantém o estado de diferente em diferentes pontos de extremidade.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Aplicar as alterações de esquema num grupo de sincronização

São replicadas apenas alterações de esquema feitas na base de dados em que o acionador DDL é criado. Não são replicadas as alterações de esquema feitas noutras bases de dados.

Depois das alterações de esquema são replicadas para todos os pontos finais, terá também de executar passos adicionais para atualizar o esquema de sincronização para iniciar ou parar a sincronizar as novas colunas.

#### <a name="add-new-columns"></a>Adicionar novas colunas

1.  Verifique o esquema alterar.

1.  Evite qualquer alteração de dados em que as novas colunas envolvidas até concluir o passo que cria o acionador.

1.  Aguarde até que as alterações de esquema são aplicadas a todos os pontos finais.

1.  Atualizar o esquema de base de dados e adicionar a nova coluna ao esquema de sincronização.

1.  Dados da coluna nova estão sincronizados durante a próxima operação de sincronização.

#### <a name="remove-columns"></a>Remover colunas

1.  Remova as colunas de esquema de sincronização. Sincronização de dados para sincronizar dados nessas colunas.

1.  Verifique o esquema alterar.

1.  Atualize o esquema de base de dados.

#### <a name="update-data-types"></a>Tipos de dados de atualização

1.  Verifique o esquema alterar.

1.  Aguarde até que as alterações de esquema são aplicadas a todos os pontos finais.

1.  Atualize o esquema de base de dados.

1.  Se os tipos de dados novas e antigas não são totalmente compatíveis - por exemplo, se alterar do `int` para `bigint` -sincronização poderão falhar antes de concluir os passos que criar os acionadores. Sincronização for concluída com êxito após uma repetição.

#### <a name="rename-columns-or-tables"></a>Mudar o nome de colunas ou tabelas

Mudar o nome de colunas ou tabelas faz a sincronização de dados que deixam de funcionar. Criar uma nova tabela ou coluna, preenchimento os dados e, em seguida, elimine o antiga tabela ou coluna em vez de mudar o nome.

#### <a name="other-types-of-schema-changes"></a>Outros tipos de alterações de esquema

Para outros tipos de alterações de esquema - por exemplo, criar procedimentos armazenados ou remover um índice - atualizar o esquema de sincronização não é necessário.

## <a name="troubleshoot"></a> Resolver problemas de replicação de alteração de esquema automatizada

A lógica de replicação descrita neste artigo deixa de funcionar em algumas situações - por exemplo, se tiver efetuado um esquema alterar numa base de dados no local que não é suportado na base de dados do Azure SQL. Nesse caso, a sincronizar a tabela de controlo de alterações de esquema falha. Tem de corrigir esse problema manualmente:

1.  Desativar o acionador DDL e evitar qualquer alteração de esquema adicional até o problema ser corrigido.

1.  A base de dados de ponto final, onde o problema está a acontecer, desabilite o disparador de depois de inserir de mensagens em fila no ponto final onde não é possível efetuar a alteração de esquema. Esta ação permite que o comando de alteração de esquema a ser sincronizada.

1.  Acionar a sincronização para sincronizar a tabela de controlo de alterações de esquema.

1.  A base de dados de ponto final, onde o problema está a acontecer, consulta o esquema alterar tabela de histórico para obter o ID do último comando de alteração de esquema aplicada.

1.  Consulte a tabela para listar todos os comandos com um ID maior que o valor de ID que obteve no passo anterior de controlo de alterações de esquema.

    a.  Ignore esses comandos que não não possível executar a base de dados do ponto final. Terá de lidar com a inconsistência de esquema. Reverta as alterações de esquema original se a inconsistência tem impacto sobre a sua aplicação.

    b.  Aplica manualmente esses comandos que devem ser aplicados.

1.  Atualizar a tabela de histórico de alterações de esquema e defina o último ID aplicado para o valor correto.

1.  Verificar se o esquema está atualizado.

1.  Voltar a ativar o acionador depois de inserir desabilitado na segunda etapa.

1.  Voltar a ativar o acionador DDL desabilitado no primeiro passo.

Se quiser limpar os registros na tabela de controle de alteração de esquema, utilize DELETE em vez de TRUNCATE. Nunca propagar novamente a coluna de identidade na tabela de registo utilizando o DBCC CHECKIDENT de alterações de esquema. Pode criar tabelas de controlo de alteração de esquema novo e atualizar o nome da tabela em que o acionador DDL se reseeding for necessário.

## <a name="other"></a> Outras considerações

-   Os utilizadores de base de dados que configurar as bases de dados de hub e member tem de ter permissões suficientes para executar os comandos de alteração de esquema.

-   Pode adicionar mais filtros no acionador DDL para replicar somente a alteração de esquema de tabelas selecionadas ou operações.

-   Só pode efetuar alterações de esquema na base de dados em que o acionador DDL é criado.

-   Se estiver a efetuar uma alteração numa base de dados do SQL Server no local, certifique-se de que a alteração de esquema é suportada na base de dados do Azure SQL.

-   Se forem feitas alterações de esquema em bases de dados que não seja a base de dados em que o acionador DDL é criado, as alterações não são replicadas. Para evitar este problema, pode criar acionadores DDL para bloquear as alterações em outros pontos de extremidade.

-   Se precisar de alterar o esquema da tabela de controlo de alteração de esquema, desative o acionador DDL antes de efetuar a alteração e, em seguida, aplicar a alteração manualmente para todos os pontos finais. Atualizar o esquema num acionador depois de inserir na mesma tabela não funciona.

-   Não propagar novamente a coluna de identidade ao utilizar DBCC CHECKIDENT.

-   Não utilize TRUNCATE a limpeza dos dados em tabela do registo de alterações de esquema.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Descrição geral - [sincronizar dados em várias bases de dados na cloud e no local com sincronização de dados SQL do Azure](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal - [Tutorial: Configurar a sincronização de dados SQL para sincronizar dados entre a base de dados do Azure SQL e SQL Server no local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de - de sincronização de dados [agente de sincronização de dados SQL do Azure de sincronização de dados](sql-database-data-sync-agent.md)
-   Melhores práticas - [melhores práticas para a sincronização de dados SQL do Azure](sql-database-best-practices-data-sync.md)
-   Monitor de - [registos de monitorizar a sincronização de dados de SQL com o Azure Monitor](sql-database-sync-monitor-oms.md)
-   Resolução de problemas - [solucionar problemas com a sincronização de dados SQL do Azure](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o PowerShell - [utilize o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)
