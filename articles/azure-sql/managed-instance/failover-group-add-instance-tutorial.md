---
title: 'Tutorial: Adicione a um grupo de auto-failover'
titleSuffix: Azure SQL Managed Instance
description: Neste tutorial, você vai criar duas Instâncias Geridas Azure SQL como primária e secundária, e depois adicioná-las a um grupo de falha automática.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: 925e6788035952a4e7b54b8d50b910243a754a09
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041284"
---
# <a name="tutorial-add-a-sql-managed-instance-to-a-failover-group"></a>Tutorial: Adicione um SQL Managed Instance a um grupo de failover
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Adicione um Azure SQL Managed Instance a um grupo de failover. Neste artigo, aprenderá a:

> [!div class="checklist"]
> - Criar uma instância gerida primária sQL
> - Crie uma Instância Gerida Secundária SQL como parte de um grupo de [failover](../database/auto-failover-group-overview.md). 
> - Ativação pós-falha de teste

  > [!NOTE]
  > - Ao passar por este tutorial, certifique-se de que está a configurar os seus recursos com os [pré-requisitos para a criação de grupos de failover para a Instância Gerida sQL](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - A criação de uma Instância Gerida SQL pode demorar muito tempo. Como resultado, este tutorial pode levar várias horas para ser concluído. Para obter mais informações sobre os tempos de provisionamento, consulte as operações de gestão de [instâncias geridas pela SQL.](sql-managed-instance-paas-overview.md#management-operations) 
  > - As Instâncias Geridas sQL que participam num grupo de failover requerem [expressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ou dois gateways VPN conectados. Este tutorial fornece passos para criar e ligar os gateways VPN. Ignore estes passos se já tiver expressRoute configurado. 


## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para concluir este tutorial, confirme que tem: 

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para completar o tutorial, certifique-se de que tem os seguintes itens:

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-sql-mi"></a>1 - Criar o grupo de recursos e o SQL MI primário

Neste passo, irá criar o grupo de recursos e a instância gerida principal sQL para o seu grupo de failover utilizando o portal Azure ou PowerShell. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Crie o grupo de recursos e a sua instância gerida principal sQL utilizando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do portal Azure. Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite na caixa de `Azure SQL` pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione **+ Adicione** para abrir a página de opção de **implementação Select SQL.** Pode ver informações adicionais sobre as diferentes bases de dados selecionando detalhes do Show no azulejo base de dados.
1. Selecione **Criar** no azulejo **SQL Managed Instances.** 

    ![Selecione SQL Managed Instance](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Na página **Create Azure SQL Managed Instance,** no separador **Basics**
    1. Em Detalhes do **Projeto,** selecione a sua **Subscrição** a partir da queda e, em seguida, escolha **criar um novo** grupo de recursos. Digite um nome para o seu grupo de recursos, como `myResourceGroup` . 
    1. Em detalhes de **instância gerida sQL,** forneça o nome da sua Instância Gerida SQL e da região onde deseja implementar a sua Instância Gerida SQL. Deixe o **Cálculo + armazenamento** em valores predefinidos. 
    1. Em **Conta administradora,** forneça um login administrativo, como, por exemplo, e uma senha de `azureuser` administração complexa. 

    ![Criar MI primário](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Deixe o resto das definições em valores predefinidos e selecione **Review + crie** para rever as definições de Instância Gerida sQL. 
1. Selecione **Criar** para criar a sua instância gerida principal mente SQL. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o seu grupo de recursos e a instância gerida principal sQL utilizando o PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary SQL Managed Instance
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
   
   
   # Set the networking values for your secondary SQL Managed Instance
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
   
   
   
   # Set the SQL Managed Instance name for the new SQL Managed Instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
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
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
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
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
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
   
   
   # Create primary SQL Managed Instance
   
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
| [Rede Nova AzVirtual](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Tem uma sub-rede numa rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [Nova AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma mesa de rota. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Arranja um grupo de segurança da rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança da rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota para uma mesa de rota. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerida azure SQL.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Criar rede virtual secundária

Se estiver a utilizar o portal Azure para criar a sua Instância Gerida SQL, terá de criar a rede virtual separadamente porque existe a exigência de que a subrede da Instância Gerida SQL primária e secundária não tenha gamas sobrepostas. Se estiver a utilizar o PowerShell para configurar a sua Instância Gerida SQL, salte para a frente para o passo 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Para verificar a gama de subneta da sua rede virtual primária, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)navegue para o seu grupo de recursos e selecione a rede virtual para a sua instância principal.  
2. Selecione **Subnets** em **Definições** e note a **gama 'Endereços'.** A gama de endereços de sub-rede da rede virtual para a instância gerida secundária SQL não pode sobrepor-se a esta. 


   ![Sub-rede primária](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Para criar uma rede virtual, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** e procurar rede *virtual.* 
1. Selecione a opção **Rede Virtual** publicada pela Microsoft e, em seguida, selecione **Criar** na página seguinte. 
1. Preencha os campos necessários para configurar a rede virtual para a sua instância gerida secundária sQL e, em seguida, selecione **Criar**. 

   O quadro seguinte mostra os valores necessários para a rede virtual secundária:

    | **Campo** | Valor |
    | --- | --- |
    | **Nome** |  O nome da rede virtual a utilizar pela instância gerida secundária sQL, como `vnet-sql-mi-secondary` . |
    | **Espaço de endereço** | O espaço de endereço para a sua rede virtual, como `10.128.0.0/16` . | 
    | **Subscrição** | A subscrição onde reside o seu principal SQL Managed Instance e o grupo de recursos. |
    | **Região** | O local onde irá implantar a sua instância gerida secundária SQL. |
    | **Sub-rede** | O nome da sua sub-rede. `default`é fornecido por defeito. |
    | **Intervalo de endereços**| O intervalo de endereços para a sua sub-rede. Isto deve ser diferente da gama de endereços de sub-rede utilizada pela rede virtual da sua instância gerida principal sQL, como `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Valores de rede virtual secundária](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este passo só é necessário se estiver a utilizar o portal Azure para implementar a sua Instância Gerida SQL. Avance para o passo 3 se estiver a usar o PowerShell. 

---

## <a name="3---create-a-secondary-sql-managed-instance"></a>3 - Criar uma instância gerida secundária sQL
Neste passo, irá criar uma Instância Gerida Secundária SQL no portal Azure, que também configurará a rede entre as duas Instâncias Geridas SQL. 

A sua segunda Instância Gerida SQL deve:
- Esteja vazio. 
- Tenha uma sub-rede e uma gama IP diferente sem a instância gerida principal sQL. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Crie a instância gerida secundária SQL utilizando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do portal Azure. Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione **+ Adicione** para abrir a página de opção de **implementação Select SQL.** Pode ver informações adicionais sobre as diferentes bases de dados selecionando detalhes do Show no azulejo base de dados.
1. Selecione **Criar** no azulejo **SQL SQL Managed Instances.** 

    ![Selecione SQL Managed Instance](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. No separador **Basics** da página **Create Azure SQL Managed Instance,** preencha os campos necessários para configurar a sua instância gerida secundária sQL. 

   O quadro seguinte mostra os valores necessários para a instância gerida secundária SQL:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A subscrição onde está a sua instância gerida principal SQL. |
    | **Grupo de recursos**| O grupo de recursos onde está a sua instância gerida principal sQL. |
    | **Nome de instância gerida SQL** | O nome do seu novo SQL Managed Instance secundário, como`sql-mi-secondary`  | 
    | **Região**| A localização para a sua instância gerida secundária SQL.  |
    | **SQL Managed Instance administrador login** | O login que pretende utilizar para a sua nova Instância Gerida Secundária SQL, como `azureuser` . |
    | **Palavra-passe** | Uma senha complexa que será usada pelo login administrativo para a nova Instância Gerida SQL secundária.  |
    | &nbsp; | &nbsp; |

1. Sob o **separador Networking,** para a **Rede Virtual,** selecione a rede virtual que criou para o Caso Gerido SQL secundário a partir da queda.

   ![Rede secundária de MI](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Sob o separador de **definições Adicional,** para **Geo-Replicação,** escolha **Sim** a _Usar como failover secundário_. Selecione a instância sql gerida primária a partir da queda. 
    1. Certifique-se de que a colagem e o fuso horário correspondem ao da instância gerida principal sQL. A instância primária sQL Gerida criada neste tutorial usou o padrão de `SQL_Latin1_General_CP1_CI_AS` colagem e o `(UTC) Coordinated Universal Time` fuso horário. 

   ![Rede secundária de MI](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Selecione **Review + crie** para rever as definições para a sua instância gerida secundária SQL. 
1. Selecione **Criar** para criar a sua instância gerida secundária SQL. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie a instância gerida secundária SQL utilizando powerShell. 

   ```powershell-interactive
   # Configure secondary virtual network
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
   
   # Configure secondary SQL Managed Instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
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
   
   # Create secondary SQL Managed Instance
   
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
| [Rede Nova AzVirtual](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Tem uma sub-rede numa rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [Nova AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma mesa de rota. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Arranja um grupo de segurança da rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança da rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota para uma mesa de rota. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerida azure SQL.  |

---

## <a name="4---create-primary-gateway"></a>4 - Criar porta de entrada primária 

Para que duas Instâncias Geridas sQL participem num grupo de failover, deve haver ou ExpressRoute ou uma porta de entrada configurada entre as redes virtuais das duas Instâncias Geridas SQL para permitir a comunicação de rede. Se optar por configurar o [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) em vez de ligar dois gateways VPN, salte para a frente para o [Passo 7](#7---create-a-failover-group).  

Este artigo fornece passos para criar os dois gateways VPN e conectá-los, mas pode saltar para a frente para criar o grupo failover se tiver configurado expressRoute em vez disso. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie a porta de entrada para a rede virtual da sua instância gerida principal sQL utilizando o portal Azure. 


1. No [portal Azure,](https://portal.azure.com)dirija-se ao seu grupo de recursos e selecione o recurso de **rede Virtual** para a sua instância gerida principal mente SQL. 
1. Selecione **Subnets** em **Definições** e, em seguida, selecione para adicionar uma nova **sub-rede Gateway**. Deixe os valores padrão. 

   ![Adicionar gateway para a instância gerida primária SQL](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Assim que o gateway da sub-rede for criado, selecione **Criar um recurso** a partir do painel de navegação esquerdo e, em seguida, digitar na caixa de `Virtual network gateway` pesquisa. Selecione o recurso de gateway da **rede Virtual** publicado pela **Microsoft**. 

   ![Criar um novo portal de rede virtual](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Preencha os campos necessários para configurar o portal da sua instância gerida principal sQL. 

   O quadro seguinte mostra os valores necessários para a porta de entrada para a instância gerida primária SQL:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A subscrição onde está a sua instância gerida principal SQL. |
    | **Nome** | O nome para o seu portal de rede virtual, como `primary-mi-gateway` . | 
    | **Região** | A região onde está a sua principal Instância Gerida sQL. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo VPN** | Selecione **baseado em rota** |
    | **SKU**| Deixe o padrão de `VpnGw1` . |
    | **Localização**| O local onde está a sua principal Instância Gerida sQL e a rede virtual primária.   |
    | **Rede virtual**| Selecione a rede virtual que foi criada na secção 2, como `vnet-sql-mi-primary` . |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Introduza um nome para o seu endereço IP, como `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **Review + crie** para rever as definições para o seu gateway de rede virtual.

   ![Definições primárias de gateway](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Selecione **Criar** para criar o seu novo portal de rede virtual. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie a porta de entrada para a rede virtual do seu caso gerido principal SQL utilizando powerShell. 

   ```powershell-interactive
   # Create primary gateway
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
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Tem uma sub-rede numa rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração IP para um gateway de rede virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um Gateway de Rede Virtual |


---


## <a name="5---create-secondary-gateway"></a>5 - Criar gateway secundário 
Neste passo, crie a porta de entrada para a rede virtual da sua Instância Gerida Secundária SQL utilizando o portal Azure, 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Utilizando o portal Azure, repita os passos na secção anterior para criar a subnet de rede virtual e a porta de entrada para a instância gerida secundária do SQL. Preencha os campos necessários para configurar o portal para a sua instância gerida secundária SQL. 

   O quadro seguinte mostra os valores necessários para a porta de entrada para a instância gerida secundária SQL:

   | **Campo** | Valor |
   | --- | --- |
   | **Subscrição** |  A subscrição onde está a sua instância gerida secundária SQL. |
   | **Nome** | O nome para o seu portal de rede virtual, como `secondary-mi-gateway` . | 
   | **Região** | A região onde está a sua instância gerida secundária SQL. |
   | **Tipo de gateway** | Selecione **VPN**. |
   | **Tipo VPN** | Selecione **baseado em rota** |
   | **SKU**| Deixe o padrão de `VpnGw1` . |
   | **Localização**| O local onde está a sua instância gerida secundária sQL e a rede virtual secundária.   |
   | **Rede virtual**| Selecione a rede virtual que foi criada na secção 2, como `vnet-sql-mi-secondary` . |
   | **Endereço IP público**| Selecione **Criar novo**. |
   | **Nome do endereço IP público**| Introduza um nome para o seu endereço IP, como `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Definições secundárias de gateway](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie a porta de entrada para a rede virtual da instância gerida secundária SQL utilizando powerShell. 

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
   
   Write-host "Creating primary gateway..."
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
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Tem uma sub-rede numa rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração IP para um gateway de rede virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um Gateway de Rede Virtual |

---


## <a name="6---connect-the-gateways"></a>6 - Ligar os gateways
Neste passo, crie uma ligação bidirecional entre os dois gateways das duas redes virtuais. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Ligue os dois portais utilizando o portal Azure. 


1. Selecione **Criar um recurso** a partir do portal [Azure](https://portal.azure.com).
1. Digite na caixa de `connection` pesquisa e, em seguida, prima introduza para pesquisar, o que o leva ao recurso **Connection,** publicado pela Microsoft.
1. Selecione **Criar** para criar a sua ligação. 
1. No separador Basics, selecione os **seguintes valores** e, em seguida, selecione **OK**. 
    1. Selecione `VNet-to-VNet` para o tipo de **Ligação**. 
    1. Selecione a subscrição na lista pendente. 
    1. Selecione o grupo de recursos para o seu Caso Gerido SQL na queda. 
    1. Selecione a localização do seu SQL Managed Instance primário a partir da queda 
1. No separador **Definições,** selecione ou introduza os seguintes valores e, em seguida, selecione **OK**:
    1. Escolha o portal principal de rede para o primeiro portal de **rede virtual,** como `Primary-Gateway` .  
    1. Escolha o gateway de rede secundário para o segundo portal de **rede virtual,** como `Secondary-Gateway` . 
    1. Selecione a caixa de verificação ao lado de **Estabelecer conectividade bidirecional**. 
    1. Ou deixe o nome de ligação primária padrão, ou mude o nome para um valor à sua escolha. 
    1. Forneça uma **chave partilhada (PSK)** para a ligação, tais como `mi1m2psk` . 

   ![Criar ligação gateway](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

1. No separador **Resumo,** reveja as definições para a sua ligação bidirecional e, em seguida, selecione **OK** para criar a sua ligação. 


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
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma ligação entre os dois gateways de rede virtual.   |

---


## <a name="7---create-a-failover-group"></a>7 - Criar um grupo de failover
Neste passo, irá criar o grupo failover e adicionar-lhe ambas as Instâncias Geridas SQL. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie o grupo failover utilizando o portal Azure. 


1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione a instância primária gerida por SQL criada na primeira secção, tais como `sql-mi-primary` . 
1. Em **Definições,** navegue para **Grupos de Failover de Instância** e, em seguida, opte por adicionar **grupo** para abrir a página do **Grupo Failover de Instância.** 

   ![Adicione um grupo de failover](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Na página do **Grupo Failover Instance,** digite o nome do seu grupo failover, como e, em seguida, escolha a instância gerida secundária `failovergrouptutorial` sQL, como por exemplo `sql-mi-secondary` a partir da queda. Selecione **Criar** para criar o seu grupo failover. 

   ![Criar grupo failover](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Uma vez concluída a implementação do grupo failover, será saqueado de volta para a página do **grupo Failover.** 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Crie o grupo failover utilizando o PowerShell. 

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
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de falhas de instância gerida Azure SQL.  |


---


## <a name="8---test-failover"></a>8 - Falha de teste
Neste passo, falhará o seu grupo de failover para o servidor secundário e, em seguida, falhará usando o portal Azure. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Teste failover usando o portal Azure. 


1. Navegue para a sua instância gerida _secundária_ sQL dentro do [portal Azure](https://portal.azure.com) e selecione **Instance Failover Groups** em definições. 
1. Reveja qual a Instância Gerida sQL é a primária, e qual a Instância Gerida SQL é a secundária. 
1. Selecione **Failover** e, em seguida, selecione **Sim** no aviso sobre as sessões de TDS estarem desligadas. 

   ![Falhar sobre o grupo failover](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Reveja qual a Instância Gerida sQL é a principal e qual a Instância Gerida SQL é a secundária. Se falhar em relação ao sucesso, as duas instâncias deveriam ter mudado de funções. 

   ![As instâncias geridas pela SQL mudaram de funções após a falha](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Vá ao novo SQL Managed Instance secundário e _selecione_ **Failover** mais uma vez para falhar a instância primária de volta ao papel principal. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Teste failover usando PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary SQL Managed Instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Reverter o grupo failover de volta ao servidor primário:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary SQL Managed Instance back to primary role
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
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista grupos de failover sQL Managed Instance.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa uma falha de um grupo de failover SQL Managed Instance. | 

---



## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos primeiro, apagando o SQL Managed Instance, depois o cluster virtual, depois quaisquer recursos restantes, e finalmente o grupo de recursos. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navegue para o seu grupo de recursos no [portal Azure.](https://portal.azure.com) 
1. Selecione as instâncias geridas sQL e, em seguida, **selecione Eliminar**. Digite `yes` a caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. Este processo pode demorar algum tempo a ser concluído em segundo plano, e até que esteja feito, não poderá eliminar o *cluster Virtual* ou quaisquer outros recursos dependentes. Monitorize a eliminação no separador Atividade para confirmar que a sua Instância Gerida SQL foi eliminada. 
1. Uma vez eliminada a Instância Gerida SQL, elimine o *cluster Virtual* selecionando-o no seu grupo de recursos e, em seguida, escolhendo **Eliminar**. Digite `yes` a caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. 
1. Eliminar os recursos restantes. Digite `yes` a caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, selecione **Eliminar**. 
1. Elimine o grupo de recursos selecionando o **grupo de recursos Delete**, digitando em nome do grupo de recursos e, em `myResourceGroup` seguida, selecionando **Delete**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Terá de remover o grupo de recursos duas vezes. Remover o grupo de recursos pela primeira vez removerá a Instância Gerida SQL e os clusters virtuais, mas depois falhará com a mensagem de erro `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.` . Executar o comando Remove-AzResourceGroup uma segunda vez para remover quaisquer recursos residuais, bem como o grupo de recursos.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
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
| [Rede Nova AzVirtual](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Tem uma sub-rede numa rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [Nova AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma mesa de rota. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Arranja um grupo de segurança da rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança da rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota para uma mesa de rota. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerida azure SQL.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Devolve informações sobre a Instância de Base de Dados Gerida azure SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração IP para um gateway de rede virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um Gateway de Rede Virtual |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma ligação entre os dois gateways de rede virtual.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de falhas de instância gerida Azure SQL.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista grupos de failover sQL Managed Instance.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa uma falha de um grupo de failover SQL Managed Instance. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Não existem scripts disponíveis para o portal Azure.

---

## <a name="next-steps"></a>Próximos passos

Neste tutorial, configuraum grupo de falhas entre duas Instâncias Geridas SQL. Aprendeu a:

> [!div class="checklist"]
> - Criar uma instância gerida primária sQL
> - Crie uma Instância Gerida Secundária SQL como parte de um grupo de [failover](../database/auto-failover-group-overview.md). 
> - Ativação pós-falha de teste

Avance para o próximo início rápido sobre como ligar-se à sua Instância Gerida SQL e como restaurar uma base de dados para a sua Instância Gerida SQL: 

> [!div class="nextstepaction"]
> [Ligue-se à sua instância](connect-vm-instance-configure.md) 
>  gerida pela SQL Restaurar uma base de dados para uma instância gerida pela [SQL](restore-sample-database-quickstart.md)


