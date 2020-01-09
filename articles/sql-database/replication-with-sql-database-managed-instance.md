---
title: Configurar a replicação em um banco de dados de instância gerenciada
description: Saiba como configurar a replicação transacional entre um Publicador/distribuidor de instância gerenciada do banco de dados SQL do Azure e um assinante de instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: fd881142e0260d313e197d5e40ae25a2621646df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372482"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurar a replicação em um banco de dados de instância gerenciada do banco de dados SQL

A replicação transacional permite replicar dados em um banco de dado de instância gerenciada do banco de dados SQL do Azure a partir de um banco de SQL Server ou de outro. 

Este artigo mostra como configurar a replicação entre um Publicador/distribuidor de instância gerenciada e um assinante de instância gerenciada. 

![Replicar entre duas instâncias gerenciadas](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Você também pode usar a replicação transacional para enviar por push as alterações feitas em um banco de dados de instância na instância gerenciada do banco de dados SQL do Azure para:

- Um banco de dados SQL Server.
- Um banco de dados individual no banco de dados SQL do Azure.
- Um banco de dados em pool em um pool elástico do banco de dados SQL do Azure.
 
A replicação transacional está em visualização pública na [instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance.md). Uma instância gerenciada pode hospedar bancos de dados de Publicador, distribuidor e Assinante. Consulte [configurações de replicação transacional](sql-database-managed-instance-transactional-replication.md#common-configurations) para configurações disponíveis.

  > [!NOTE]
  > - Este artigo destina-se a orientar um usuário na configuração de replicação com uma instância gerenciada do banco de dados do Azure de ponta a ponta, começando com a criação do grupo de recursos. Se você já tiver instâncias gerenciadas implantadas, pule para a [etapa 4](#4---create-a-publisher-database) para criar o banco de dados do Publicador ou a [etapa 6](#6---configure-distribution) se você já tiver um banco de dados do Publicador e do assinante e estiver pronto para começar a configurar a replicação.  
  > - Este artigo configura o Publicador e o distribuidor na mesma instância gerenciada. Para posicionar o distribuidor em uma instância gerenciada separada, consulte o tutorial [Configurar a replicação entre um Publicador de mi e um distribuidor de mi](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Requisitos

Configurar uma instância gerenciada para funcionar como um Publicador e/ou um distribuidor requer:

- Que a instância gerenciada não está participando atualmente de uma relação de replicação geográfica.
- Que a instância gerenciada pelo Publicador está na mesma rede virtual que o distribuidor e o Assinante, ou o [emparelhamento vNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) foi estabelecido entre as redes virtuais de todas as três entidades. 
- A conectividade utiliza a Autenticação SQL entre os participantes da replicação.
- Um compartilhamento de conta de armazenamento do Azure para o diretório de trabalho de replicação.
- A porta 445 (TCP de saída) está aberta nas regras de segurança do NSG para as instâncias gerenciadas para acessar o compartilhamento de arquivos do Azure.  Se você encontrar o erro "falha ao conectar-se ao armazenamento do Azure \<nome da conta de armazenamento > com o erro 53 do so", será necessário adicionar uma regra de saída ao NSG da sub-rede do SQL Instância Gerenciada apropriada.


 > [!NOTE]
 > Os bancos de dados individuais e os bancos de dados em pool no banco de dados SQL do Azure só podem ser assinantes. 


## <a name="features"></a>Funcionalidades

Suportar

- A combinação de replicação transacional e de instantâneo de SQL Server instâncias locais e gerenciadas no banco de dados SQL do Azure.
- Os assinantes podem estar no local SQL Server bancos de dados, em bancos de dados individuais/instâncias gerenciadas no banco de dados SQL do Azure ou em bancos de dados em pool em pools elásticos do banco de dados SQL do Azure.
- Replicação unidirecional ou bidirecional.

Os recursos a seguir não têm suporte em uma instância gerenciada no banco de dados SQL do Azure:

- [Assinaturas atualizáveis](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Replicação geográfica ativa](sql-database-active-geo-replication.md) com replicação transacional. Em vez de replicação geográfica ativa, use [grupos de failover automático](sql-database-auto-failover-group.md), mas observe que a publicação precisa ser [excluída manualmente](sql-database-managed-instance-transact-sql-information.md#replication) da instância gerenciada primária e recriada na instância gerenciada secundária após o failover.  
 
## <a name="1---create-a-resource-group"></a>1-criar um grupo de recursos

Use o [portal do Azure](https://portal.azure.com) para criar um grupo de recursos com o nome `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2-criar instâncias gerenciadas

Use o [portal do Azure](https://portal.azure.com) para criar duas [instâncias gerenciadas](sql-database-managed-instance-create-tutorial-portal.md) na mesma rede virtual e sub-rede. Por exemplo, nomeie as duas instâncias gerenciadas:

- `sql-mi-pub` (juntamente com alguns caracteres para randomização)
- `sql-mi-sub` (juntamente com alguns caracteres para randomização)

Você também precisará [Configurar uma VM do Azure para se conectar](sql-database-managed-instance-configure-vm.md) às instâncias gerenciadas do banco de dados SQL do Azure. 

## <a name="3---create-azure-storage-account"></a>3-criar conta de armazenamento do Azure

[Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho e, em seguida, crie um [compartilhamento de arquivos](../storage/files/storage-how-to-create-file-share.md) dentro da conta de armazenamento. 

Copie o caminho do compartilhamento de arquivos no formato: `\\storage-account-name.file.core.windows.net\file-share-name`

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie as chaves de acesso de armazenamento no formato: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Para obter mais informações, consulte [gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4-criar um banco de dados do Publicador

Conecte-se à instância gerenciada `sql-mi-pub` usando SQL Server Management Studio e execute o seguinte código Transact-SQL (T-SQL) para criar o banco de dados do Publicador:

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

## <a name="5---create-a-subscriber-database"></a>5-criar um banco de dados do assinante

Conecte-se à sua instância gerenciada `sql-mi-sub` usando SQL Server Management Studio e execute o seguinte código T-SQL para criar seu banco de dados de assinante vazio:

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

## <a name="6---configure-distribution"></a>6-configurar a distribuição

Conecte-se à sua instância gerenciada do `sql-mi-pub` usando SQL Server Management Studio e execute o código T-SQL a seguir para configurar o banco de dados de distribuição. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-configurar o Publicador para usar o distribuidor 

Em sua instância gerenciada do Publicador `sql-mi-pub`, altere a execução da consulta para o modo [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) e execute o código a seguir para registrar o novo distribuidor com o seu editor. 

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
   > Certifique-se de usar apenas barras invertidas (`\`) para o parâmetro file_storage. O uso de uma barra (`/`) pode causar um erro ao se conectar ao compartilhamento de arquivos. 

Esse script configura um Publicador local na instância gerenciada, adiciona um servidor vinculado e cria um conjunto de trabalhos para o SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8-criar publicação e assinante

Usando o modo [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) , execute o seguinte script T-SQL para habilitar a replicação para seu banco de dados e configure a replicação entre o Publicador, o distribuidor e o Assinante. 

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

## <a name="9---modify-agent-parameters"></a>9-modificar parâmetros do agente

A instância gerenciada do banco de dados SQL do Azure atualmente está apresentando alguns problemas de back-end com conectividade com os agentes de replicação. Enquanto esse problema está sendo resolvido, a solução alternativa é aumentar o valor de tempo limite de logon para os agentes de replicação. 

Execute o seguinte comando T-SQL no Publicador para aumentar o tempo limite do logon: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Execute o seguinte comando T-SQL novamente para definir o tempo limite de logon de volta para o valor padrão, caso você precise fazer isso:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Reinicie todos os três agentes para aplicar essas alterações. 

## <a name="10---test-replication"></a>replicação de 10 testes

Depois que a replicação tiver sido configurada, você poderá testá-la inserindo novos itens no Publicador e observando que as alterações se propagam para o Assinante. 

Execute o seguinte trecho T-SQL para exibir as linhas no Assinante:

```sql
select * from dbo.ReplTest
```

Execute o seguinte trecho T-SQL para inserir linhas adicionais no Publicador e, em seguida, verifique as linhas novamente no Assinante. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar recursos

Para descartar a publicação, execute o seguinte comando T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Para remover a opção de replicação do banco de dados, execute o seguinte comando T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Para desabilitar a publicação e a distribuição, execute o seguinte comando T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Você pode limpar os recursos do Azure [excluindo os recursos de instância gerenciada do grupo de recursos](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e, em seguida, excluindo o grupo de recursos `SQLMI-Repl`. 

   
## <a name="see-also"></a>Veja também

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [Tutorial: configurar a replicação transacional entre um Publicador de MI e um assinante de SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [O que é um Instância Gerenciada?](sql-database-managed-instance.md)
