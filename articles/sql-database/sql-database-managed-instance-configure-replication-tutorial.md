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
ms.openlocfilehash: e1ee616adadcabb2deb3fa08a5f3d4f768fddfd5
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045642"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Tutorial: configurar a replicação transacional entre duas instâncias gerenciadas e SQL Server


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Configure um Instância Gerenciada como um Publicador de replicação. 
> - Configure um Instância Gerenciada como um distribuidor de replicação. 
> - Configure um SQL Server como um assinante. 

![Replicação entre um SQL MI pub, o SQL MI dist e um SQL Server sub](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Este tutorial destina-se a um público experiente e pressupõe que o usuário esteja familiarizado com a implantação e a conexão com ambas as instâncias gerenciadas e SQL Server VMs no Azure. Como tal, determinadas etapas neste tutorial são iteradas. 

Para saber mais, confira os artigos visão geral, [recursos](sql-database-managed-instance.md)e [replicação transacional](sql-database-managed-instance-transactional-replication.md) do [banco de dados SQL do Azure](sql-database-managed-instance-index.yml).

Para configurar a replicação entre um Publicador de instância gerenciada e um assinante de instância gerenciada, consulte [Configurar a replicação transacional entre duas instâncias gerenciadas](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, verifique se você tem os seguintes pré-requisitos:

- Uma [subscrição do Azure](https://azure.microsoft.com/free/). 
- Experiência com a implantação de duas instâncias gerenciadas na mesma rede virtual. 
- Um Assinante SQL Server, seja local ou uma VM do Azure. Este tutorial usa uma VM do Azure.  
- [SQL Server Management Studio (SSMS) 18,0 ou superior](/sql/ssms/download-sql-server-management-studio-ssms).
- A versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0).
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
Crie duas instâncias gerenciadas dentro desse novo grupo de recursos usando o [portal do Azure](https://portal.azure.com). 

- O nome da instância gerenciada pelo Publicador deve ser: `sql-mi-publisher` (juntamente com alguns caracteres para randomização) e o nome da rede virtual deve ser `vnet-sql-mi-publisher`.
- O nome da instância gerenciada do distribuidor deve ser: `sql-mi-distributor` (juntamente com alguns caracteres para randomização) e deve estar _na mesma rede virtual que a instância gerenciada do Publicador_.

   ![Usar a vnet do Publicador para o distribuidor](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Para obter mais informações sobre como criar uma instância gerenciada, consulte [criar uma instância gerenciada no portal](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > Para simplificar, e por ser a configuração mais comum, este tutorial sugere colocar a instância gerenciada do distribuidor dentro da mesma rede virtual que o Publicador. No entanto, é possível criar o distribuidor em uma rede virtual separada. Para fazer isso, você precisará configurar o emparelhamento VPN entre as redes virtuais do Publicador e o distribuidor e, em seguida, configurar o emparelhamento VPN entre as redes virtuais do distribuidor e do Assinante. 

## <a name="3---create-a-sql-server-vm"></a>3-criar uma VM SQL Server
Crie uma máquina virtual SQL Server usando o [portal do Azure](https://portal.azure.com). A máquina virtual SQL Server deve ter as seguintes características:

- Nome: `sql-vm-sub`
- Imagem: SQL Server 2016 ou superior
- Grupo de recursos: o mesmo que a instância gerenciada
- Rede virtual: `sql-vm-sub-vnet` 

Para obter mais informações sobre como implantar uma VM SQL Server no Azure, consulte [início rápido: criar SQL Server VM](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

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

Quando o emparelhamento VPN for estabelecido, teste a conectividade iniciando SQL Server Management Studio (SSMS) no SQL Server e se conectando a ambas as instâncias gerenciadas. Para obter mais informações sobre como se conectar a uma instância gerenciada usando o SSMS, consulte [usar o SSMS para se conectar ao mi](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance). 

![Testar a conectividade com as instâncias gerenciadas](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5-criar uma zona DNS privada

Uma zona DNS privada permite o Roteamento DNS entre as instâncias gerenciadas e a SQL Server. 

### <a name="create-private-dns-zone"></a>Criar zona DNS privada
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **criar um recurso** para criar um novo recurso do Azure. 
1. Pesquise `private dns zone` no Azure Marketplace. 
1. Escolha o recurso de **zona de DNS privado** publicado pela Microsoft e, em seguida, selecione **criar** para criar a zona DNS. 
1. Escolha a assinatura e o grupo de recursos na lista suspensa. 
1. Forneça um nome arbitrário para a zona DNS, como `repldns.com`. 

   ![Criar zona DNS privada](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Selecione **Rever + criar**. Examine os parâmetros de sua zona DNS privada e, em seguida, selecione **criar** para criar o recurso. 

### <a name="create-a-record"></a>Criar um registro

1. Vá para a nova **zona de DNS privado** e selecione **visão geral**. 
1. Selecione **+ conjunto de registros** para criar um novo registro a. 
1. Forneça o nome da VM SQL Server, bem como o endereço IP interno privado. 

   ![Configurar um registro](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Selecione **OK** para criar o registro a. 

### <a name="link-the-virtual-network"></a>Vincular a rede virtual

1. Vá para a nova **zona de DNS privado** e selecione **links de rede virtual**. 
1. Selecione **+ Adicionar**. 
1. Forneça um nome para o link, como `Pub-link`. 
1. Selecione sua assinatura na lista suspensa e selecione a rede virtual para a instância gerenciada do Publicador. 
1. Marque a caixa ao lado de **habilitar o registro automático**. 

   ![Criar link de vnet](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Selecione **OK** para vincular sua rede virtual. 
1. Repita essas etapas para adicionar um link para a rede virtual do Assinante, com um nome como `Sub-link`. 


## <a name="6---create-azure-storage-account"></a>6-criar conta de armazenamento do Azure

[Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho e, em seguida, crie um [compartilhamento de arquivos](../storage/files/storage-how-to-create-file-share.md) dentro da conta de armazenamento. 

Copie o caminho do compartilhamento de arquivos no formato: `\\storage-account-name.file.core.windows.net\file-share-name`   

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie a cadeia de conexão da chave de acesso de armazenamento no formato: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Para obter mais informações, consulte [gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md). 


## <a name="7---create-a-database"></a>7-criar um banco de dados
Crie um novo banco de dados no Publicador MI. Para o fazer, siga estes passos:

1. Inicie o SQL Server Management Studio (SSMS) no seu SQL Server. 
1. Conecte-se à instância gerenciada `sql-mi-publisher`. 
1. Abra uma **nova** janela de consulta e execute a seguinte consulta T-SQL para criar o banco de dados:

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
Depois que a conectividade é estabelecida e você tem um banco de dados de exemplo, você pode configurar a distribuição em sua instância gerenciada `sql-mi-distributor`. Para o fazer, siga estes passos:

1. Inicie o SQL Server Management Studio (SSMS) no seu SQL Server. 
1. Conecte-se à instância gerenciada `sql-mi-distributor`. 
1. Abra uma **nova** janela de consulta e execute o seguinte código TRANSACT-SQL para configurar a distribuição na instância gerenciada do distribuidor: 

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
   > Certifique-se de usar apenas barras invertidas (`\`) para o parâmetro @working_directory. O uso de uma barra (`/`) pode causar um erro ao se conectar ao compartilhamento de arquivos. 

1. Conecte-se à instância gerenciada `sql-mi-publisher`. 
1. Abra uma **nova** janela de consulta e execute o seguinte código TRANSACT-SQL para registrar o distribuidor no Publicador: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9-criar a publicação
Depois que a distribuição tiver sido configurada, agora você poderá criar a publicação. Para o fazer, siga estes passos: 

1. Inicie o SQL Server Management Studio (SSMS) no seu SQL Server. 
1. Conecte-se à instância gerenciada `sql-mi-publisher`. 
1. No Pesquisador de **objetos**, expanda o nó **replicação** e clique com o botão direito do mouse na pasta **publicação local** . Selecione **nova publicação...** . 
1. Selecione **Avançar** para ir para a página de boas-vindas. 
1. Na página **banco de dados de publicação** , selecione o banco de dados `ReplTutorial` que você criou anteriormente. Selecione **Seguinte**. 
1. Na página **tipo de publicação** , selecione **publicação transacional**. Selecione **Seguinte**. 
1. Na página **artigos** , marque a caixa ao lado de **tabelas**. Selecione **Seguinte**. 
1. Na página **filtrar linhas da tabela** , selecione **Avançar** sem adicionar nenhum filtro. 
1. Na página **agente de instantâneo** , marque a caixa ao lado de **criar instantâneo imediatamente e mantenha o instantâneo disponível para inicializar assinaturas**. Selecione **Seguinte**. 
1. Na página **segurança do agente** , selecione **configurações de segurança.** .. Forneça SQL Server credenciais de logon a serem usadas para o Snapshot Agent e para se conectar ao Publicador. Selecione **OK** para fechar a página **agente de instantâneo segurança** . Selecione **Seguinte**. 

   ![Configurar segurança de Agente de Instantâneo](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Na página **ações do assistente** , escolha **criar a publicação** e (opcionalmente) optar por **gerar um arquivo de script com etapas para criar a publicação** se você quiser salvar esse script para mais tarde. 
1. Na página **concluir o assistente** , nomeie a publicação `ReplTest` e selecione **Avançar** para criar a publicação. 
1. Depois que a publicação tiver sido criada, atualize o nó **replicação** no **pesquisador de objetos** e expanda **publicações locais** para ver sua nova publicação. 


## <a name="10---create-the-subscription"></a>10-criar a assinatura 

Depois que a publicação tiver sido criada, você poderá criar a assinatura. Para o fazer, siga estes passos: 

1. Inicie o SQL Server Management Studio (SSMS) no seu SQL Server. 
1. Conecte-se à instância gerenciada `sql-mi-publisher`. 
1. Abra uma **nova** janela de consulta e execute o seguinte código TRANSACT-SQL para adicionar a assinatura e o agente de distribuição. Use o DNS como parte do nome do Assinante. 

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

1. Navegue até o grupo de recursos na [portal do Azure](https://portal.azure.com). 
1. Selecione as instâncias gerenciadas e, em seguida, selecione **excluir**. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. Esse processo pode levar algum tempo para ser concluído em segundo plano e até que ele seja concluído, você não poderá excluir o *cluster virtual* nem outros recursos dependentes. Monitore a exclusão na guia atividade para confirmar se a instância gerenciada foi excluída. 
1. Depois que a instância gerenciada for excluída, exclua o *cluster virtual* selecionando-o em seu grupo de recursos e, em seguida, escolhendo **excluir**. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. 
1. Exclua todos os recursos restantes. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. 
1. Exclua o grupo de recursos selecionando **excluir grupo de recursos**, digitando o nome do grupo de recursos, `myResourceGroup`e, em seguida, selecionando **excluir**. 

## <a name="known-errors"></a>Erros conhecidos

### <a name="windows-logins-are-not-supported"></a>Não há suporte para logons do Windows

`Exception Message: Windows logins are not supported in this version of SQL Server.`

O agente foi configurado com um logon do Windows e precisa usar um logon SQL Server em vez disso. Use a página **segurança do agente** das **Propriedades da publicação** para alterar as credenciais de logon para um SQL Server logon. 


### <a name="failed-to-connect-to-azure-storage"></a>Falha ao conectar ao armazenamento do Azure


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 obteve a cadeia de conexão do armazenamento do Azure para replstorage 2019-11-19 02:21:05.07 conectando-se ao armazenamento de arquivos do Azure '\\replstorage. File. Core. Windows. net\replshare ' 2019-11-19 02:21:31.21 falhou ao se conectar ao armazenamento do Azure ' ' com o erro de sistema operacional: 53.


Isso é provável porque a porta 445 está fechada no firewall do Azure, no firewall do Windows ou em ambos. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

O uso de uma barra invertida em vez de uma barra à frente no caminho do arquivo para o compartilhamento de arquivos pode causar esse erro. Isso está ok: `\\replstorage.file.core.windows.net\replshare` isso pode causar um erro do sistema operacional 55: `'\\replstorage.file.core.windows.net/replshare'`

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

Ao adicionar uma nova assinatura usando o assistente para **nova assinatura** , na página **publicação** , você pode descobrir que não há bancos de dados e publicações listados como opções disponíveis e pode ver a seguinte mensagem de erro:

`There are no publications to which yuo can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Embora seja possível que essa mensagem de erro seja precisa e realmente não haja publicações disponíveis no Publicador ao qual você se conectou ou não tenha permissões suficientes, esse erro também pode ser causado por uma versão mais antiga do SQL Server Management Studio. Tente atualizar para SQL Server Management Studio 18,0 ou superior para refazer a regra como uma causa raiz. 


## <a name="next-steps"></a>Passos seguintes

### <a name="enable-security-features"></a>Habilitar recursos de segurança

Consulte o artigo recursos de [segurança de funcionalidades de instância gerenciada](sql-database-managed-instance.md#azure-sql-database-security-features) a seguir para obter uma lista abrangente de maneiras de proteger seu banco de dados. Os seguintes recursos de segurança são discutidos:

- [Auditoria de instância gerenciada](sql-database-managed-instance-auditing.md) 
- [Sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](sql-database-managed-instance-threat-detection.md) 
- [Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança ao nível da linha](/sql/relational-databases/security/row-level-security) 
- [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funcionalidades de instância gerida

Para obter uma visão geral completa de um recurso de instância gerenciada, consulte:

> [!div class="nextstepaction"]
> [Recursos de instância gerenciada](sql-database-managed-instance.md)
