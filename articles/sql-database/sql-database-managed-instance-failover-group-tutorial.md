---
title: 'Tutorial: Adicione um exemplo gerido a um grupo de failover'
description: Aprenda a configurar um grupo de failover para a sua base de dados Azure SQL gerida.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462654"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Tutorial: Adicione uma base de dados SQL gerida a um grupo de failover

Adicione uma base de dados SQL gerida a um grupo de failover. Neste artigo, ficará a saber como:

> [!div class="checklist"]
> - Criar uma instância gerida primária
> - Criar um exemplo de gestão secundária como parte de um grupo de [failover](sql-database-auto-failover-group.md). 
> - Ativação pós-falha de teste

  > [!NOTE]
  > - Ao passar por este tutorial, certifique-se de que está a configurar os seus recursos com os [pré-requisitos para a criação de grupos de failover para exemplo gerido](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Criar um caso gerido pode levar uma quantidade significativa de tempo. Como resultado, este tutorial pode levar várias horas para ser concluído. Para obter mais informações sobre os tempos de provisionamento, consulte [as operações de gestão de instâncias geridas.](sql-database-managed-instance.md#managed-instance-management-operations) 
  > - As instâncias geridas que participam num grupo de failover requerem [expressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ou dois gateways VPN conectados. Este tutorial fornece passos para criar e ligar os gateways VPN. Ignore estes passos se já tiver expressRoute configurado. 


## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para concluir este tutorial, confirme que tem: 

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para completar o tutorial, certifique-se de que tem os seguintes itens:

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - Criar grupo de recursos e instância gerida primária
Neste passo, irá criar o grupo de recursos e a principal instância gerida para o seu grupo failover usando o portal Azure ou PowerShell. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Crie o grupo de recursos e a sua principal instância gerida utilizando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do portal Azure. Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione **+ Adicione** para abrir a página de opção de **implementação Select SQL.** Pode ver informações adicionais sobre as diferentes bases de dados selecionando detalhes do Show no azulejo base de dados.
1. Selecione **Criar** no azulejo **de instâncias geridas pela SQL.** 

    ![Selecione instância gerida](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na página **Create Azure SQL Database Managed Instance,** no separador **Basics**
    1. Em Detalhes do **Projeto,** selecione a sua **Subscrição** a partir da queda e, em seguida, escolha **criar um novo** grupo de recursos. Digite um nome para o seu grupo de recursos, como `myResourceGroup`. 
    1. Em detalhes de **instância geridas,** forneça o nome da sua instância gerida, e a região onde deseja implementar a sua instância gerida. Deixe o **Cálculo + armazenamento** em valores predefinidos. 
    1. Em **Conta administradora,** forneça um login administrativo, como `azureuser`, e uma senha de administração complexa. 

    ![Criar MI primário](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Deixe o resto das definições em valores predefinidos e selecione **Review + crie** para rever as definições de instância geridas. 
1. Selecione **Criar** para criar a sua instância gerida principalmente. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o seu grupo de recursos e a instância gerida principal usando o PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
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
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
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
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
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
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
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
   Write-host "Primary managed instance created successfully."
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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerida pela Base de Dados Azure SQL.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Criar rede virtual secundária
Se estiver a usar o portal Azure para criar a sua instância gerida, terá de criar a rede virtual separadamente porque existe a exigência de que a sub-rede da instância gerida primária e secundária não tenha gamas sobrepostas. Se estiver a utilizar o PowerShell para configurar a sua instância gerida, salte para a frente para o passo 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 
Para verificar a gama de subneta da sua rede virtual primária, siga estes passos:
1. No [portal Azure,](https://portal.azure.com)navegue para o seu grupo de recursos e selecione a rede virtual para a sua instância principal. 
1. Selecione **Subnets** em **Definições** e note a **gama 'Endereços'.** A gama de endereços de sub-rede da rede virtual para a instância gerida secundária não pode sobrepor-se a esta. 


   ![Sub-rede primária](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Para criar uma rede virtual, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** e procurar rede *virtual.* 
1. Selecione a opção **Rede Virtual** publicada pela Microsoft e, em seguida, selecione **Criar** na página seguinte. 
1. Preencha os campos necessários para configurar a rede virtual para a sua instância gerida secundária e, em seguida, selecione **Criar**. 

   O quadro seguinte mostra os valores necessários para a rede virtual secundária:

    | **Campo** | Valor |
    | --- | --- |
    | **Nome** |  O nome para a rede virtual a utilizar pelo caso gerido secundário, como `vnet-sql-mi-secondary`. |
    | **Espaço de endereços** | O espaço de endereço para a sua rede virtual, como `10.128.0.0/16`. | 
    | **Subscrição** | A subscrição onde reside a sua principal instância gerida e o seu grupo de recursos. |
    | **Região** | O local onde irá implementar a sua instância secundária gerida. |
    | **Sub-rede** | O nome da sua sub-rede. `default` é fornecido por defeito. |
    | **Intervalo de endereços**| O intervalo de endereços para a sua sub-rede. Isto deve ser diferente da gama de endereços de sub-rede utilizada pela rede virtual da sua instância gerida principalmente, como `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Valores de rede virtual secundária](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este passo só é necessário se estiver a usar o portal Azure para implementar a sua instância gerida. Avance para o passo 3 se estiver a usar o PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - Criar um caso gerido secundário
Neste passo, irá criar uma instância secundária gerida no portal Azure, que também configurará o networking entre as duas instâncias geridas. 

A sua segunda instância gerida deve:
- Esteja vazio. 
- Tenha uma sub-rede e uma gama ip diferente si do que a instância gerida principalmente. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Crie a instância gerida secundária utilizando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do portal Azure. Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione **+ Adicione** para abrir a página de opção de **implementação Select SQL.** Pode ver informações adicionais sobre as diferentes bases de dados selecionando detalhes do Show no azulejo base de dados.
1. Selecione **Criar** no azulejo **de instâncias geridas pela SQL.** 

    ![Selecione instância gerida](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. No separador **Basics** da página Dedados Gerida para a Base de **Dados Create Azure SQL,** preencha os campos necessários para configurar a sua instância gerida secundária. 

   O quadro seguinte mostra os valores necessários para a instância gerida secundária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A subscrição onde é a sua principal instância gerida. |
    | **Grupo de recursos**| O grupo de recursos onde é a sua principal instância gerida. |
    | **Nome da instância gerida** | O nome do seu novo caso secundário gerido, como `sql-mi-secondary`  | 
    | **Região**| A localização para a sua instância secundária gerida.  |
    | **Início de sessão de administração da instância gerida** | O login que pretende utilizar para a sua nova instância secundária gerida, como `azureuser`. |
    | **Palavra-passe** | Uma senha complexa que será usada pelo login do administrador para a nova instância gerida secundária.  |
    | &nbsp; | &nbsp; |

1. Sob o **separador Networking,** para a **Rede Virtual,** selecione a rede virtual que criou para a instância gerida secundária a partir da queda.

   ![Rede secundária de MI](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Sob o separador de **definições Adicional,** para **Geo-Replicação,** escolha **Sim** a _Usar como failover secundário_. Selecione a instância principal gerida a partir da queda. 
    1. Certifique-se de que a colagem e o fuso horário correspondem ao da instância gerida principalmente. A principal instância gerida criada neste tutorial usou o padrão de `SQL_Latin1_General_CP1_CI_AS` colagem e o fuso horário `(UTC) Coordinated Universal Time`. 

   ![Rede secundária de MI](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Selecione **Review + crie** para rever as definições para a sua instância gerida secundária. 
1. Selecione **Criar** para criar a sua instância gerida secundária. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie a instância gerida secundária utilizando o PowerShell. 

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
   
   # Configure secondary managed instance subnet
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
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
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
   Write-host "Secondary managed instance created successfully."
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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerida pela Base de Dados Azure SQL.  |

---

## <a name="4---create-primary-gateway"></a>4 - Criar porta de entrada primária 
Para dois casos geridos para participar num grupo de failover, deve haver ou ExpressRoute ou uma porta de entrada configurada entre as redes virtuais das duas instâncias geridas para permitir a comunicação de rede. Se optar por configurar o [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) em vez de ligar dois gateways VPN, salte para a frente para o [Passo 7](#7---create-a-failover-group).  

Este artigo fornece passos para criar os dois gateways VPN e conectá-los, mas pode saltar para a frente para criar o grupo failover se tiver configurado expressRoute em vez disso. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie a porta de entrada para a rede virtual da sua instância gerida principal utilizando o portal Azure. 


1. No [portal Azure,](https://portal.azure.com)dirija-se ao seu grupo de recursos e selecione o recurso de **rede Virtual** para a sua instância gerida principalmente. 
1. Selecione **Subnets** em **Definições** e, em seguida, selecione para adicionar uma nova **sub-rede Gateway**. Deixe os valores padrão. 

   ![Adicionar porta de entrada para instância gerida primária](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Assim que o gateway da sub-rede for criado, selecione **Criar um recurso** a partir do painel de navegação esquerdo e, em seguida, digite `Virtual network gateway` na caixa de pesquisa. Selecione o recurso de gateway da **rede Virtual** publicado pela **Microsoft**. 

   ![Criar um novo portal de rede virtual](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Preencha os campos necessários para configurar o portal da sua instância principal gerida. 

   O quadro seguinte mostra os valores necessários para a porta de entrada para a instância gerida primária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A subscrição onde é a sua principal instância gerida. |
    | **Nome** | O nome para o seu portal de rede virtual, como `primary-mi-gateway`. | 
    | **Região** | A região onde o seu caso secundário gerido é. |
    | **Tipo gateway** | Selecione **VPN**. |
    | **Tipo VPN** | Selecione **baseado em rota** |
    | **SKU**| Deixe o padrão de `VpnGw1`. |
    | **Localização**| O local onde a sua principal instância gerida e a rede virtual primária é.   |
    | **Rede virtual**| Selecione a rede virtual que foi criada na secção 2, como `vnet-sql-mi-primary`. |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Introduza um nome para o seu endereço IP, como `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **Review + crie** para rever as definições para o seu gateway de rede virtual.

   ![Definições primárias de gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecione **Criar** para criar o seu novo portal de rede virtual. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie a porta de entrada para a rede virtual da sua instância gerida principal usando o PowerShell. 

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
Neste passo, crie a porta de entrada para a rede virtual da sua instância secundária gerida utilizando o portal Azure, 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Utilizando o portal Azure, repita os passos na secção anterior para criar a subnet de rede virtual e a porta de entrada para a instância gerida secundária. Preencha os campos necessários para configurar o portal para a sua instância gerida secundária. 

   O quadro seguinte mostra os valores necessários para a porta de entrada para a instância gerida secundária:

   | **Campo** | Valor |
   | --- | --- |
   | **Subscrição** |  A subscrição onde é a sua instância secundária gerida. |
   | **Nome** | O nome para o seu portal de rede virtual, como `secondary-mi-gateway`. | 
   | **Região** | A região onde o seu caso secundário gerido é. |
   | **Tipo gateway** | Selecione **VPN**. |
   | **Tipo VPN** | Selecione **baseado em rota** |
   | **SKU**| Deixe o padrão de `VpnGw1`. |
   | **Localização**| O local onde está a sua instância secundária gerida e a rede virtual secundária.   |
   | **Rede virtual**| Selecione a rede virtual que foi criada na secção 2, como `vnet-sql-mi-secondary`. |
   | **Endereço IP público**| Selecione **Criar novo**. |
   | **Nome do endereço IP público**| Introduza um nome para o seu endereço IP, como `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Definições secundárias de gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie a porta de entrada para a rede virtual da instância gerida secundária utilizando o PowerShell. 

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
1. Digite `connection` na caixa de pesquisa e, em seguida, prima introduza para pesquisar, o que o leva ao recurso **Connection,** publicado pela Microsoft.
1. Selecione **Criar** para criar a sua ligação. 
1. No separador Basics, selecione os **seguintes valores** e, em seguida, selecione **OK**. 
    1. Selecione `VNet-to-VNet` para o **tipo de ligação**. 
    1. Selecione a subscrição na lista pendente. 
    1. Selecione o grupo de recursos para a sua instância gerida na queda. 
    1. Selecione a localização da sua instância gerida principal a partir do drop-down 
1. No separador **Definições,** selecione ou introduza os seguintes valores e, em seguida, selecione **OK**:
    1. Escolha o portal principal de rede para o primeiro portal de **rede virtual**, como `Primary-Gateway`.  
    1. Escolha o gateway de rede secundário para o segundo portal de **rede virtual**, como `Secondary-Gateway`. 
    1. Selecione a caixa de verificação ao lado de **Estabelecer conectividade bidirecional**. 
    1. Ou deixe o nome de ligação primária padrão, ou mude o nome para um valor à sua escolha. 
    1. Forneça uma **chave partilhada (PSK)** para a ligação, como `mi1m2psk`. 

   ![Criar ligação gateway](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

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
Neste passo, irá criar o grupo failover e adicionar-lhe ambos os casos geridos. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie o grupo failover utilizando o portal Azure. 


1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
1. Selecione a instância gerida primária que criou na primeira secção, como `sql-mi-primary`. 
1. Em **Definições,** navegue para **Grupos de Failover de Instância** e, em seguida, opte por adicionar **grupo** para abrir a página do **Grupo Failover de Instância.** 

   ![Adicione um grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na página do **Grupo Failover,** escreva o nome do seu grupo failover, como `failovergrouptutorial` e, em seguida, escolha a instância gerida secundária, como `sql-mi-secondary` a partir da queda. Selecione **Criar** para criar o seu grupo failover. 

   ![Criar grupo failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

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
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria uma nova Base de Dados Azure SQL gerida por falhas no grupo.  |


---


## <a name="8---test-failover"></a>8 - Falha de teste
Neste passo, falhará o seu grupo de failover para o servidor secundário e, em seguida, falhará usando o portal Azure. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Teste failover usando o portal Azure. 


1. Navegue para a sua instância gerida _secundária_ dentro do [portal Azure](https://portal.azure.com) e selecione **Instance Failover Groups** em definições. 
1. A revisão da instância gerida é a principal, e que geriu a instância é a secundária. 
1. Selecione **Failover** e, em seguida, selecione **Sim** no aviso sobre as sessões de TDS estarem desligadas. 

   ![Falhar sobre o grupo failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Reveja qual o caso manhoso que é o principal e qual o caso secundário. Se falhar em relação ao sucesso, as duas instâncias deveriam ter mudado de funções. 

   ![Casos geridos mudaram de funções após falha](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Vá para a _nova_ instância secundária gerida e selecione **Failover** mais uma vez para falhar a instância primária de volta ao papel principal. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Teste failover usando PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
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
   
   # Fail primary managed instance back to primary role
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
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista grupos de falhas de instância geridos.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa uma falha de um grupo de falhas de instância gerido. | 

---



## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos primeiro, apagando a instância gerida, depois o cluster virtual, depois quaisquer recursos restantes, e finalmente o grupo de recursos. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navegue para o seu grupo de recursos no [portal Azure.](https://portal.azure.com) 
1. Selecione as instâncias geridas e, em seguida, **selecione Eliminar**. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, **selecione Eliminar**. Este processo pode demorar algum tempo a ser concluído em segundo plano, e até que esteja feito, não poderá eliminar o *cluster Virtual* ou quaisquer outros recursos dependentes. Monitorize a eliminação no separador Atividade para confirmar que a sua instância gerida foi eliminada. 
1. Uma vez eliminada a instância gerida, elimine o *cluster Virtual* selecionando-o no seu grupo de recursos e, em seguida, escolhendo **Eliminar**. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, **selecione Eliminar**. 
1. Eliminar os recursos restantes. Digite `yes` na caixa de texto para confirmar que pretende eliminar o recurso e, em seguida, **selecione Eliminar**. 
1. Elimine o grupo de recursos selecionando o grupo de **recursos Delete**, digitando em nome do grupo de recursos, `myResourceGroup`, e, em seguida, selecionando **Delete**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Terá de remover o grupo de recursos duas vezes. A remoção do grupo de recursos pela primeira vez removerá a instância gerida e os clusters virtuais, mas falhará com a mensagem de erro `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Executar o comando Remove-AzResourceGroup uma segunda vez para remover quaisquer recursos residuais, bem como o grupo de recursos.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
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
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerida pela Base de Dados Azure SQL.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Devolve informações sobre a Instância de Base de Dados Gerida azure SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração IP para um gateway de rede virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um Gateway de Rede Virtual |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma ligação entre os dois gateways de rede virtual.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria uma nova Base de Dados Azure SQL gerida por falhas no grupo.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista grupos de falhas de instância geridos.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa uma falha de um grupo de falhas de instância gerido. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Não existem scripts disponíveis para o portal Azure.

---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configuraste um grupo de falhas entre dois casos geridos. Aprendeu a:

> [!div class="checklist"]
> - Criar uma instância gerida primária
> - Criar um exemplo de gestão secundária como parte de um grupo de [failover](sql-database-auto-failover-group.md). 
> - Ativação pós-falha de teste

Avance para o próximo início rápido sobre como ligar-se à sua instância gerida e como restaurar uma base de dados para a sua instância gerida: 

> [!div class="nextstepaction"]
> [Ligue-se à sua instância gerida](sql-database-managed-instance-configure-vm.md)
> Restaurar uma base de dados a uma instância [gerida](sql-database-managed-instance-get-started-restore.md)


