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
ms.openlocfilehash: c72c4d21f948d6d6c4d1d4598efa0e13de9705a6
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926204"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurar a replicação numa base de dados de instância gerida de base de dados do Azure SQL

Replicação transacional permite-lhe replicar dados num banco de dados de instância gerida de base de dados do Azure SQL de uma base de dados do SQL Server ou outra instância da base de dados. 

Também pode utilizar a replicação transacional para enviar por push as alterações feitas num banco de dados de instância na instância gerida da base de dados do Azure SQL para:

- Uma base de dados do SQL Server.
- Uma base de dados na base de dados do Azure SQL.
- Uma base de dados agrupado num conjunto elástico SQL Database do Azure.
 
Replicação transacional está em pré-visualização pública no [instância gerida de base de dados do Azure SQL](sql-database-managed-instance.md). Uma instância gerida pode alojar as bases de dados do publicador, o distribuidor e o subscritor. Ver [configurações de replicação transacional](sql-database-managed-instance-transactional-replication.md#common-configurations) para configurações disponíveis.

  > [!NOTE]
  > Este artigo destina-se para orientar um usuário na configuração de replicação com uma base de dados do Azure instância gerida de ponta a ponta, começando com a criação do grupo de recursos. Se já gerenciou instâncias implementadas, avançar diretamente para [passo 4](#4---create-a-publisher-database) para criar a sua base de dados do publicador, ou [passo 6](#6---configure-distribution) se já tiver uma base de dados do publicador e o subscritor e estiver pronto para começar configurar a replicação.  

## <a name="requirements"></a>Requisitos

Configurar uma instância gerida a funcionar como um publicador e/ou de um distribuidor requer:

- Que a instância gerida não está atualmente a participar numa relação de georreplicação.
- O publicador de instância gerida está na mesma rede virtual como o distribuidor e o subscritor, ou [vNet peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) tiver sido estabelecida entre as redes virtuais de todos os três entidades. 
- Conectividade utiliza a autenticação do SQL entre os participantes de replicação.
- Um compartilhamento de conta de armazenamento do Azure para o diretório de trabalho de replicação.
- Porta 445 (saída de TCP) está aberta nas regras de segurança de NSG para as instâncias geridas para aceder à partilha de ficheiros do Azure. 


 > [!NOTE]
 > Bases de dados individuais e bases de dados agrupadas na base de dados do Azure SQL só é possível os subscritores. 


## <a name="features"></a>Funcionalidades

Suporta:

- Combinação de replicação de instantâneo e transacional do SQL Server no local e instâncias geridas na base de dados do Azure SQL.
- Os assinantes podem estar no local do SQL Server bases de dados, bases de dados única/geridos instâncias na base de dados do Azure SQL ou bases de dados agrupadas em conjuntos elásticos da base de dados do Azure SQL.
- Unidirecional ou bidirecional de replicação.

As seguintes funcionalidades não são suportadas numa instância gerida na base de dados do Azure SQL:

- [Subscrições atualizáveis](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Georreplicação ativa](sql-database-active-geo-replication.md) e [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md) não deve ser utilizada se a replicação transacional está configurada.
 
## <a name="1---create-a-resource-group"></a>1 - criar um grupo de recursos

Utilize o [portal do Azure](https://portal.azure.com) para criar um grupo de recursos com o nome `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 - criar instâncias geridas

Utilize o [portal do Azure](https://portal.azure.com) para criar dois [instâncias geridas](sql-database-managed-instance-create-tutorial-portal.md) na mesma rede virtual e sub-rede. As duas instâncias geridas devem ter o nome:

- `sql-mi-pub`
- `sql-mi-sub`

Também terá [configurar uma VM do Azure para ligar](sql-database-managed-instance-configure-vm.md) à base de dados SQL do Azure instâncias geridas. 

## <a name="3---create-azure-storage-account"></a>3 - criar conta de armazenamento do Azure

[Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho e, em seguida, crie uma [partilha de ficheiros](../storage/files/storage-how-to-create-file-share.md) na conta de armazenamento. 

Copie o caminho de partilha de ficheiros no formato de: `\\storage-account-name.file.core.windows.net\file-share-name`

Copie as chaves de acesso de armazenamento no formato de: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Para obter mais informações, veja [Ver e copiar chaves de acesso do armazenamento](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4 - criar uma base de dados do publicador

Ligar ao seu `sql-mi-pub` geridos instância com o SQL Server Management Studio e execute o seguinte código Transact-SQL (T-SQL) para criar a sua base de dados do publicador:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - criar uma base de dados do subscritor

Ligar ao seu `sql-mi-sub` geridos instância com o SQL Server Management Studio e execute o seguinte código T-SQL para criar a sua base de dados do subscritor vazio:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - configurar a distribuição

Ligar ao seu `sql-mi-pub` geridos instância com o SQL Server Management Studio e execute o seguinte código T-SQL para configurar a sua base de dados de distribuição. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - configurar o publicador para utilizar o distribuidor 

No seu publicador de instância gerida `sql-mi-pub`, altere a execução de consultas [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) modo e execute o seguinte código para registar o distribuidor de novo no seu publicador. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)';
```

Este script configura um editor local na instância gerida, adiciona um servidor vinculado e cria um conjunto de tarefas para o SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 - criar a publicação e o subscritor

Usando [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) modo, execute o seguinte script de T-SQL para ativar a replicação da base de dados e configurar a replicação entre o publicador, o distribuidor e o subscritor. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - modificar os parâmetros de agente

A instância gerida de base de dados SQL do Azure está atualmente com alguns problemas de back-end com a conectividade com os agentes de replicação. Enquanto este problema está a ser resolvidos resolvido, a solução alternativa para aumentar o valor de tempo limite de início de sessão para os agentes de replicação. 

Execute o seguinte comando do T-SQL no publicador para aumentar o tempo limite de início de sessão: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Execute o seguinte comando de T-SQL novamente para definir o tempo limite de início de sessão para o valor predefinido, caso precise fazer isso:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Reinicie todos os agentes de três para aplicar estas alterações. 

## <a name="10---test-replication"></a>10 - testar a replicação

Assim que a replicação tiver sido configurada, pode testá-lo ao inserir novos itens no publicador e observar as alterações propagadas para o subscritor. 

Execute o seguinte fragmento de T-SQL para ver as linhas no subscritor:

```sql
select * from dbo.ReplTest
```

Execute o seguinte fragmento de T-SQL para inserir linhas adicionais no publicador e, em seguida, verifique as linhas novamente no subscritor. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a publicação, execute o seguinte comando do T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Para remover a opção de replicação da base de dados, execute o seguinte comando do T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Para desativar a publicação e distribuição, execute o seguinte comando do T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Pode limpar os recursos do Azure por [eliminar os recursos de instância gerida de grupo de recursos](../azure-resource-manager/manage-resources-portal.md#delete-resources) e, em seguida, a eliminar o grupo de recursos `SQLMI-Repl`. 

   
## <a name="see-also"></a>Consultar Também

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [O que é uma instância gerida?](sql-database-managed-instance.md)
