---
title: Configurar a replicação transacional entre duas instâncias gerenciadas e SQL Server
description: Um tutorial que configura a replicação entre uma instância gerenciada pelo Publicador, uma instância gerenciada do distribuidor e um Assinante SQL Server em uma VM do Azure, juntamente com os componentes de rede necessários, como a zona DNS privada e o emparelhamento VPN.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719896"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Tutorial: configurar a replicação transacional entre duas instâncias gerenciadas e SQL Server


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Configure um Instância Gerenciada como um Publicador de replicação. 
> - Configure um Instância Gerenciada como um distribuidor de replicação. 
> - Configure um SQL Server como um assinante. 

![Replicação entre um SQL MI pub, o SQL MI dist e um SQL Server sub](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Este tutorial destina-se a um público experiente e pressupõe que o usuário esteja familiarizado com a implantação e a conexão com ambas as instâncias gerenciadas e SQL Server VMs no Azure. Como tal, determinadas etapas neste tutorial são iteradas. 

Para saber mais, consulte a Base de [Dados Azure SQL gerida](sql-database-managed-instance-index.yml)na visão geral da instância , [capacidades](sql-database-managed-instance.md)e artigos de [replicação transacional SQL.](sql-database-managed-instance-transactional-replication.md)

Para configurar a replicação entre um editor de instância gerido e um assinante de instância gerido, consulte a [replicação transacional Configure entre duas instâncias geridas](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, verifique se você tem os seguintes pré-requisitos:

- Uma [subscrição Azure.](https://azure.microsoft.com/free/) 
- Experiência com a implantação de duas instâncias gerenciadas na mesma rede virtual. 
- Um Assinante SQL Server, seja local ou uma VM do Azure. Este tutorial usa uma VM do Azure.  
- [Estúdio de Gestão de Servidores SQL (SSMS) 18.0 ou superior](/sql/ssms/download-sql-server-management-studio-ssms).
- A versão mais recente do [Azure Powershell.](/powershell/azure/install-az-ps?view=azps-1.7.0)
- A porta 445 e 1433 permitem o tráfego do SQL no firewall do Azure e no firewall do Windows. 

## <a name="1---create-the-resource-group"></a>1-criar o grupo de recursos
Use o seguinte trecho de código do PowerShell para criar um novo grupo de recursos:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2-criar duas instâncias gerenciadas
Crie duas instâncias geridas dentro deste novo grupo de recursos utilizando o [portal Azure.](https://portal.azure.com) 

- O nome da instância gerida pela editora deve ser: `sql-mi-publisher` (juntamente com alguns caracteres para aleatoriedade) e o nome da rede virtual deve ser `vnet-sql-mi-publisher`.
- O nome da instância gerida pelo distribuidor deve ser: `sql-mi-distributor` (juntamente com alguns caracteres para aleatoriedade) e deve estar na mesma rede virtual que _a instância gerida pelo editor._

   ![Usar a vnet do Publicador para o distribuidor](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Para mais informações sobre a criação de um caso gerido, consulte [Criar uma instância gerida no portal](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > Para simplificar, e por ser a configuração mais comum, este tutorial sugere colocar a instância gerenciada do distribuidor dentro da mesma rede virtual que o Publicador. No entanto, é possível criar o distribuidor em uma rede virtual separada. Para fazer isso, você precisará configurar o emparelhamento VPN entre as redes virtuais do Publicador e o distribuidor e, em seguida, configurar o emparelhamento VPN entre as redes virtuais do distribuidor e do Assinante. 

## <a name="3---create-a-sql-server-vm"></a>3-criar uma VM SQL Server
Crie uma máquina virtual SQL Server utilizando o [portal Azure](https://portal.azure.com). A máquina virtual SQL Server deve ter as seguintes características:

- Nome: `sql-vm-sub`
- Imagem: SQL Server 2016 ou superior
- Grupo de recursos: o mesmo que a instância gerenciada
- Rede virtual: `sql-vm-sub-vnet` 

Para obter mais informações sobre a implementação de um VM de servidor SQL para o Azure, consulte [Quickstart: Create SQL Server VM](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4-configurar o emparelhamento VPN
Configure o emparelhamento VPN para habilitar a comunicação entre a rede virtual das duas instâncias gerenciadas e a rede virtual de SQL Server. Para fazer isso, use este trecho de código do PowerShell:

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

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Quando o emparelhamento VPN for estabelecido, teste a conectividade iniciando SQL Server Management Studio (SSMS) no SQL Server e se conectando a ambas as instâncias gerenciadas. Para obter mais informações sobre a ligação a uma instância gerida utilizando SSMS, consulte [Use SSMS para se ligar ao MI](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance). 

![Testar a conectividade com as instâncias gerenciadas](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5-criar uma zona DNS privada

Uma zona DNS privada permite o Roteamento DNS entre as instâncias gerenciadas e a SQL Server. 

### <a name="create-private-dns-zone"></a>Criar zona DNS privada
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** para criar um novo recurso Azure. 
1. Procure `private dns zone` no Azure Marketplace. 
1. Escolha o recurso **privado da zona DNS** publicado pela Microsoft e, em seguida, selecione **Criar** para criar a zona DNS. 
1. Escolha a assinatura e o grupo de recursos na lista suspensa. 
1. Forneça um nome arbitrário para a sua zona DNS, como `repldns.com`. 

   ![Criar zona DNS privada](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Selecione **Rever + criar**. Reveja os parâmetros da sua zona Privada DNS e, em seguida, selecione **Criar** para criar o seu recurso. 

### <a name="create-a-record"></a>Criar um registro

1. Vá à sua nova **zona Privada dNS** e selecione **visão geral**. 
1. Selecione **+ Conjunto de registos** para criar um novo A-Record. 
1. Forneça o nome da VM SQL Server, bem como o endereço IP interno privado. 

   ![Configurar um registro](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Selecione **OK** para criar o disco A. 

### <a name="link-the-virtual-network"></a>Vincular a rede virtual

1. Vá para a sua nova **zona Privada DNS** e selecione links de **rede Virtual**. 
1. Selecione **+ Adicionar**. 
1. Forneça um nome para o link, como `Pub-link`. 
1. Selecione sua assinatura na lista suspensa e selecione a rede virtual para a instância gerenciada do Publicador. 
1. Verifique a caixa ao lado do **registo automático enable**. 

   ![Criar link de vnet](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Selecione **OK** para ligar a sua rede virtual. 
1. Repita estes passos para adicionar um link para a rede virtual do assinante, com um nome como `Sub-link`. 


## <a name="6---create-azure-storage-account"></a>6-criar conta de armazenamento do Azure

[Crie uma Conta](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) de Armazenamento Azure para o diretório de trabalho e, em seguida, crie uma parte de [ficheiro](../storage/files/storage-how-to-create-file-share.md) dentro da conta de armazenamento. 

Copie o caminho da partilha de ficheiros no formato: `\\storage-account-name.file.core.windows.net\file-share-name`   

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie a cadeia de ligação de ligação de acesso ao armazenamento no formato: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Para mais informações, consulte Gerir as chaves de [acesso à conta](../storage/common/storage-account-keys-manage.md)de armazenamento . 


## <a name="7---create-a-database"></a>7-criar um banco de dados
Crie um novo banco de dados no Publicador MI. Para isso, siga estes passos:

1. Inicie o SQL Server Management Studio (SSMS) no seu SQL Server. 
1. Ligue-se à `sql-mi-publisher` instância gerida. 
1. Abra uma nova janela **de consulta** e execute a seguinte consulta T-SQL para criar a base de dados:

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

## <a name="8---configure-distribution"></a>8-configurar a distribuição 
Uma vez estabelecida a conectividade e tiver uma base de dados de amostras, pode configurar a distribuição na sua `sql-mi-distributor` instância gerida. Para isso, siga estes passos:

1. Inicie o SQL Server Management Studio (SSMS) no seu SQL Server. 
1. Ligue-se à `sql-mi-distributor` instância gerida. 
1. Abra uma nova janela **de consulta** e execute o seguinte código Transact-SQL para configurar a distribuição na instância gerida pelo distribuidor: 

   ```sql
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
   > Certifique-se de que utiliza apenas as pestanas (`\`) para o parâmetro @working_directory. A utilização de um corte para a frente (`/`) pode causar um erro ao ligar-se à parte do ficheiro. 

1. Ligue-se à `sql-mi-publisher` instância gerida. 
1. Abra uma nova janela **de consulta** e execute o seguinte código Transact-SQL para registar o distribuidor na editora: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9-criar a publicação
Depois que a distribuição tiver sido configurada, agora você poderá criar a publicação. Para isso, siga estes passos: 

1. Inicie o SQL Server Management Studio (SSMS) no seu SQL Server. 
1. Ligue-se à `sql-mi-publisher` instância gerida. 
1. No **Object Explorer,** expanda o nó de **replicação** e clique à direita na pasta **De publicação local.** Selecione **Nova Publicação...** . 
1. Selecione **Next** para passar pela página de boas-vindas. 
1. Na página base de dados da **Publicação,** selecione a base de dados `ReplTutorial` criada anteriormente. Selecione **Seguinte**. 
1. Na página do **tipo Publicação,** selecione **publicação transacional**. Selecione **Seguinte**. 
1. Na página de **Artigos,** verifique a caixa ao lado das **Tabelas**. Selecione **Seguinte**. 
1. Na página **Filter Table Rows,** selecione **Seguinte** sem adicionar filtros. 
1. Na página do **Snapshot Agent,** verifique imediatamente a caixa ao lado da **Create snapshot e mantenha o instantâneo disponível para inicializar as subscrições**. Selecione **Seguinte**. 
1. Na página de Segurança do **Agente,** selecione Definições de **Segurança..** . Forneça credenciais de login do SQL Server para utilizar para o agente Snapshot e para se ligar ao Editor. Selecione **OK** para fechar a página de Segurança do **Agente Instantâneo.** Selecione **Seguinte**. 

   ![Configurar segurança de Agente de Instantâneo](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Na página **Wizard Actions,** opte por **Criar a publicação** e (opcionalmente) optar por Gerar um ficheiro de script com **passos para criar a publicação** se quiser guardar este script para mais tarde. 
1. Na página **Complete the Wizard,** nomeie a sua publicação `ReplTest` e selecione **Next** para criar a sua publicação. 
1. Uma vez criada a sua publicação, refresque o nó **de Replicação** no **Object Explorer** e expanda **publicações locais** para ver a sua nova publicação. 


## <a name="10---create-the-subscription"></a>10-criar a assinatura 

Depois que a publicação tiver sido criada, você poderá criar a assinatura. Para isso, siga estes passos: 

1. Inicie o SQL Server Management Studio (SSMS) no seu SQL Server. 
1. Ligue-se à `sql-mi-publisher` instância gerida. 
1. Abra uma nova janela **de consulta** e execute o seguinte código Transact-SQL para adicionar o agente de subscrição e distribuição. Use o DNS como parte do nome do Assinante. 

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

## <a name="11---test-replication"></a>11-replicação de teste 

Depois que a replicação tiver sido configurada, você poderá testá-la inserindo novos itens no Publicador e observando que as alterações se propagam para o Assinante. 

Execute o seguinte trecho T-SQL para exibir as linhas no Assinante:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Execute o seguinte trecho T-SQL para inserir linhas adicionais no Publicador e, em seguida, verifique as linhas novamente no Assinante. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar recursos

1. Navegue para o seu grupo de recursos no [portal Azure.](https://portal.azure.com) 
1. Selecione as instâncias geridas e, em seguida, **selecione Eliminar**. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, **selecione Eliminar**. Este processo pode demorar algum tempo a ser concluído em segundo plano, e até que esteja feito, não poderá eliminar o *cluster Virtual* ou quaisquer outros recursos dependentes. Monitore a exclusão na guia atividade para confirmar se a instância gerenciada foi excluída. 
1. Uma vez eliminada a instância gerida, elimine o *cluster Virtual* selecionando-o no seu grupo de recursos e, em seguida, escolhendo **Eliminar**. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, **selecione Eliminar**. 
1. Exclua todos os recursos restantes. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, **selecione Eliminar**. 
1. Elimine o grupo de recursos selecionando o grupo de **recursos Delete**, digitando em nome do grupo de recursos, `myResourceGroup`, e, em seguida, selecionando **Delete**. 

## <a name="known-errors"></a>Erros conhecidos

### <a name="windows-logins-are-not-supported"></a>Não há suporte para logons do Windows

`Exception Message: Windows logins are not supported in this version of SQL Server.`

O agente foi configurado com um logon do Windows e precisa usar um logon SQL Server em vez disso. Utilize a página de Segurança do **Agente** das propriedades da **Publicação** para alterar as credenciais de login para um login do Servidor SQL. 


### <a name="failed-to-connect-to-azure-storage"></a>Falha ao conectar ao armazenamento do Azure


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Obteve a linha de conexão de armazenamento azure para replstorage 2019-11-19 02:21:05.07 Ligação aos Ficheiros Azure Armazenamento '\\replstorage.file.windows.windows.net\replshare' 2019-11-19 02:21:31.21 Não conseguiu ligar-se ao Armazenamento Azure '' com erro de OS: 53.


Isso é provável porque a porta 445 está fechada no firewall do Azure, no firewall do Windows ou em ambos. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

O uso de uma barra invertida em vez de uma barra à frente no caminho do arquivo para o compartilhamento de arquivos pode causar esse erro. Isto é ok: `\\replstorage.file.core.windows.net\replshare` Isto pode causar um erro de OS 55: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Não foi possível conectar ao Assinante

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Possíveis soluções:
- Verifique se a porta 1433 está aberta. 
- Verifique se o TCP/IP está habilitado no Assinante. 
- Confirme se o nome DNS foi usado ao criar o Assinante. 
- Verifique se suas redes virtuais estão corretamente vinculadas na zona DNS privada. 
- Verifique se o registro A está configurado corretamente. 
- Verifique se o emparelhamento VPN está configurado corretamente. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Não há publicações para as quais você pode assinar

Ao adicionar uma nova subscrição utilizando o novo assistente **de subscrição,** na página **De publicação,** poderá descobrir que não existem bases de dados e publicações listadas como opções disponíveis, podendo ver a seguinte mensagem de erro:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Embora seja possível que essa mensagem de erro seja precisa e realmente não haja publicações disponíveis no Publicador ao qual você se conectou ou não tenha permissões suficientes, esse erro também pode ser causado por uma versão mais antiga do SQL Server Management Studio. Tente atualizar para SQL Server Management Studio 18,0 ou superior para refazer a regra como uma causa raiz. 


## <a name="next-steps"></a>Passos Seguintes

### <a name="enable-security-features"></a>Habilitar recursos de segurança

Consulte o seguinte artigo de funcionalidades de segurança de funcionalidades de segurança de recursos de instância [geridas](sql-database-managed-instance.md#azure-sql-database-security-features) para uma lista completa de formas de proteger a sua base de dados. Os seguintes recursos de segurança são discutidos:

- [Auditoria de instânciagerida](sql-database-managed-instance-auditing.md) 
- [Sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](sql-database-managed-instance-threat-detection.md) 
- [Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança ao nível da linha](/sql/relational-databases/security/row-level-security) 
- [Encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funcionalidades de instância gerida

Para obter uma visão geral completa de um recurso de instância gerenciada, consulte:

> [!div class="nextstepaction"]
> [Capacidades de instância geridas](sql-database-managed-instance.md)
