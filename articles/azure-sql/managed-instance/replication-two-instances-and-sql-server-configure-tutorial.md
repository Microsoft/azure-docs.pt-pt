---
title: Configure a replicação transacional entre Azure SQL Managed Instance e SQL Server
description: Um tutorial que configura a replicação entre uma instância gerida por um editor, um distribuidor gerido instância, e um subscritor do SQL Server num VM Azure, juntamente com os necessários componentes de rede, como a zona privada de DNS e o esquisite VNet.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/21/2019
ms.openlocfilehash: d2b45f5b51f4656294632aa46f679a7a09c06ed3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94593931"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Tutorial: Configurar a replicação transacional entre a Azure SQL Managed Instance e o SQL Server
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional permite replicar dados de uma base de dados para outra hospedada no SQL Server ou [no Azure SQL Managed Instance](sql-managed-instance-paas-overview.md). SQL Managed Instance pode ser um editor, distribuidor ou assinante na topologia de replicação. Consulte [as configurações de replicação transacional](replication-transactional-overview.md#common-configurations) para as configurações disponíveis. 

A replicação transacional está atualmente em pré-visualização pública para SQL Managed Instance. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Configurar um caso gerido como editor de replicação.
> - Configure um caso gerido como distribuidor de replicação.
> - Configure o SQL Server como assinante.

![Replicação entre um editor de instância gerido, distribuidor de instância gerido e subscritor do SQL Server](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Este tutorial destina-se a um público experiente e assume que o utilizador está familiarizado com a implementação e ligação a instâncias geridas e VMs do SQL Server dentro do Azure. 


> [!NOTE]
> Este artigo descreve o uso de [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) em Azure SQL Managed Instance. Não está relacionado com [grupos de failover](../database/auto-failover-group-overview.md), uma funcionalidade de Instância Gerida Azure SQL que permite criar réplicas legíveis completas de instâncias individuais. Existem considerações adicionais ao configurar a [replicação transacional com grupos de failover](replication-transactional-overview.md#with-failover-groups).

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Experiência com a implementação de duas instâncias geridas dentro da mesma rede virtual.
- Um assinante do SQL Server, no local ou num VM Azure. Este tutorial usa um Azure VM.  
- [SQL Server Management Studio (SSMS) 18.0 ou superior](/sql/ssms/download-sql-server-management-studio-ssms).
- A versão mais recente do [Azure PowerShell.](/powershell/azure/install-az-ps)
- As portas 445 e 1433 permitem o tráfego SQL tanto na firewall Azure como na firewall do Windows.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Utilize o seguinte corte de código PowerShell para criar um novo grupo de recursos:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="create-two-managed-instances"></a>Criar duas instâncias geridas

Crie duas instâncias geridas dentro deste novo grupo de recursos utilizando o [portal Azure](https://portal.azure.com).

- O nome da editora gerida deve ser `sql-mi-publisher` (juntamente com alguns caracteres para aleatoriedade), e o nome da rede virtual deve ser `vnet-sql-mi-publisher` .
- O nome da instância gerida pelo distribuidor deve ser `sql-mi-distributor` (juntamente com alguns caracteres para aleatoriedade), e deve estar na mesma rede virtual que _a editora geriu exemplo_.

   ![Utilize o VNet editor para o distribuidor](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

Para obter mais informações sobre a criação de um caso gerido, consulte [Criar uma instância gerida no portal](instance-create-quickstart.md).

  > [!NOTE]
  > Por uma questão de simplicidade, e por ser a configuração mais comum, este tutorial sugere colocar o distribuidor gerido na mesma rede virtual que a editora. No entanto, é possível criar o distribuidor numa rede virtual separada. Para tal, terá de configurar o VNet entre as redes virtuais do editor e distribuidor e, em seguida, configurar o VNet entre as redes virtuais do distribuidor e do assinante.

## <a name="create-a-sql-server-vm"></a>Criar um SQL Server VM

Crie uma máquina virtual SQL Server utilizando o [portal Azure](https://portal.azure.com). A máquina virtual SQL Server deve ter as seguintes características:

- Nome: `sql-vm-sub`
- Imagem: SQL Server 2016 ou maior
- Grupo de recursos: o mesmo que a instância gerida
- Rede virtual: `sql-vm-sub-vnet`

Para obter mais informações sobre a implementação de um SQL Server VM para Azure, consulte [Quickstart: Create a SQL Server VM](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="configure-vnet-peering"></a>Configure o olho da VNet

Configure o vNet espreitando para permitir a comunicação entre a rede virtual das duas instâncias geridas, e a rede virtual do SQL Server. Para tal, utilize este corte de código PowerShell:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VNet peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VNet peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Uma vez estabelecido o peering VNet, teste a conectividade, lançando o SQL Server Management Studio (SSMS) no SQL Server e conectando-se a ambas as instâncias geridas. Para obter mais informações sobre a ligação a um caso gerido utilizando SSMS, consulte [utilizar SSMS para ligar ao SQL Managed Instance](point-to-site-p2s-configure.md#connect-with-ssms).

![Testar conectividade com as instâncias geridas](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="create-a-private-dns-zone"></a>Criar uma zona privada de DNS

Uma zona privada de DNS permite o encaminhamento de DNS entre as instâncias geridas e o SQL Server.

### <a name="create-a-private-dns-zone"></a>Criar uma zona privada de DNS

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. **Selecione Criar um recurso** para criar um novo recurso Azure.
1. Pesquisa `private dns zone` no Azure Marketplace.
1. Escolha o recurso **de zona DNS privado** publicado pela Microsoft e, em seguida, selecione **Criar** para criar a zona DNS.
1. Escolha o grupo de subscrição e recursos a partir da entrega.
1. Forneça um nome arbitrário para a sua zona DE DNS, como `repldns.com` .

   ![Criar zona privada de DNS](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Selecione **Rever + criar**. Reveja os parâmetros para a sua zona privada de DNS e, em seguida, **selecione Criar** para criar o seu recurso.

### <a name="create-an-a-record"></a>Criar um disco A

1. Vá à sua nova **zona privada de DNS** e selecione **Overview**.
1. Selecione **+ Conjunto de registo** para criar um novo disco A.
1. Forneça o nome do seu SQL Server VM, bem como o endereço IP interno privado.

   ![Configure um recorde A](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Selecione **OK** para criar o disco A.

### <a name="link-the-virtual-network"></a>Ligue a rede virtual

1. Vá para a sua nova **zona privada de DNS** e selecione **links de rede Virtual.**
1. Selecione **+ Adicionar**.
1. Fornecer um nome para o link, como `Pub-link` .
1. Selecione a sua subscrição a partir do drop-down e, em seguida, selecione a rede virtual para a sua instância gerida pelo seu editor.
1. Verifique a caixa ao lado **do Ative o registo automático**.

   ![Criar ligação VNet](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. Selecione **OK** para ligar a sua rede virtual.
1. Repita estes passos para adicionar um link para a rede virtual do assinante, com um nome como `Sub-link` .

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

[Crie uma conta de armazenamento Azure](../../storage/common/storage-account-create.md#create-a-storage-account) para o diretório de trabalho e, em seguida, crie uma [parte de arquivo](../../storage/files/storage-how-to-create-file-share.md) dentro da conta de armazenamento.

Copie o caminho da partilha de ficheiros no formato de: `\\storage-account-name.file.core.windows.net\file-share-name`

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie a cadeia de ligação da chave de acesso ao armazenamento no formato de: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../../storage/common/storage-account-keys-manage.md)

## <a name="create-a-database"></a>Criar uma base de dados

Crie uma nova base de dados sobre o exemplo gerido pelo editor. Para tal, siga estes passos:

1. Lance o SQL Server Management Studio no SQL Server.
1. Ligue-se ao `sql-mi-publisher` caso gerido.
1. Abra uma nova janela **de consulta** e execute a seguinte consulta T-SQL para criar a base de dados.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
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

## <a name="configure-distribution"></a>Distribuição de configuração

Uma vez estabelecida a conectividade e tiver uma base de dados de amostras, pode configurar a distribuição no seu `sql-mi-distributor` caso gerido. Para tal, siga estes passos:

1. Lance o SQL Server Management Studio no SQL Server.
1. Ligue-se ao `sql-mi-distributor` caso gerido.
1. Abra uma nova janela **de consulta** e execute o seguinte código Transact-SQL para configurar a distribuição na instância gerida pelo distribuidor:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Certifique-se de que utiliza apenas as pestanas `\` para o @working_directory parâmetro. A utilização de um corte dianteiro `/` () pode causar um erro ao ligar-se à partilha de ficheiros.

1. Ligue-se ao `sql-mi-publisher` caso gerido.
1. Abra uma janela **New Query** e execute o seguinte código Transact-SQL para registar o distribuidor no editor:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="create-the-publication"></a>Criar a publicação

Uma vez configurada a distribuição, pode agora criar a publicação. Para tal, siga estes passos:

1. Lance o SQL Server Management Studio no SQL Server.
1. Ligue-se ao `sql-mi-publisher` caso gerido.
1. No **Object Explorer,** expanda o nó **de replicação** e clique à direita na pasta **da Publicação Local.** Selecione **Nova Publicação...**.
1. Selecione **Seguinte** para passar pela página de boas-vindas.
1. Na página **Base de Dados de Publicações,** selecione a `ReplTutorial` base de dados que criou anteriormente. Selecione **Seguinte**.
1. Na página do **tipo Publicação,** selecione **Publicação Transactional**. Selecione **Seguinte**.
1. Na página **de Artigos,** verifique a caixa ao lado **das Tabelas.** Selecione **Seguinte**.
1. Na página **'Linhas de tabela' filtrar,** selecione **Seguinte** sem adicionar filtros.
1. Na página Snapshot **Agent,** verifique imediatamente a caixa ao lado **do Create snapshot e mantenha o instantâneo disponível para inicializar as subscrições**. Selecione **Seguinte**.
1. Na página de Segurança do **Agente,** selecione **Definições de Segurança...**. Forneça credenciais de login do SQL Server para utilizar para o Agente Snapshot e para ligar ao editor. Selecione **OK** para fechar a página **de Segurança do Agente Instantâneo.** Selecione **Seguinte**.

   ![Configure a segurança do agente snapshot](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. Na página Ações do **Assistente,** opte por **Criar a publicação** e (opcionalmente) escolha gerar um ficheiro de script com **passos para criar a publicação** se quiser guardar este script para mais tarde.
1. Na página **'Completar o Assistente',** nomeie a sua publicação `ReplTest` e selecione **Seguinte** para criar a sua publicação.
1. Uma vez criada a sua publicação, refresque o nó **de replicação** no **Object Explorer** e expanda **as Publicações Locais** para ver a sua nova publicação.

## <a name="create-the-subscription"></a>Criar a subscrição

Uma vez criada a publicação, pode criar a subscrição. Para tal, siga estes passos:

1. Lance o SQL Server Management Studio no SQL Server.
1. Ligue-se ao `sql-mi-publisher` caso gerido.
1. Abra uma janela **Nova Consulta** e execute o seguinte código Transact-SQL para adicionar o agente de subscrição e distribuição. Utilize o DNS como parte do nome do assinante.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="test-replication"></a>Replicação do teste

Uma vez configurada a replicação, pode testá-la inserindo novos itens na editora e assistindo às alterações que se propagam ao assinante.

Executar o seguinte corte T-SQL para ver as linhas no assinante:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Execute o seguinte corte T-SQL para inserir linhas adicionais na editora e, em seguida, verifique novamente as linhas no assinante.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar os recursos

1. Navegue para o seu grupo de recursos no [portal Azure](https://portal.azure.com).
1. Selecione as instâncias geridas e, em seguida, **selecione Delete**. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. Este processo pode demorar algum tempo a ser concluído em segundo plano, e até que esteja feito, não será capaz de eliminar o *cluster virtual* ou quaisquer outros recursos dependentes. Monitorize a eliminação no separador **'Atividade'** para confirmar que a sua instância gerida foi eliminada.
1. Uma vez eliminada a instância gerida, elimine o *cluster virtual* selecionando-o no seu grupo de recursos e, em seguida, escolhendo **Delete**. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**.
1. Elimine os recursos restantes. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**.
1. Eliminar o grupo de recursos selecionando eliminar o **grupo de recursos,** digitando em nome do grupo de `myResourceGroup` recursos, e selecionando **delete**.

## <a name="known-errors"></a>Erros conhecidos

### <a name="windows-logins-are-not-supported"></a>Os logins do Windows não são suportados

`Exception Message: Windows logins are not supported in this version of SQL Server.`

O agente foi configurado com um login do Windows e precisa de utilizar um login do SQL Server. Utilize a página de Segurança do **Agente** das propriedades da **Publicação** para alterar as credenciais de login para um login do SQL Server.

### <a name="failed-to-connect-to-azure-storage"></a>Falha ao ligar ao Armazenamento do Microsoft Azure

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Cadeia de Conexão de Armazenamento Azure para replstorage 2019-11-19 02:21:05.05.007 Ligação ao Azure Files Storage ' \\ replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Falhou a ligação ao Azure Storage '' com erro de SISTEMA: 53.

Isto é provável porque a porta 445 está fechada na firewall Azure, na firewall do Windows, ou em ambos.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

A utilização de um corte para a frente em vez de retrocesso no caminho do ficheiro para a partilha de ficheiros pode causar este erro.
  
  - Isto está bom: `\\replstorage.file.core.windows.net\replshare`
  - Isto pode causar um erro de SO 55: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Não consegui ligar ao Assinante

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Soluções possíveis:

- Certifique-se de que a porta 1433 está aberta.
- Certifique-se de que o TCP/IP está ativado no assinante.
- Confirme-se que o nome DNS foi utilizado na criação do assinante.
- Verifique se as suas redes virtuais estão corretamente ligadas na zona privada do DNS.
- Verifique se o seu registo A está configurado corretamente.
- Verifique se o seu espremiado VNet está configurado corretamente.

### <a name="no-publications-to-which-you-can-subscribe"></a>Nenhuma publicação a que possa subscrever

Quando estiver a adicionar uma nova subscrição utilizando o assistente de **Nova Subscrição,** na página **'Publicação',** poderá descobrir que não existem bases de dados e publicações listadas como opções disponíveis, e poderá ver a seguinte mensagem de erro:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

Embora seja possível que esta mensagem de erro seja precisa, e realmente não existam publicações disponíveis na editora a que ligou, ou que não tenha permissões suficientes, este erro também pode ser causado por uma versão mais antiga do SQL Server Management Studio. Tente atualizar para o SQL Server Management Studio 18.0 ou superior para excluir isto como uma causa raiz.

## <a name="next-steps"></a>Passos seguintes

### <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Consulte o artigo [O que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) São discutidas as seguintes funcionalidades de segurança:

- [Auditoria de casos geridos sql](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](threat-detection-configure.md)
- [Mascaramento dinâmico de dados](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança ao nível da linha](/sql/relational-databases/security/row-level-security)
- [Encriptação transparente de dados (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Capacidades de instância gerida sql

Para uma visão geral completa das capacidades de exemplo geridas, consulte:

> [!div class="nextstepaction"]
> [Capacidades de instância gerida sql](sql-managed-instance-paas-overview.md)