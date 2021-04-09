---
title: Configure um grupo de failover
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como configurar um grupo de auto-failover para uma Base de Dados Azure SQL (única e agrupada) e SQL Managed Instance, utilizando o portal Azure, o Azure CLI e o PowerShell.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: 09bb7cb2344e3e708a64842916e6e483136da3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594288"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configure um grupo de failover para a Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este tópico [ensina-lhe](auto-failover-group-overview.md) como configurar um grupo de falha automática para a Base de Dados Azure SQL e Azure SQL Managed Instance.

## <a name="single-database"></a>Base de dados individual

Crie o grupo de failover e adicione-lhe uma única base de dados utilizando o portal Azure ou PowerShell.

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As definições de login e firewall do servidor secundário devem corresponder às do servidor principal.

### <a name="create-failover-group"></a>Criar grupo de failover

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o seu grupo de failover e adicione-lhe a sua única base de dados utilizando o portal Azure.

1. Selecione **Azure SQL** no menu esquerdo do [portal Azure](https://portal.azure.com). Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda.
1. Selecione a base de dados que pretende adicionar ao grupo de failover.
1. Selecione o nome do servidor no **nome do Servidor** para abrir as definições para o servidor.

   ![Abrir servidor para um único db](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Selecione **grupos failover** sob o painel **de Definições** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover.

   ![Adicione novo grupo de failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na página **do Grupo Failover,** introduza ou selecione os valores necessários e, em seguida, selecione **Criar**.

   - **Bases de dados dentro do grupo**: Escolha a base de dados que pretende adicionar ao seu grupo de failover. A adição da base de dados ao grupo de failover iniciará automaticamente o processo de geo-replicação.

   ![Adicione a Base de Dados SQL ao grupo de failover](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o seu grupo de failover e adicione-lhe a sua base de dados utilizando o PowerShell.

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
   Write-host "Creating a secondary server in the failover region..."
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

Teste de falha do seu grupo de failover utilizando o portal Azure ou PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste de falha do seu grupo de failover utilizando o portal Azure.

1. Selecione **Azure SQL** no menu esquerdo do [portal Azure](https://portal.azure.com). Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva "Azure SQL" na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda.
1. Selecione a base de dados que pretende adicionar ao grupo de failover.

   ![Abrir servidor para um único db](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Selecione **grupos failover** sob o painel **de Definições** e, em seguida, escolha o grupo de failover que acabou de criar.
  
   ![Selecione o grupo de failover a partir do portal](./media/auto-failover-group-configure/select-failover-group.png)

1. Reveja qual o servidor primário e qual o servidor secundário.
1. Selecione **Failover** do painel de tarefas para falhar sobre o seu grupo de failover que contém a sua base de dados.
1. Selecione **Sim** no aviso que o notifica de que as sessões de TDS serão desligadas.

   ![Falhe sobre o seu grupo de failover contendo a sua base de dados](./media/auto-failover-group-configure/failover-sql-db.png)

1. Reveja qual o servidor que é agora primário e qual o servidor secundário. Se o fracasso foi bem sucedido, os dois servidores deveriam ter trocado de funções.
1. Selecione **Failover** novamente para falhar os servidores de volta às suas funções originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste de falha do seu grupo de failover usando PowerShell.  

Verifique o papel da réplica secundária:

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

Falha no servidor secundário:

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

Reverter o grupo de failover de volta ao servidor primário:

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

> [!IMPORTANT]
> Se precisar de eliminar a base de dados secundária, retire-a do grupo de failover antes de a eliminar. A eliminação de uma base de dados secundária antes de ser removida do grupo de failover pode causar comportamentos imprevisíveis.

## <a name="elastic-pool"></a>Conjunto elástico

Crie o grupo failover e adicione-lhe uma piscina elástica utilizando o portal Azure, ou PowerShell.  

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As definições de login e firewall do servidor secundário devem corresponder às do servidor principal.

### <a name="create-the-failover-group"></a>Criar o grupo failover

Crie o grupo de failover para a sua piscina elástica utilizando o portal Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o seu grupo de failover e adicione-lhe a sua piscina elástica utilizando o portal Azure.

1. Selecione **Azure SQL** no menu esquerdo do [portal Azure](https://portal.azure.com). Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva "Azure SQL" na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda.
1. Selecione a piscina elástica que pretende adicionar ao grupo failover.
1. No **painel de visão** geral, selecione o nome do servidor no **nome do Servidor** para abrir as definições do servidor.
  
   ![Servidor aberto para piscina elástica](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Selecione **grupos failover** sob o painel **de Definições** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover.

   ![Adicione novo grupo de failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na página **do Grupo Failover,** introduza ou selecione os valores necessários e, em seguida, selecione **Criar**. Ou cria um novo servidor secundário ou seleciona um servidor secundário existente.

1. Selecione **bases de dados dentro do grupo** e, em seguida, escolha o pool elástico que pretende adicionar ao grupo de failover. Se uma piscina elástica já não existir no servidor secundário, aparece um aviso que o leva a criar uma piscina elástica no servidor secundário. Selecione o aviso e, em seguida, selecione **OK** para criar a piscina elástica no servidor secundário.

   ![Adicione piscina elástica ao grupo failover](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. **Selecione** para aplicar as definições de piscina elástica no grupo failover e, em seguida, selecione **Criar** para criar o seu grupo de failover. A adição da piscina elástica ao grupo failover iniciará automaticamente o processo de geo-replicação.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o seu grupo de failover e adicione-lhe a sua piscina elástica utilizando o PowerShell.

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

Teste a falha da sua piscina elástica utilizando o portal Azure ou PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Falhe o seu grupo de failover para o servidor secundário e, em seguida, falhe de novo usando o portal Azure.

1. Selecione **Azure SQL** no menu esquerdo do [portal Azure](https://portal.azure.com). Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva "Azure SQL" na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda.
1. Selecione a piscina elástica que pretende adicionar ao grupo failover.
1. No **painel de visão** geral, selecione o nome do servidor no **nome do Servidor** para abrir as definições do servidor.

   ![Servidor aberto para piscina elástica](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. Selecione **grupos failover** sob o painel **definições** e, em seguida, escolha o grupo de failover que criou na secção 2.
  
   ![Selecione o grupo de failover a partir do portal](./media/auto-failover-group-configure/select-failover-group.png)

1. Reveja qual o servidor primário e qual o servidor secundário.
1. Selecione **Failover** do painel de tarefas para falhar sobre o seu grupo de failover contendo a sua piscina elástica.
1. Selecione **Sim** no aviso que o notifica de que as sessões de TDS serão desligadas.

   ![Falhe sobre o seu grupo de failover contendo a sua base de dados](./media/auto-failover-group-configure/failover-sql-db.png)

1. Reveja qual o servidor primário, qual o servidor secundário. Se o fracasso foi bem sucedido, os dois servidores deveriam ter trocado de funções.
1. Selecione **Failover** novamente para falhar o grupo de failover de volta às definições originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste de falha do seu grupo de failover usando PowerShell.

Verifique o papel da réplica secundária:

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

Falha no servidor secundário:

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

> [!IMPORTANT]
> Se precisar de eliminar a base de dados secundária, retire-a do grupo de failover antes de a eliminar. A eliminação de uma base de dados secundária antes de ser removida do grupo de failover pode causar comportamentos imprevisíveis.

## <a name="sql-managed-instance"></a>Instância Gerida do SQL

Crie um grupo de failover entre duas instâncias geridas em Azure SQL Managed Instance utilizando o portal Azure ou PowerShell.

Você precisará configurar [expressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ou criar um portal para a rede virtual de cada SQL Managed Instance, ligar os dois gateways e, em seguida, criar o grupo failover. 

Implementar ambas as instâncias geridas em [regiões emparelhadas](../../best-practices-availability-paired-regions.md) por razões de desempenho. Os casos geridos que residem em regiões geo-emparelhadas têm um desempenho muito melhor em comparação com as regiões não remuneradas. 

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- O caso secundário gerido deve estar vazio.
- A gama de sub-redes para a rede virtual secundária não deve sobrepor-se à gama de sub-redes da rede virtual primária.
- A colagem e o azono horário do caso secundário gerido devem corresponder ao da instância gerida primária.
- Ao ligar os dois gateways, a **Chave Partilhada** deve ser a mesma para ambas as ligações.

### <a name="create-primary-virtual-network-gateway"></a>Criar porta de rede virtual primária

Se não tiver configurado [o ExpressRoute,](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)pode criar o portal de rede virtual primário com o portal Azure ou PowerShell.

> [!NOTE]
> O SKU do gateway afeta o desempenho da produção. Este artigo implementa uma porta de entrada com o SKU mais básico `HwGw1` (). Implemente um SKU mais alto (exemplo: `VpnGw3` ) para obter uma produção mais elevada. Para todas as opções disponíveis, consulte [Gateway SKUs](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o portal de rede virtual primário utilizando o portal Azure.

1. No [portal Azure,](https://portal.azure.com)vá ao seu grupo de recursos e selecione o recurso **de rede Virtual** para a sua primeira instância gerida.
1. Selecione **sub-redes** em **Definições** e, em seguida, selecione para adicionar uma nova **sub-rede Gateway**. Deixe os valores predefinidos.

   ![Adicionar porta de entrada para a primeira instância gerida](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Assim que o gateway da sub-rede for criado, **selecione Criar um recurso** a partir do painel de navegação esquerdo e, em seguida, digitar na caixa de `Virtual network gateway` pesquisa. Selecione o recurso **gateway de rede Virtual** publicado pela **Microsoft**.

   ![Criar um novo portal de rede virtual](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Preencha os campos necessários para configurar o portal da sua primeira instância gerida.

   O quadro a seguir mostra os valores necessários para a porta de entrada para a instância gerida primária:

    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A subscrição onde está a sua primeira instância gerida. |
    | **Nome** | O nome para o seu portal de rede virtual. |
    | **Região** | A região onde está o seu principal caso gerido. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo VPN** | Selecione **route-based** |
    | **SKU**| Deixar o padrão de `VpnGw1` . |
    | **Localização**| O local onde está o seu caso secundário gerido e a rede virtual secundária.   |
    | **Rede virtual**| Selecione a rede virtual para o seu caso secundário gerido. |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Insira um nome para o seu endereço IP. |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **Review + create** para rever as definições do seu gateway de rede virtual.

   ![Definições de gateway primário](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Selecione **Criar** para criar o seu novo portal de rede virtual.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway de rede virtual primário utilizando o PowerShell.

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

### <a name="create-secondary-virtual-network-gateway"></a>Criar porta de rede virtual secundária

Crie o portal de rede virtual secundário utilizando o portal Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Repita os passos na secção anterior para criar a sub-rede de rede virtual e o gateway para a instância gerida secundária. Preencha os campos necessários para configurar o portal para o seu caso secundário gerido.

O quadro a seguir mostra os valores necessários para a porta de entrada para a instância gerida secundária:

   | **Campo** | Valor |
   | --- | --- |
   | **Subscrição** |  A subscrição onde está a sua instância de gestão secundária. |
   | **Nome** | O nome para o seu gateway de rede virtual, como `secondary-mi-gateway` . |
   | **Região** | A região onde o seu caso secundário gerido é. |
   | **Tipo de gateway** | Selecione **VPN**. |
   | **Tipo VPN** | Selecione **route-based** |
   | **SKU**| Deixar o padrão de `VpnGw1` . |
   | **Localização**| O local onde está o seu caso secundário gerido e a rede virtual secundária.   |
   | **Rede virtual**| Selecione a rede virtual que foi criada na secção 2, tal como `vnet-sql-mi-secondary` . |
   | **Endereço IP público**| Selecione **Criar novo**. |
   | **Nome do endereço IP público**| Insira um nome para o seu endereço IP, como `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Definições de gateway secundária](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway de rede virtual secundário utilizando o PowerShell.

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

### <a name="connect-the-gateways"></a>Ligar os gateways

Criar ligações entre os dois gateways utilizando o portal Azure ou PowerShell.

É necessário criar duas ligações - a ligação da porta de entrada primária para o gateway secundário, e, em seguida, a ligação do gateway secundário para o gateway primário.

A chave partilhada utilizada para ambas as ligações deve ser a mesma para cada ligação.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Criar ligações entre os dois gateways utilizando o portal Azure.

1. **Selecione Criar um recurso** a partir do portal [Azure.](https://portal.azure.com)
1. Digite `connection` a caixa de pesquisa e, em seguida, prima para introduzir para pesquisar, o que o leva ao recurso **Connection,** publicado pela Microsoft.
1. Selecione **Criar** para criar a sua ligação.
1. No **separador Básicos,** selecione os seguintes valores e, em seguida, selecione **OK**.
    1. Selecione `VNet-to-VNet` para o tipo de **Ligação**.
    1. Selecione a subscrição na lista pendente.
    1. Selecione o grupo de recursos para a sua instância gerida no drop-down.
    1. Selecione a localização da sua primeira instância gerida a partir do drop-down.
1. No separador **Definições,** selecione ou introduza os seguintes valores e, em seguida, selecione **OK**:
    1. Escolha o portal de rede primário para o **primeiro gateway de rede virtual,** como `Primary-Gateway` .  
    1. Escolha o portal de rede secundária para o **segundo gateway de rede virtual,** como `Secondary-Gateway` .
    1. Selecione a caixa de verificação ao lado **de Estabelecer conectividade bidirecional**.
    1. Ou deixe o nome de ligação primária predefinido ou mude-o para um valor à sua escolha.
    1. Forneça uma **chave partilhada (PSK)** para a ligação, tais como `mi1m2psk` .

   ![Criar ligação de gateway](./media/auto-failover-group-configure/create-gateway-connection.png)

1. No **separador Resumo,** reveja as definições da sua ligação bidirecional e, em seguida, selecione **OK** para criar a sua ligação.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Criar ligações entre os dois gateways utilizando o PowerShell.

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

### <a name="create-the-failover-group"></a>Criar o grupo failover

Crie o grupo de failover para as suas instâncias geridas utilizando o portal Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o grupo de failover para as suas Ocorrências Geridas SQL utilizando o portal Azure.

1. Selecione **Azure SQL** no menu esquerdo do [portal Azure](https://portal.azure.com). Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda.
1. Selecione a instância gerida primária que pretende adicionar ao grupo de failover.  
1. Em **Definições**, navegue para **grupos de failover de exemplo** e, em seguida, escolha adicionar **grupo** para abrir a página do **Grupo Desabrosado de Exemplo.**

   ![Adicione um grupo de failover](./media/auto-failover-group-configure/add-failover-group.png)

1. Na página do **Grupo Desaparamento** de Exemplo, digite o nome do seu grupo de failover e, em seguida, escolha a instância gerida secundária a partir do drop-down. Selecione **Criar** para criar o seu grupo de failover.

   ![Criar grupo de failover](./media/auto-failover-group-configure/create-failover-group.png)

1. Uma vez concluída a implementação do grupo de failover, será levado de volta para a página do **grupo Failover.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o grupo de failover para as suas instâncias geridas utilizando o PowerShell.

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

Teste de falha do seu grupo de failover utilizando o portal Azure ou PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste de falha do seu grupo de failover utilizando o portal Azure.

1. Navegue para o seu caso _secundário_ gerido dentro do [portal Azure](https://portal.azure.com) e selecione **Grupos de Failover de Exemplo** em definições.
1. A revisão que geriu o caso é a principal, e que o caso gerido é o secundário.
1. Selecione **Failover** e, em seguida, selecione **Sim** no aviso sobre as sessões de TDS serem desligadas.

   ![Falha sobre o grupo de failover](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Rever qual é a instância manged principal e qual o caso secundário. Se o fracasso foi bem sucedido, as duas instâncias deveriam ter trocado de funções.

   ![Casos geridos mudaram de função após falha](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Vá para o novo caso _secundário_ gerido e selecione **Failover** mais uma vez para falhar a instância primária de volta ao papel principal.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste de falha do seu grupo de failover usando PowerShell.

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

## <a name="use-private-link"></a>Utilizar o Private Link

A utilização de um link privado permite-lhe associar um servidor lógico a um endereço IP privado específico dentro da rede virtual e da sub-rede. 

Para utilizar uma ligação privada com o seu grupo de failover, faça o seguinte:

1. Certifique-se de que os seus servidores primários e secundários estão numa [região emparelhada.](../../best-practices-availability-paired-regions.md) 
1. Crie a rede virtual e a sub-rede em cada região para hospedar pontos finais privados para servidores primários e secundários de modo a que tenham espaços de endereço IP não sobrepostos. Por exemplo, o intervalo de endereços de rede virtual primária de 10.0.0.0/16 e o intervalo de endereços de rede virtual secundária de 10.0.0.1/16 sobrepõem-se. Para obter mais informações sobre as gamas de endereços de rede virtual, consulte o blog [que projeta redes virtuais Azure.](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)
1. Crie um [ponto final privado e uma zona de DNS privada Azure para o servidor primário](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint). 
1. Crie também um ponto final privado para o servidor secundário, mas desta vez opte por reutilizar a mesma zona privada de DNS que foi criada para o servidor primário. 
1. Uma vez estabelecido o link privado, pode criar o grupo de failover seguindo os passos descritos anteriormente neste artigo. 


## <a name="locate-listener-endpoint"></a>Localizar ponto final do ouvinte

Uma vez configurado o seu grupo de failover, atualize o fio de ligação para a sua aplicação no ponto final do ouvinte. Isto irá manter a sua aplicação ligada ao ouvinte do grupo de failover, em vez da base de dados primária, piscina elástica ou base de dados de casos. Desta forma, não precisa de atualizar manualmente a cadeia de ligação sempre que a sua entidade de base de dados falha, e o tráfego é encaminhado para qualquer entidade que seja atualmente primária.

O ponto final do ouvinte é na forma `fog-name.database.windows.net` de, e é visível no portal Azure, ao visualizar o grupo de failover:

![Cadeia de ligação de grupo failover](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Observações

- A remoção de um grupo de failover para uma base de dados única ou agrupada não para a replicação e não elimina a base de dados replicada. Terá de parar manualmente a geo-replicação e eliminar a base de dados do servidor secundário se pretender adicionar uma base de dados única ou agrupada a um grupo de failover depois de ter sido removida. Se não o fizer, pode resultar num erro semelhante ao `The operation cannot be performed due to multiple errors` de tentar adicionar a base de dados ao grupo de failover.

## <a name="next-steps"></a>Passos seguintes

Para obter etapas detalhadas que configuram um grupo de failover, consulte os seguintes tutoriais:

- [Adicione uma única base de dados a um grupo de failover](failover-group-add-single-database-tutorial.md)
- [Adicionar um conjunto elástico a um grupo de ativação pós-falha](failover-group-add-elastic-pool-tutorial.md)
- [Adicione uma instância gerida a um grupo de failover](../managed-instance/failover-group-add-instance-tutorial.md)

Para obter uma visão geral das opções de alta disponibilidade da Base de Dados Azure SQL, consulte [grupos de geo-replicação](active-geo-replication-overview.md) e [auto-failover](auto-failover-group-overview.md).