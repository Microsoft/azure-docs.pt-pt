---
title: Configure a replicação entre instâncias geridas
titleSuffix: Azure SQL Managed Instance
description: Este tutorial ensina-o a configurar a replicação transacional entre um editor/distribuidor de Instância Gerida Azure SQL e um assinante da SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 47ea5ea73c581313f90791ca6d7892ebad3f666b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690690"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Tutorial: Configurar a replicação entre dois casos geridos

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional permite replicar dados de uma base de dados para outra hospedada no SQL Server ou [no Azure SQL Managed Instance](sql-managed-instance-paas-overview.md). SQL Managed Instance pode ser um editor, distribuidor ou assinante na topologia de replicação. Consulte [as configurações de replicação transacional](replication-transactional-overview.md#common-configurations) para as configurações disponíveis. 

A replicação transacional está atualmente em pré-visualização pública para SQL Managed Instance. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Configure um caso gerido como editor e distribuidor de replicação.
> - Configurar um caso gerido como assinante de replicação.

![Replicar entre dois casos geridos](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Este tutorial destina-se a um público experiente e assume que o utilizador está familiarizado com a implementação e ligação a instâncias geridas e VMs do SQL Server dentro do Azure. 


> [!NOTE]
> - Este artigo descreve o uso de [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) em Azure SQL Managed Instance. Não está relacionado com [grupos de failover](../database/auto-failover-group-overview.md), uma funcionalidade de Instância Gerida Azure SQL que permite criar réplicas legíveis completas de instâncias individuais. Existem considerações adicionais ao configurar a [replicação transacional com grupos de failover](replication-transactional-overview.md#with-failover-groups).



## <a name="requirements"></a>Requisitos

Configurar a SQL Managed Instance para funcionar como editor e/ou distribuidor requer:

- Que a editora geriu o exemplo está na mesma rede virtual que o distribuidor e o assinante, ou [gateways VPN](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) foram configurados entre as redes virtuais das três entidades. 
- A conectividade utiliza a Autenticação SQL entre os participantes da replicação.
- Uma parte da conta de armazenamento Azure para o diretório de trabalho de replicação.
- A Porta 445 (saída TCP) está aberta nas regras de segurança da NSG para as instâncias geridas para aceder à partilha de ficheiros Azure.  Se encontrar o `failed to connect to azure storage <storage account name> with os error 53` erro, terá de adicionar uma regra de saída ao NSG da sub-rede de instância gerida do SQL apropriado.

## <a name="1---create-a-resource-group"></a>1 - Criar um grupo de recursos

Utilize o [portal Azure](https://portal.azure.com) para criar um grupo de recursos com o nome `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2 - Criar instâncias geridas

Utilize o [portal Azure](https://portal.azure.com) para criar [duas ocorrências geridas SQL](instance-create-quickstart.md) na mesma rede virtual e sub-rede. Por exemplo, nomeie as duas instâncias geridas:

- `sql-mi-pub` (juntamente com alguns caracteres para aleatoriedade)
- `sql-mi-sub` (juntamente com alguns caracteres para aleatoriedade)

Também terá de [configurar um Azure VM para se ligar](connect-vm-instance-configure.md) às suas instâncias geridas. 

## <a name="3---create-an-azure-storage-account"></a>3 - Criar uma conta de armazenamento Azure

[Crie uma conta de armazenamento Azure](../../storage/common/storage-account-create.md#create-a-storage-account) para o diretório de trabalho e, em seguida, crie uma [parte de arquivo](../../storage/files/storage-how-to-create-file-share.md) dentro da conta de armazenamento. 

Copie o caminho da partilha de ficheiros no formato de: `\\storage-account-name.file.core.windows.net\file-share-name`

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie as teclas de acesso ao armazenamento no formato de: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../../storage/common/storage-account-keys-manage.md) 

## <a name="4---create-a-publisher-database"></a>4 - Criar uma base de dados de editores

Ligue-se à sua `sql-mi-pub` instância gerida utilizando o SQL Server Management Studio e execute o seguinte código Transact-SQL (T-SQL) para criar a sua base de dados de editores:

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

## <a name="5---create-a-subscriber-database"></a>5 - Criar uma base de dados de assinantes

Ligue-se à sua `sql-mi-sub` instância gerida utilizando o SQL Server Management Studio e execute o seguinte código T-SQL para criar a sua base de dados de assinantes vazia:

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

## <a name="6---configure-distribution"></a>6 - Distribuição de configuração

Ligue-se à sua `sql-mi-pub` instância gerida utilizando o SQL Server Management Studio e execute o seguinte código T-SQL para configurar a sua base de dados de distribuição.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Configure editor para usar distribuidor

No seu editor SQL Managed `sql-mi-pub` Instance, altere a execução de consulta para o modo [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) e execute o seguinte código para registar o novo distribuidor com o seu editor.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Certifique-se de que utiliza apenas as pestanas `\` para o parâmetro file_storage. A utilização de um corte dianteiro `/` () pode causar um erro ao ligar-se à partilha de ficheiros.

Este script configura um editor local na instância gerida, adiciona um servidor ligado e cria um conjunto de trabalhos para o agente do SQL Server.

## <a name="8---create-publication-and-subscriber"></a>8 - Criar publicação e assinante

Utilizando o modo [SQLCMD,](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) execute o seguinte script T-SQL para permitir a replicação da sua base de dados e configurar a replicação entre o seu editor, distribuidor e assinante.

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


-- Configure your log reader agent
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
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Modificar os parâmetros do agente

A Azure SQL Managed Instance está atualmente a ter alguns problemas de backend com conectividade com os agentes de replicação. Enquanto este problema está a ser abordado, a solução é aumentar o valor de tempo limite de login para os agentes de replicação.

Executar o seguinte comando T-SQL na editora para aumentar o tempo de início de sessão:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Volte a executar o seguinte comando T-SQL para definir o tempo limite de início de início de volta para o valor predefinido, caso necessite de o fazer:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Reinicie os três agentes para aplicar estas alterações.

## <a name="10---test-replication"></a>10 - Replicação do teste

Uma vez configurada a replicação, pode testá-la inserindo novos itens na editora e assistindo às alterações que se propagam ao assinante.

Executar o seguinte corte T-SQL para ver as linhas no assinante:

```sql
select * from dbo.ReplTest
```

Execute o seguinte corte T-SQL para inserir linhas adicionais na editora e, em seguida, verifique novamente as linhas no assinante.

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para deixar cair a publicação, executar o seguinte comando T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Para remover a opção de replicação da base de dados, execute o seguinte comando T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Para desativar a publicação e distribuição, executar o seguinte comando T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Pode limpar os seus recursos Azure [eliminando os recursos da SQL Managed Instance do grupo de recursos](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e, em seguida, eliminando o grupo de recursos `SQLMI-Repl` . 

## <a name="next-steps"></a>Passos seguintes

Também pode aprender mais informações sobre [a replicação transacional com a Azure SQL Managed Instance](replication-transactional-overview.md) ou aprender a configurar a replicação entre um [editor/distribuidor sql Managed Instance e um SQL no assinante Azure VM.](replication-two-instances-and-sql-server-configure-tutorial.md)