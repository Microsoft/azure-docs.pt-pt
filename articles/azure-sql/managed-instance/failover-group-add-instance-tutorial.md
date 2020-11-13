---
title: 'Tutorial: Adicione a SQL Gestd Instance a um grupo de failover'
titleSuffix: Azure SQL Managed Instance
description: Neste tutorial, aprenda a criar um grupo de failover entre uma instância gerida primária e secundária do Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: sashan
ms.date: 08/27/2019
ms.openlocfilehash: 92d1ce51306e846e2d842bef33bb9782da14019a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593999"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Tutorial: Adicione a SQL Gestd Instance a um grupo de failover
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Adicione instâncias geridas de Azure SQL Managed Instance a um grupo de failover. Neste artigo, aprenderá a:

> [!div class="checklist"]
> - Criar um exemplo gerido primário.
> - Criar um caso secundário gerido como parte de um [grupo de failover](../database/auto-failover-group-overview.md). 
> - Teste falhado.

  > [!NOTE]
  > - Ao passar por este tutorial, certifique-se de que está a configurar os seus recursos com os [pré-requisitos para a criação de grupos de failover para a SQL Managed Instance](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Criar um caso gerido pode demorar um tempo significativo. Como resultado, este tutorial pode levar várias horas para ser concluído. Para obter mais informações sobre os tempos de provisionamento, consulte [as operações de gestão de instância gerida sql](sql-managed-instance-paas-overview.md#management-operations). 
  > - Casos geridos que participam num grupo de failover requerem ou [a Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ou dois gateways VPN conectados. O VNet Peering Global não é suportado. Este tutorial fornece passos para criar e ligar os gateways VPN. Ignore estes passos se já tiver o ExpressRoute configurado. 


## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para concluir este tutorial, confirme que tem: 

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para completar o tutorial, certifique-se de que tem os seguintes itens:

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>Criar um grupo de recursos e exemplo gerido primário

Neste passo, irá criar o grupo de recursos e a primeira instância gerida para o seu grupo de failover utilizando o portal Azure ou PowerShell. 

Implementar ambas as instâncias geridas em [regiões emparelhadas](../../best-practices-availability-paired-regions.md) por razões de desempenho. Os casos geridos que residem em regiões geo-emparelhadas têm um desempenho muito melhor em comparação com as regiões não remuneradas. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Crie o grupo de recursos e a sua primeira instância gerida utilizando o portal Azure. 

1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite `Azure SQL` na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda. 
1. **Selecione + Adicione** para abrir a página de **opção de implementação Select SQL.** Pode ver informações adicionais sobre as diferentes bases de dados selecionando detalhes do **Show** no azulejo **de Bases de Dados.**
1. Selecione **Criar** no azulejo **SQL Managed Instances.** 

    ![Selecione sql gestdione instance](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Na página **"Configurar exemplos geridos do Azure SQL",** no separador **Básicos:**
    1. Em **Detalhes do Projeto,** selecione a sua **Subscrição** a partir do drop-down e, em seguida, escolha **criar novo** grupo de recursos. Digite um nome para o seu grupo de recursos, como `myResourceGroup` . 
    1. Em detalhes de **instância gerida SQL,** forneça o nome da sua instância gerida, e a região onde gostaria de implementar o seu caso gerido. Deixe **o Compute + armazenamento** a valores predefinidos. 
    1. Em **Conta de Administrador** , forneça um login de administração, `azureuser` como, por exemplo, e uma senha de administração complexa. 

    ![Criar exemplos geridos primários](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Deixe as restantes definições em valores predefinidos e selecione **Review + criar** para rever as definições de SqL Managed Instance. 
1. Selecione **Criar** para criar o seu caso gerido principal. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o seu grupo de recursos e o caso gerido primário utilizando o PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede numa rede virtual. | 
| [Grupo deSegurança New-AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma mesa de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Arranja um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota para uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria um caso gerido.  |

---

## <a name="create-secondary-virtual-network"></a>Criar rede virtual secundária

Se estiver a utilizar o portal Azure para criar a sua instância gerida, terá de criar a rede virtual separadamente porque existe um requisito de que a sub-rede da instância gerida primária e secundária não tenha intervalos de sobreposição. Se estiver a usar o PowerShell para configurar a sua instância gerida, avance para o passo 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Para verificar a gama de sub-redes da sua rede virtual primária, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)navegue para o seu grupo de recursos e selecione a rede virtual para a sua primeira instância.  
2. Selecione **sub-redes** em **Definições** e note o **intervalo De Endereço**. O intervalo de endereços da rede virtual para o caso secundário gerido não pode sobrepor-se a isto. 


   ![Sub-rede primária](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Para criar uma rede virtual, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** e procurar rede *virtual.* 
1. Selecione a opção **Rede Virtual** publicada pela Microsoft e, em seguida, selecione **Criar** na página seguinte. 
1. Preencha os campos necessários para configurar a rede virtual para a sua instância gerida secundária e, em seguida, selecione **Criar**. 

   A tabela a seguir mostra os valores necessários para a rede virtual secundária:

    | **Campo** | Valor |
    | --- | --- |
    | **Nome** |  O nome da rede virtual a ser utilizada pelo caso secundário gerido, como `vnet-sql-mi-secondary` . |
    | **Espaço de endereços** | O espaço de endereço para a sua rede virtual, tal como `10.128.0.0/16` . | 
    | **Subscrição** | A subscrição onde reside o seu principal caso gerido e grupo de recursos. |
    | **Região** | O local onde irá implantar o seu caso secundário gerido. |
    | **Sub-rede** | O nome da sua sub-rede. `default` é fornecido por defeito. |
    | **Intervalo de endereços**| O intervalo de endereços para a sua sub-rede. Isto deve ser diferente da gama de endereços da sub-rede utilizada pela rede virtual da sua primeira instância gerida, como `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Valores de rede virtual secundária](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este passo só é necessário se estiver a utilizar o portal Azure para implantar a SQL Managed Instance. Avance para o passo 3 se estiver a usar o PowerShell. 

---

## <a name="create-a-secondary-managed-instance"></a>Criar um caso secundário gerido
Neste passo, irá criar uma instância secundária gerida no portal Azure, que também configurará a ligação em rede entre as duas instâncias geridas. 

A sua segunda instância gerida deve:
- Fiquem vazios. 
- Ter uma sub-rede e um intervalo de IP diferente do caso gerido primário. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Crie o caso secundário gerido utilizando o portal Azure. 

1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite `Azure SQL` na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda. 
1. **Selecione + Adicione** para abrir a página de **opção de implementação Select SQL.** Pode ver informações adicionais sobre as diferentes bases de dados selecionando detalhes do **Show** no azulejo **de Bases de Dados.**
1. **Selecione Criar** no azulejo **gerido sql.** 

    ![Selecione sql gestdione instance](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. No separador **Basics** da página Configuração de **Exemplo Gerida do Azure SQL,** preencha os campos necessários para configurar o seu caso secundário gerido. 

   O quadro a seguir mostra os valores necessários para a instância gerida secundária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A subscrição onde está a sua primeira instância gerida. |
    | **Grupo de recursos**| O grupo de recursos onde está a sua primeira instância gerida. |
    | **Nome de instância gerida sql** | O nome do seu novo caso secundário gerido, como `sql-mi-secondary` .  | 
    | **Região**| A localização para o seu caso secundário gerido.  |
    | **Login de administração de exemplos geridos sql** | O login que pretende utilizar para o seu novo caso secundário gerido, como `azureuser` . |
    | **Palavra-passe** | Uma senha complexa que será usada pelo login de administração para o novo caso secundário gerido.  |
    | &nbsp; | &nbsp; |

1. No **separador 'Rede',** para a **Rede Virtual,** selecione a rede virtual que criou para a instância gerida secundária a partir do drop-down.

   ![Rede de MI secundária](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. No **separador Definições Adicionais,** para **Geo-Replicação,** escolha **Sim** a _Utilizar como secundário de failover_. Selecione a instância gerida primária a partir do drop-down. 
    
   Certifique-se de que a colisão e o fuso horário coincidem com os da primeira instância gerida. O primeiro exemplo gerido criado neste tutorial usou o padrão de `SQL_Latin1_General_CP1_CI_AS` colagem e o `(UTC) Coordinated Universal Time` fuso horário. 

   ![Rede de casos geridos secundários](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Selecione **Review + crie** para rever as definições para o seu caso secundário gerido. 
1. Selecione **Criar** para criar o seu caso secundário gerido. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o caso secundário gerido utilizando o PowerShell. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede numa rede virtual. | 
| [Grupo deSegurança New-AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma mesa de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Arranja um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota para uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria um caso gerido.  |

---

## <a name="create-a-primary-gateway"></a>Criar um portal primário 

Para que dois casos geridos participem num grupo de failover, deve haver um ExpressRoute ou um gateway configurado entre as redes virtuais das duas instâncias geridas para permitir a comunicação da rede. Se optar por configurar o [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) em vez de ligar duas portas VPN, avance para o [passo 7](#create-a-failover-group).  

Este artigo fornece passos para criar os dois gateways VPN e conectá-los, mas você pode saltar antes para criar o grupo de failover se você tiver configurado ExpressRoute em vez disso. 

> [!NOTE]
> O SKU do gateway afeta o desempenho da produção. Este tutorial implementa uma porta de entrada com o SKU mais básico `HwGw1` (). Implemente um SKU mais alto (exemplo: `VpnGw3` ) para obter uma produção mais elevada. Para todas as opções disponíveis, consulte [Gateway SKUs](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o portal para a rede virtual da sua primeira instância gerida utilizando o portal Azure. 


1. No [portal Azure,](https://portal.azure.com)vá ao seu grupo de recursos e selecione o recurso **de rede Virtual** para a sua primeira instância gerida. 
1. Selecione **sub-redes** em **Definições** e, em seguida, selecione para adicionar uma nova **sub-rede Gateway**. Deixe os valores predefinidos. 

   ![Adicionar porta de entrada para a primeira instância gerida](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Assim que o gateway da sub-rede for criado, **selecione Criar um recurso** a partir do painel de navegação esquerdo e, em seguida, digitar na caixa de `Virtual network gateway` pesquisa. Selecione o recurso **gateway de rede Virtual** publicado pela **Microsoft**. 

   ![Criar um novo portal de rede virtual](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Preencha os campos necessários para configurar o portal para a sua primeira instância gerida. 

   O quadro a seguir mostra os valores necessários para a porta de entrada para a instância gerida primária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A subscrição onde está a sua primeira instância gerida. |
    | **Nome** | O nome para o seu gateway de rede virtual, como `primary-mi-gateway` . | 
    | **Região** | A região onde está o seu principal caso gerido. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo VPN** | Selecione **Route-based**. |
    | **SKU**| Deixar o padrão de `VpnGw1` . |
    | **Rede virtual**| Selecione a rede virtual que foi criada na secção 2, tal como `vnet-sql-mi-primary` . |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Insira um nome para o seu endereço IP, como `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **Review + create** para rever as definições do seu gateway de rede virtual.

   ![Definições de gateway primário](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Selecione **Criar** para criar o seu novo portal de rede virtual. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o portal para a rede virtual da sua primeira instância gerida utilizando o PowerShell. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede numa rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração IP para um gateway de rede virtual. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria uma porta de entrada de rede virtual. |


---


## <a name="create-secondary-gateway"></a>Criar porta de entrada secundária 
Neste passo, crie o portal para a rede virtual do seu caso secundário gerido utilizando o portal Azure. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Utilizando o portal Azure, repita os passos na secção anterior para criar a sub-rede de rede virtual e gateway para a instância gerida secundária. Preencha os campos necessários para configurar o portal para o seu caso secundário gerido. 

   O quadro a seguir mostra os valores necessários para a porta de entrada para a instância gerida secundária:

   | **Campo** | Valor |
   | --- | --- |
   | **Subscrição** |  A subscrição onde está a sua instância de gestão secundária. |
   | **Nome** | O nome para o seu gateway de rede virtual, como `secondary-mi-gateway` . | 
   | **Região** | A região onde o seu caso secundário gerido é. |
   | **Tipo de gateway** | Selecione **VPN**. |
   | **Tipo VPN** | Selecione **Route-based**. |
   | **SKU**| Deixar o padrão de `VpnGw1` . |
   | **Rede virtual**| Selecione a rede virtual para o caso secundário gerido, como `vnet-sql-mi-secondary` . |
   | **Endereço IP público**| Selecione **Criar novo**. |
   | **Nome do endereço IP público**| Insira um nome para o seu endereço IP, como `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Definições de gateway secundária](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o portal para a rede virtual do caso secundário gerido utilizando o PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede numa rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração IP para um gateway de rede virtual. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria uma porta de entrada de rede virtual. |

---


## <a name="connect-the-gateways"></a>Ligar os gateways
Neste passo, crie uma ligação bidirecional entre as duas portas de entrada das duas redes virtuais. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Ligue os dois gateways utilizando o portal Azure. 


1. **Selecione Criar um recurso** a partir do portal [Azure.](https://portal.azure.com)
1. Digite `connection` a caixa de pesquisa e, em seguida, prima para introduzir para pesquisar, o que o leva ao recurso **Connection,** publicado pela Microsoft.
1. Selecione **Criar** para criar a sua ligação. 
1. Na página **Basics,** selecione os seguintes valores e, em seguida, selecione **OK**. 
    1. Selecione `VNet-to-VNet` para o tipo de **Ligação**. 
    1. Selecione a subscrição na lista pendente. 
    1. Selecione o grupo de recursos para SQL Managed Instance no drop-down. 
    1. Selecione a localização da sua primeira instância gerida a partir do drop-down. 
1. Na página **Definições,** selecione ou introduza os seguintes valores e, em seguida, selecione **OK** :
    1. Escolha o portal de rede primário para o **primeiro gateway de rede virtual,** como `primaryGateway` .  
    1. Escolha o portal de rede secundária para o **segundo gateway de rede virtual,** como `secondaryGateway` . 
    1. Selecione a caixa de verificação ao lado **de Estabelecer conectividade bidirecional**. 
    1. Ou deixe o nome de ligação primária predefinido ou mude-o para um valor à sua escolha. 
    1. Forneça uma **chave partilhada (PSK)** para a ligação, tais como `mi1m2psk` . 
    1. Selecione **OK** para guardar as suas definições. 

    ![Criar ligação de gateway](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. Na página **'Rever + criar',** rever as definições da sua ligação bidirecional e, em seguida, selecione **OK** para criar a sua ligação. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ligue os dois gateways utilizando o PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Esta parte do tutorial utiliza o seguinte cmdlet PowerShell:

| Comando | Notas |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma ligação entre os dois gateways de rede virtuais.   |

---


## <a name="create-a-failover-group"></a>Criar um grupo de failover
Neste passo, irá criar o grupo de failover e adicionar-lhe ambas as instâncias geridas. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie o grupo de failover utilizando o portal Azure. 


1. Selecione **Azure SQL** no menu esquerdo do [portal Azure](https://portal.azure.com). Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite `Azure SQL` na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda. 
1. Selecione a primeira instância gerida que criou na primeira secção, como `sql-mi-primary` . 
1. Em **Definições** , navegue para **grupos de failover de exemplo** e, em seguida, escolha adicionar **grupo** para abrir a página do Grupo de Falha **de Exemplo.** 

   ![Adicione um grupo de failover](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Na página do **Grupo Desfalque de Exemplo,** escreva o nome do seu grupo de failover, tal como `failovergrouptutorial` . Em seguida, escolha o caso secundário gerido, `sql-mi-secondary` como, a partir da queda. Selecione **Criar** para criar o seu grupo de failover. 

   ![Criar grupo de failover](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Uma vez concluída a implementação do grupo de failover, será levado de volta para a página do **grupo Failover.** 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Crie o grupo de failover utilizando o PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Esta parte do tutorial utiliza o seguinte cmdlet PowerShell:

| Comando | Notas |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de failover de instâncias geridas Azure SQL.  |


---


## <a name="test-failover"></a>Ativação pós-falha de teste
Neste passo, falhará o seu grupo de failover para o servidor secundário e, em seguida, falhará de volta usando o portal Azure. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Teste falha usando o portal Azure. 


1. Navegue para o seu caso _secundário_ gerido dentro do [portal Azure](https://portal.azure.com) e selecione **Grupos de Failover de Exemplo** em definições. 
1. A revisão que geriu o caso é a principal, e que o caso gerido é o secundário. 
1. Selecione **Failover** e, em seguida, selecione **Sim** no aviso sobre as sessões de TDS serem desligadas. 

   ![Falha sobre o grupo de failover](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. A revisão que geriu o caso é a primeira e que geriu o caso é a secundária. Se o fracasso foi bem sucedido, as duas instâncias deveriam ter trocado de funções. 

   ![Casos geridos mudaram de função após falha](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Vá para o novo caso _secundário_ gerido e selecione **Failover** mais uma vez para falhar a instância primária de volta ao papel principal. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Teste falha usando PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Reverta o grupo de failover de volta ao servidor primário:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista os grupos de failover de instâncias geridas SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa uma falha de um grupo de failover sql Managed Instance. | 

---



## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos eliminando primeiro as instâncias geridas, depois o cluster virtual, depois os recursos restantes, e, finalmente, o grupo de recursos. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navegue para o seu grupo de recursos no [portal Azure](https://portal.azure.com). 
1. Selecione as instâncias geridas e, em seguida, **selecione Delete**. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. Este processo pode demorar algum tempo a ser concluído em segundo plano, e até que esteja feito, não será capaz de eliminar o *cluster virtual* ou quaisquer outros recursos dependentes. Monitorize a eliminação no separador **Atividade** para confirmar que a sua instância gerida foi eliminada. 
1. Uma vez eliminada a instância gerida, elimine o *cluster virtual* selecionando-o no seu grupo de recursos e, em seguida, escolhendo **Delete**. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. 
1. Elimine os recursos restantes. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. 
1. Eliminar o grupo de recursos selecionando eliminar o **grupo de recursos,** digitando em nome do grupo de `myResourceGroup` recursos, e selecionando **delete**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Terá de remover o grupo de recursos duas vezes. Remover o grupo de recursos pela primeira vez removerá as instâncias geridas e os clusters virtuais, mas depois falhará com a mensagem de erro `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Executar o Remove-AzResourceGroup comandar uma segunda vez para remover quaisquer recursos residuais, bem como o grupo de recursos.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

Esta parte do tutorial utiliza o seguinte cmdlet PowerShell:

| Comando | Notas |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. |

---

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede numa rede virtual. | 
| [Grupo deSegurança New-AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma mesa de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Arranja um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota para uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria um caso gerido.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Devolve informações sobre a Azure SQL Managed Instance. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração IP para um gateway de rede virtual. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria uma porta de entrada de rede virtual. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma ligação entre os dois gateways de rede virtuais.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de failover sql Managed Instance.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista os grupos de failover de instâncias geridas SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa uma falha de um grupo de failover sql Managed Instance. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Não existem scripts disponíveis para o portal Azure.

---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configuraste um grupo de falhanços entre duas instâncias geridas. Aprendeu a:

> [!div class="checklist"]
> - Criar um exemplo gerido primário.
> - Criar um caso secundário gerido como parte de um [grupo de failover](../database/auto-failover-group-overview.md). 
> - Teste falhado.

Avance para o próximo quickstart sobre como ligar-se a SQL Managed Instance, e como restaurar uma base de dados para SQL Managed Instance: 

> [!div class="nextstepaction"]
> [Ligue-se à sql Gestd instance](connect-vm-instance-configure.md) 
>  [Restaurar uma base de dados para SQL Gestditundo Instância](restore-sample-database-quickstart.md)


