---
title: Configure a replicação entre duas instâncias geridas pelo Azure SQL
titleSuffix: Azure SQL Managed Instance
description: Este tutorial ensina-o a configurar a replicação transacional entre um editor/distribuidor de instânciagerida Azure SQL e um assinante SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 5603c6a828eb27bec43cf1fcb1924ad3ec430685
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051830"
---
# <a name="tutorial-configure-replication-between-two-azure-sql-managed-instances"></a>Tutorial: Configure a replicação entre duas instâncias geridas pelo Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional permite-lhe replicar dados de uma base de dados para outra hospedada no SQL Server, ou numa [Instância Gerida Azure SQL](sql-managed-instance-paas-overview.md) (pré-visualização pública). Um SQL Managed Instance pode ser um editor, distribuidor ou assinante na topologia de replicação. Consulte as configurações de [replicação transacional](replication-transactional-overview.md#common-configurations) para obter configurações disponíveis.

> [!NOTE]
> Este artigo descreve a utilização da [replicação transacional](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) em Caso Gerido Azure SQL. Não está relacionado com [grupos failover](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group), uma funcionalidade De Instância Gerida Azure SQL que lhe permite criar réplicas legíveis completas de instâncias individuais.

Este tutorial ensina-o a configurar um SQL Managed Instance como editor e distribuidor, e depois um segundo SQL Managed Instance como assinante.  

![Replicar entre dois casos geridos](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

  > [!NOTE]
  > - Este artigo destina-se a orientar um utilizador avançado na configuração da replicação com uma Instância Gerida SQL de ponta a ponta, começando pela criação do grupo de recursos. Se já tiver gerido as instâncias implementadas, salte para o [Passo 4](#4---create-a-publisher-database) para criar a sua base de dados de editores, ou [passo 6](#6---configure-distribution) se já tiver uma base de dados de editores e assinantes, e estiver pronto para começar a configurar a replicação.  
  > - Este artigo confunde o seu editor e distribuidor na mesma instância gerida. Para colocar o distribuidor numa instância separada, consulte a replicação tutorial [Configure entre um editor de MI e um distribuidor MI](replication-two-instances-and-sql-server-configure-tutorial.md). 

## <a name="requirements"></a>Requisitos

Configurar uma Instância Gerida SQL para funcionar como editor e/ou distribuidor requer:

- Que a editora SQL Managed Instance está na mesma rede virtual que o distribuidor e o assinante, ou [o peering vNet](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) foi configurado entre as redes virtuais das três entidades. 
- A conectividade utiliza a Autenticação SQL entre os participantes da replicação.
- Uma conta de armazenamento Azure partilhada para o diretório de trabalho de replicação.
- A porta 445 (saída de TCP) está aberta nas regras de segurança do NSG para as Instâncias Geridas sQL aceder em função da partilha de ficheiros Azure.  Se encontrar o erro, terá de adicionar uma regra de `failed to connect to azure storage \<storage account name> with os error 53` saída ao NSG da subnet SQL Managed Instance apropriada.

## <a name="1---create-a-resource-group"></a>1 - Criar um grupo de recursos

Utilize o [portal Azure](https://portal.azure.com) para criar um grupo de recursos com o nome `SQLMI-Repl` .  

## <a name="2---create-sql-managed-instances"></a>2 - Criar Instâncias Geridas SQL

Utilize o [portal Azure](https://portal.azure.com) para criar duas [Instâncias Geridas SQL](instance-create-quickstart.md) na mesma rede virtual e subnet. Por exemplo, nomeie as duas Instâncias Geridas SQL:

- `sql-mi-pub`(juntamente com alguns caracteres para aleatoriedade)
- `sql-mi-sub`(juntamente com alguns caracteres para aleatoriedade)

Também terá de [configurar um VM Azure para se ligar](connect-vm-instance-configure.md) aos seus Casos Geridos SQL. 

## <a name="3---create-azure-storage-account"></a>3 - Criar conta de armazenamento azure

[Crie uma Conta](/azure/storage/common/storage-create-storage-account#create-a-storage-account) de Armazenamento Azure para o diretório de trabalho e, em seguida, crie uma parte de [ficheiro](../../storage/files/storage-how-to-create-file-share.md) dentro da conta de armazenamento. 

Copiar o caminho da partilha de ficheiros no formato de:`\\storage-account-name.file.core.windows.net\file-share-name`

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copiar as chaves de acesso ao armazenamento no formato de:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Para mais informações, consulte Gerir as chaves de [acesso à conta](../../storage/common/storage-account-keys-manage.md)de armazenamento . 

## <a name="4---create-a-publisher-database"></a>4 - Criar uma base de dados de editores

Conecte-se à sua Instância Gerida SQL utilizando o Estúdio de Gestão de `sql-mi-pub` Servidores SQL e execute o seguinte código Transact-SQL (T-SQL) para criar a sua base de dados de editores:

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

Conecte-se à sua Instância Gerida SQL utilizando o Estúdio de Gestão de `sql-mi-sub` Servidores SQL e execute o seguinte código T-SQL para criar a sua base de dados de assinantes vazia:

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

Conecte-se à sua Instância Gerida sQL utilizando o Estúdio de Gestão de `sql-mi-pub` Servidores SQL e execute o seguinte código T-SQL para configurar a sua base de dados de distribuição.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Configure o editor para usar o distribuidor

Na sua editora SQL Managed `sql-mi-pub` Instance, altere a execução da consulta para o modo [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) e execute o seguinte código para registar o novo distribuidor com o seu editor.

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
   > Certifique-se de que utiliza apenas as pestanas traseiras `\` para o parâmetro file_storage. A utilização de um corte dianteiro `/` () pode causar um erro ao ligar-se à parte do ficheiro.

Este script configura uma editora local no SQL Managed Instance, adiciona um servidor ligado e cria um conjunto de trabalhos para o SQL Server Agent.

## <a name="8---create-publication-and-subscriber"></a>8 - Criar publicação e assinante

Utilizando o modo [SQLCMD,](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) execute o seguinte script T-SQL para permitir a replicação para a sua base de dados e configure a replicação entre o seu editor, distribuidor e assinante.

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

A Azure SQL Managed Instance está atualmente a ter alguns problemas de backend com conectividade com os agentes de replicação. Enquanto esta questão está a ser abordada, a supressão é aumentar o valor de tempo de início de sessão para os agentes de replicação.

Executar o seguinte comando T-SQL na editora para aumentar o tempo de início de sessão:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Executar novamente o seguinte comando T-SQL para definir o tempo de início de sessão de volta ao valor predefinido, caso precise de o fazer:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Reinicie os três agentes para aplicar estas alterações.

## <a name="10---test-replication"></a>10 - Replicação de teste

Uma vez configurada a replicação, pode testá-la inserindo novos itens na editora e assistindo às alterações propagadas ao assinante.

Executar o seguinte corte T-SQL para ver as linhas no assinante:

```sql
select * from dbo.ReplTest
```

Faça o seguinte corte T-SQL para inserir linhas adicionais na editora e, em seguida, verifique novamente as linhas no assinante.

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar recursos

Para deixar cair a publicação, execute o seguinte comando T-SQL:

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

Pode limpar os seus recursos Azure [apagando os recursos SQL Managed Instance do grupo de recursos](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e, em seguida, apagando o grupo de recursos `SQLMI-Repl` . 

## <a name="next-steps"></a>Próximos passos

Também pode aprender mais informações sobre [replicação transacional com o Azure SQL Managed Instance](replication-transactional-overview.md) ou aprender a configurar a replicação entre um [Editor/Distribuidor de Instâncias Geridas SQL e um SQL no subscritor Azure VM](replication-two-instances-and-sql-server-configure-tutorial.md). 
