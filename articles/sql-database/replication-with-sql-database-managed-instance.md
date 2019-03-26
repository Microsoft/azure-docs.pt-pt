---
title: Configurar a replicação numa base de dados de instância gerida de SQL Database do Azure | Documentos da Microsoft
description: Saiba mais sobre como configurar a replicação transacional num banco de dados de instância gerida de base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: b20a119a69ac796bc9ea85083d335f0a7d2fdf2d
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417960"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurar a replicação numa base de dados de instância gerida de base de dados do Azure SQL

Replicação transacional permite-lhe replicar dados num banco de dados de instância gerida de base de dados do Azure SQL de uma base de dados do SQL Server ou outra instância da base de dados. Também pode utilizar a replicação transacional para enviar por push as alterações feitas num banco de dados de instância na instância gerida da base de dados do Azure SQL para uma base de dados do SQL Server, para uma base de dados na base de dados SQL do Azure, para uma base de dados agrupado num conjunto elástico SQL Database do Azure. Replicação transacional está na pré-visualização pública no [instância gerida de base de dados do Azure SQL](sql-database-managed-instance.md). Uma instância gerida pode alojar as bases de dados do publicador, o distribuidor e o subscritor. Ver [configurações de replicação transacional](sql-database-managed-instance-transactional-replication.md#common-configurations) para configurações disponíveis.

## <a name="requirements"></a>Requisitos

Configurar uma instância gerida a funcionar como um publicador ou distribuidor requer:

- Que a instância gerida não está atualmente a participar numa relação de georreplicação.

   >[!NOTE]
   >Bases de dados individuais e bases de dados agrupadas na base de dados do Azure SQL só é possível os subscritores.

- Todas as instâncias geridas tem de estar na mesma vNet.

- Conectividade utiliza a autenticação do SQL entre os participantes de replicação.

- Um compartilhamento de conta de armazenamento do Azure para o diretório de trabalho de replicação.

- Porta 445 (saída de TCP) tem de estar abertas nas regras de segurança de sub-rede da instância gerida para aceder à partilha de ficheiros do Azure

## <a name="features"></a>Funcionalidades

Suporta:

- Combinação de replicação de instantâneo e transacional do SQL Server no local e instâncias geridas na base de dados do Azure SQL.
- Os assinantes podem estar no local do SQL Server bases de dados, bases de dados única/geridos instâncias na base de dados do Azure SQL ou bases de dados agrupadas em conjuntos elásticos da base de dados do Azure SQL.
- Unidirecional ou bidirecional de replicação.

As seguintes funcionalidades não são suportadas numa instância gerida na base de dados do Azure SQL:

- Subscrições atualizáveis.
- [Georreplicação ativa](sql-database-active-geo-replication.md) e [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md) não deve ser utilizada se a replicação transacional está configurada.

## <a name="configure-publishing-and-distribution-example"></a>Configurar o exemplo de publicação e distribuição

1. [Criar uma instância gerida da base de dados do Azure SQL](sql-database-managed-instance-create-tutorial-portal.md) no portal.
2. [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho.

   Certifique-se de que copie as chaves de armazenamento. Ver [ver e copiar chaves de acesso de armazenamento](../storage/common/storage-account-manage.md#access-keys
).
3. Crie uma base de dados de instância para o publicador.

   Nos scripts de exemplo abaixo, substitua `<Publishing_DB>` com o nome da base de dados de instância.

4. Crie um utilizador de base de dados com a autenticação de SQL para o distribuidor. Utilize uma palavra-passe segura.

   Nos scripts de exemplo abaixo, utilize `<SQL_USER>` e `<PASSWORD>` com esta conta do SQL Server da base de dados utilizador e palavra-passe.

5. [Ligue-se para a instância gerida da base de dados SQL](sql-database-connect-query-ssms.md).

6. Execute a consulta seguinte para adicionar o distribuidor e a base de dados de distribuição.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Para configurar um publicador para utilizar uma base de dados de distribuição especificado, atualize e execute a consulta seguinte.

   Substitua `<SQL_USER>` e `<PASSWORD>` com a conta do SQL Server e a palavra-passe.

   Substitua `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` com o valor da sua conta de armazenamento.  

   Substitua `<STORAGE_CONNECTION_STRING>` com a cadeia de ligação a partir de **chaves de acesso** separador da sua conta de armazenamento do Microsoft Azure.

   Depois de atualizar a seguinte consulta, executá-lo.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Configure o publicador para a replicação.

    Na consulta seguinte, substitua `<Publishing_DB>` com o nome da sua base de dados do publicador.

    Substitua `<Publication_Name>` com o nome da sua publicação.

    Substitua `<SQL_USER>` e `<PASSWORD>` com a conta do SQL Server e a palavra-passe.

    Depois de atualizar a consulta, execute-o para criar a publicação.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Adicione o artigo, a subscrição e o agente de subscrição de push.

   Para adicionar esses objetos, atualize o seguinte script.

   - Substitua `<Object_Name>` com o nome do objeto de publicação.
   - Substitua `<Object_Schema>` com o nome do esquema de origem.
   - Substitua os outros parâmetros parênteses angulares `<>` de acordo com os valores nos scripts do anteriores.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>Consultar Também

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [O que é uma instância gerida?](sql-database-managed-instance.md)
