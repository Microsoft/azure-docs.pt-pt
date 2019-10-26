---
title: Configurar um grupo de failover para o banco de dados SQL do Azure
description: Saiba como configurar um grupo de failover automático para um banco de dados único do banco de dados SQL do Azure, um pool elástico e uma instância gerenciada usando o portal do Azure, a CLI do AZ e o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 626677ef9444e0ad99fd3678e23bdeec62fd920c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933397"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configurar um grupo de failover para o banco de dados SQL do Azure

Este tópico ensina como configurar um grupo de [failover automático](sql-database-auto-failover-group.md) para um banco de dados individual do banco de dados SQL do Azure, um pool elástico e uma instância gerenciada usando o portal do Azure ou o PowerShell. 

## <a name="single-database"></a>Base de dados única
Crie o grupo de failover e adicione um único banco de dados a ele usando o portal do Azure ou o PowerShell.

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As configurações de logon e firewall do servidor para o servidor secundário devem corresponder à do servidor primário. 

### <a name="create-failover-group"></a>Criar grupo de failover

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Crie seu grupo de failover e adicione seu banco de dados a ele usando o portal do Azure.

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione o banco de dados individual que você deseja adicionar ao grupo de failover. 
1. Selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.

   ![Abrir servidor para banco de BD único](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecione **grupos de failover** no painel **configurações** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na página **grupo de failover** , insira ou selecione os valores necessários e, em seguida, selecione **criar**.

   - **Bancos de dados dentro do grupo**: escolha o banco que deseja adicionar ao grupo de failover. Adicionar o banco de dados ao grupo de failover iniciará automaticamente o processo de replicação geográfica. 
        
    ![Adicionar Banco de BD SQL ao grupo de failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Crie seu grupo de failover e adicione seu banco de dados individual a ele usando o PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Ativação pós-falha de teste 

Teste o failover de seu grupo de failover usando o portal do Azure ou o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Teste o failover de seu grupo de failover usando o portal do Azure. 

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione o banco de dados individual que você deseja adicionar ao grupo de failover. 

   ![Abrir servidor para banco de BD único](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecione **grupos de failover** no painel **configurações** e escolha o grupo de failover que você acabou de criar. 
  
   ![Selecione o grupo de failover no portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Examine qual servidor é primário e qual servidor é secundário. 
1. Selecione **failover** no painel de tarefas para fazer failover do grupo de failover que contém o banco de dados individual. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Fazer failover do grupo de failover que contém o banco de dados SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Examine qual servidor agora é primário e qual servidor é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter funções trocadas. 
1. Selecione **failover** novamente para reprovar os servidores de volta para suas funções originalmente. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover de seu grupo de failover usando o PowerShell.  

Verifique a função da réplica secundária: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Fazer failover para o servidor secundário: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Reverter grupo de failover de volta para o servidor primário:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

## <a name="elastic-pool"></a>Conjunto elástico
Crie o grupo de failover e adicione um pool elástico a ele usando o portal do Azure ou o PowerShell.  

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As configurações de logon e firewall do servidor para o servidor secundário devem corresponder à do servidor primário. 

### <a name="create-the-failover-group"></a>Criar o grupo de failover 

Crie o grupo de failover para seu pool elástico usando o portal do Azure ou o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Crie seu grupo de failover e adicione seu pool elástico a ele usando o portal do Azure.

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione o pool elástico que você deseja adicionar ao grupo de failover. 
1. No painel **visão geral** , selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.
  
    ![Abrir servidor para pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos de failover** no painel **configurações** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na página **grupo de failover** , insira ou selecione os valores necessários e, em seguida, selecione **criar**. Crie um novo servidor secundário ou selecione um servidor secundário existente. 

1. Selecione **bancos de dados dentro do grupo** e escolha o pool elástico que você deseja adicionar ao grupo de failover. Se um pool elástico ainda não existir no servidor secundário, será exibido um aviso solicitando que você crie um pool elástico no servidor secundário. Selecione o aviso e, em seguida, selecione **OK** para criar o pool elástico no servidor secundário. 
        
    ![Adicionar pool elástico ao grupo de failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selecione **selecionar** para aplicar as configurações de pool elástico ao grupo de failover e, em seguida, selecione **criar** para criar seu grupo de failover. Adicionar o pool elástico ao grupo de failover iniciará automaticamente o processo de replicação geográfica. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie seu grupo de failover e adicione seu pool elástico a ele usando o PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Ativação pós-falha de teste

Teste o failover de seu pool elástico usando o portal do Azure ou o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Falhe o grupo de failover para o servidor secundário e, em seguida, faça o failback usando o portal do Azure. 

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione o pool elástico que você deseja adicionar ao grupo de failover. 
1. No painel **visão geral** , selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.
  
    ![Abrir servidor para pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Selecione **grupos de failover** no painel **configurações** e escolha o grupo de failover que você criou na seção 2. 
  
   ![Selecione o grupo de failover no portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Examine qual servidor é primário e qual servidor é secundário. 
1. Selecione **failover** no painel de tarefas para fazer failover do grupo de failover que contém o pool elástico. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Fazer failover do grupo de failover que contém o banco de dados SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Examine qual servidor é primário, qual servidor é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter funções trocadas. 
1. Selecione **failover** novamente para falhar o grupo de failover de volta para as configurações originais. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover de seu grupo de failover usando o PowerShell.

Verifique a função da réplica secundária: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Fazer failover para o servidor secundário: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

## <a name="managed-instance"></a>Instância gerida

Crie um grupo de failover entre duas instâncias gerenciadas usando o portal do Azure ou o PowerShell. 

Você precisará criar um gateway para a rede virtual de cada instância gerenciada, conectar os dois gateways e, em seguida, criar o grupo de failover.

### <a name="prerequisites"></a>Pré-requisitos
Considere os seguintes pré-requisitos:

- A instância gerenciada secundária deve estar vazia.
- O intervalo de sub-rede para a rede virtual secundária não deve sobrepor o intervalo de sub-rede da rede virtual primária. 
- O agrupamento e o fuso horário da instância secundária devem corresponder ao da instância primária. 
- Ao conectar os dois gateways, a **chave compartilhada** deve ser a mesma para ambas as conexões. 

### <a name="create-primary-virtual-network-gateway"></a>Criar gateway de rede virtual primária 

Crie o gateway de rede virtual primária com o portal do Azure ou o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Crie o gateway de rede virtual primária usando o portal do Azure. 

1. Na [portal do Azure](https://portal.azure.com), vá para o grupo de recursos e selecione o recurso de **rede virtual** para sua instância gerenciada primária. 
1. Selecione sub- **redes** em **configurações** e, em seguida, selecione para adicionar uma nova **sub-rede de gateway**. Deixe os valores padrão. 

   ![Adicionar gateway para instância gerenciada primária](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Depois que o gateway de sub-rede for criado, selecione **criar um recurso** no painel de navegação esquerdo e digite `Virtual network gateway` na caixa de pesquisa. Selecione o recurso de **Gateway de rede virtual** publicado pela **Microsoft**. 

   ![Criar um novo gateway de rede virtual](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Preencha os campos obrigatórios para configurar o gateway de sua instância gerenciada primária. 

   A tabela a seguir mostra os valores necessários para o gateway para a instância gerenciada primária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A assinatura na qual a instância gerenciada primária é. |
    | **Nome** | O nome do seu gateway de rede virtual. | 
    | **Região** | A região onde sua instância gerenciada secundária é. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo de VPN** | Selecionar **baseado em rota** |
    | **SKU**| Mantenha o padrão de `VpnGw1`. |
    | **Localização**| O local onde a instância gerenciada secundária e a rede virtual secundária são.   |
    | **Rede virtual**| Selecione a rede virtual para sua instância gerenciada secundária. |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Insira um nome para seu endereço IP. |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **revisar + criar** para examinar as configurações do seu gateway de rede virtual.

   ![Configurações do gateway primário](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecione **criar** para criar seu novo gateway de rede virtual. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway de rede virtual primário usando o PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Criar um gateway de rede virtual secundário

Crie o gateway de rede virtual secundário usando o portal do Azure ou o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Repita as etapas na seção anterior para criar a sub-rede de rede virtual e o gateway para a instância gerenciada secundária. Preencha os campos obrigatórios para configurar o gateway para sua instância gerenciada secundária. 

   A tabela a seguir mostra os valores necessários para o gateway para a instância gerenciada secundária:

   | **Campo** | Valor |
   | --- | --- |
   | **Subscrição** |  A assinatura na qual a instância gerenciada secundária é. |
   | **Nome** | O nome do seu gateway de rede virtual, como `secondary-mi-gateway`. | 
   | **Região** | A região onde sua instância gerenciada secundária é. |
   | **Tipo de gateway** | Selecione **VPN**. |
   | **Tipo de VPN** | Selecionar **baseado em rota** |
   | **SKU**| Mantenha o padrão de `VpnGw1`. |
   | **Localização**| O local onde a instância gerenciada secundária e a rede virtual secundária são.   |
   | **Rede virtual**| Selecione a rede virtual que foi criada na seção 2, como `vnet-sql-mi-secondary`. |
   | **Endereço IP público**| Selecione **Criar novo**. |
   | **Nome do endereço IP público**| Insira um nome para seu endereço IP, como `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Configurações do gateway secundário](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway de rede virtual secundário usando o PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Conectar os gateways 
Crie conexões entre os dois gateways usando o portal do Azure ou o PowerShell. 

Duas conexões precisam ser criadas-a conexão do gateway primário para o gateway secundário e, em seguida, a conexão do gateway secundário com o gateway primário. 

A chave compartilhada usada para ambas as conexões deve ser a mesma para cada conexão. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Crie conexões entre os dois gateways usando o portal do Azure. 

1. Selecione **criar um recurso** no [portal do Azure](https://portal.azure.com).
1. Digite `connection` na caixa de pesquisa e pressione ENTER para pesquisar, que leva você para o recurso de **conexão** , publicado pela Microsoft.
1. Selecione **criar** para criar sua conexão. 
1. Na guia **noções básicas** , selecione os valores a seguir e, em seguida, selecione **OK**. 
    1. Selecione `VNet-to-VNet` para o **tipo de conexão**. 
    1. Selecione a subscrição na lista pendente. 
    1. Selecione o grupo de recursos para sua instância gerenciada na lista suspensa. 
    1. Selecione o local da instância gerenciada primária na lista suspensa 
1. Na guia **configurações** , selecione ou insira os seguintes valores e, em seguida, selecione **OK**:
    1. Escolha o gateway de rede primário para o **primeiro gateway de rede virtual**, como `Primary-Gateway`.  
    1. Escolha o gateway de rede secundário para o **segundo gateway de rede virtual**, como `Secondary-Gateway`. 
    1. Marque a caixa de seleção ao lado de **estabelecer conectividade bidirecional**. 
    1. Deixe o nome da conexão primária padrão ou renomeie-o para um valor de sua escolha. 
    1. Forneça uma **chave compartilhada (PSK)** para a conexão, como `mi1m2psk`. 

   ![Criar conexão de gateway](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Na guia **Resumo** , examine as configurações da conexão bidirecional e, em seguida, selecione **OK** para criar a conexão. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie conexões entre os dois gateways usando o PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Criar o grupo de failover 
Crie o grupo de failover para suas instâncias gerenciadas usando o portal do Azure ou o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Crie o grupo de failover para suas instâncias gerenciadas usando portal do Azure. 

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione a instância gerenciada primária que você deseja adicionar ao grupo de failover.  
1. Em **configurações**, navegue até **instância grupos de failover** e, em seguida, escolha **Adicionar grupo** para abrir a página **grupo de failover de instância** . 

   ![Adicionar um grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na página **grupo de failover de instância** , digite o nome do grupo de failover e escolha a instância gerenciada secundária na lista suspensa. Selecione **criar** para criar seu grupo de failover. 

   ![Criar grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Depois que a implantação do grupo de failover for concluída, você será levado de volta à página **grupo de failover** . 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o grupo de failover para suas instâncias gerenciadas usando o PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Ativação pós-falha de teste

Teste o failover de seu grupo de failover usando o portal do Azure ou o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Teste o failover de seu grupo de failover usando o portal do Azure. 

1. Navegue até sua instância gerenciada dentro do [portal do Azure](https://portal.azure.com) e selecione **grupos de failover de instância** em configurações. 
1. Examine qual instância gerenciada é a primária e qual instância gerenciada é a secundária. 
1. Selecione **failover** e, em seguida, selecione **Sim** no aviso sobre as sessões de TDS sendo desconectadas. 

   ![Fazer failover do grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Examine qual instância gerenciada é a primária e qual é a instância secundária. Se o failover tiver sido bem-sucedido, as duas instâncias deverão ter funções alternadas. 

   ![Instâncias gerenciadas têm funções alternadas após o failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Selecione **failover** novamente para falhar a instância primária de volta para a função primária. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover de seu grupo de failover usando o PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Localizar ponto de extremidade do ouvinte

Quando o grupo de failover estiver configurado, atualize a cadeia de conexão do seu aplicativo para o ponto de extremidade do ouvinte. Isso manterá seu aplicativo conectado ao ouvinte do grupo de failover, em vez do banco de dados primário, do pool elástico ou da instância gerenciada. Dessa forma, você não precisa atualizar manualmente a cadeia de conexão toda vez que a entidade do banco de dados SQL do Azure faz failover e o tráfego é roteado para qualquer entidade que esteja primária no momento. 

O ponto de extremidade do ouvinte está na forma de `fog-name.database.windows.net`e fica visível na portal do Azure, ao exibir o grupo de failover:

![Cadeia de conexão do grupo de failover](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Observações

- A remoção de um grupo de failover para um banco de dados único ou em pool não interrompe a replicação e não exclui o banco de dados replicado. Você precisará parar manualmente a replicação geográfica e excluir o banco de dados do servidor secundário se desejar adicionar um banco de dados individual ou em pool de volta a um grupo de failover depois que ele tiver sido removido. A falha de qualquer coisa pode resultar em um erro semelhante a `The operation cannot be performed due to multiple errors` ao tentar adicionar o banco de dados ao grupo de failover. 


## <a name="next-steps"></a>Passos seguintes

Para obter etapas detalhadas sobre como configurar um grupo de failover, consulte os seguintes tutoriais:
- [Adicionar um banco de dados individual a um grupo de failover](sql-database-single-database-failover-group-tutorial.md)
- [Adicionar um pool elástico a um grupo de failover](sql-database-elastic-pool-failover-group-tutorial.md)
- [Adicionar instâncias gerenciadas a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md)
 
Para obter uma visão geral das opções de alta disponibilidade do banco de dados SQL do Azure, consulte [replicação geográfica](sql-database-active-geo-replication.md) e [grupos de failover automático](sql-database-auto-failover-group.md). 
