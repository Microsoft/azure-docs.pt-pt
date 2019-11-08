---
title: 'Tutorial: adicionar uma instância gerenciada a um grupo de failover'
description: Saiba como configurar um grupo de failover para sua instância gerenciada do banco de dados SQL do Azure.
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
ms.openlocfilehash: 85d2e58d35ef233fda7c724f85152fc74bd11189
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826844"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Tutorial: adicionar uma instância gerenciada do banco de dados SQL a um grupo de failover

Adicione uma instância gerenciada do banco de dados SQL a um grupo de failover. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> - Criar uma instância gerenciada primária
> - Crie uma instância gerenciada secundária como parte de um [grupo de failover](sql-database-auto-failover-group.md). 
> - Ativação pós-falha de teste

  > [!NOTE]
  > - Ao percorrer este tutorial, verifique se você está configurando seus recursos com os [pré-requisitos para configurar grupos de failover para instância gerenciada](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - A criação de uma instância gerenciada pode levar uma quantidade significativa de tempo. Como resultado, este tutorial pode levar várias horas para ser concluído. Para obter mais informações sobre os tempos de provisionamento, consulte [operações gerenciadas de gerenciamento de instância](sql-database-managed-instance.md#managed-instance-management-operations). 


## <a name="prerequisites"></a>Pré-requisitos

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para concluir este tutorial, confirme que tem: 

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para concluir o tutorial, verifique se você tem os seguintes itens:

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1-Criar grupo de recursos e instância gerenciada primária
Nesta etapa, você criará o grupo de recursos e a instância gerenciada primária para seu grupo de failover usando o portal do Azure ou o PowerShell. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

Crie o grupo de recursos e sua instância gerenciada primária usando o portal do Azure. 

1. Selecione **SQL do Azure** no menu à esquerda da portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **selecionar opção de implantação do SQL** . Você pode exibir informações adicionais sobre os bancos de dados diferentes selecionando Mostrar detalhes no bloco bancos de dados.
1. Selecione **criar** no bloco **instâncias gerenciadas do SQL** . 

    ![Selecionar instância gerenciada](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na página **criar instância gerenciada do banco de dados SQL do Azure** , na guia **noções básicas**
    1. Em **detalhes do projeto**, selecione sua **assinatura** na lista suspensa e, em seguida, escolha **criar novo** grupo de recursos. Digite um nome para seu grupo de recursos, como `myResourceGroup`. 
    1. Em **detalhes do instância gerenciada**, forneça o nome da sua instância gerenciada e a região em que você deseja implantar sua instância gerenciada. Deixe o **armazenamento de computação +** em valores padrão. 
    1. Em **conta de administrador**, forneça um logon de administrador, como `azureuser`e uma senha de administrador complexa. 

    ![Criar MI primário](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Deixe o restante das configurações em valores padrão e selecione **revisar + criar** para examinar as configurações de instância gerenciada. 
1. Selecione **criar** para criar sua instância gerenciada primária. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie seu grupo de recursos e a instância gerenciada primária usando o PowerShell. 

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

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma tabela de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtém um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota a uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerenciada do banco de dados SQL do Azure.  |

---

## <a name="2---create-secondary-virtual-network"></a>2-criar rede virtual secundária
Se você estiver usando o portal do Azure para criar sua instância gerenciada, será necessário criar a rede virtual separadamente porque há um requisito de que a sub-rede da instância gerenciada primária e secundária não tenha intervalos sobrepostos. Se você estiver usando o PowerShell para configurar sua instância gerenciada, pule para a etapa 3. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 
Para verificar o intervalo de sub-rede de sua rede virtual primária, siga estas etapas:
1. No [portal do Azure](https://portal.azure.com), navegue até o grupo de recursos e selecione a rede virtual para sua instância primária. 
1. Selecione **sub-redes** em **configurações** e anote o **intervalo de endereços**. O intervalo de endereços de sub-rede da rede virtual para a instância gerenciada secundária não pode sobrepor isso. 


   ![Sub-rede primária](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Para criar uma rede virtual, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** e pesquise *rede virtual*. 
1. Selecione a opção **rede virtual** publicada pela Microsoft e, em seguida, selecione **criar** na próxima página. 
1. Preencha os campos obrigatórios para configurar a rede virtual para sua instância gerenciada secundária e, em seguida, selecione **criar**. 

   A tabela a seguir mostra os valores necessários para a rede virtual secundária:

    | **Campo** | Valor |
    | --- | --- |
    | **Nome** |  O nome da rede virtual a ser usada pela instância gerenciada secundária, como `vnet-sql-mi-secondary`. |
    | **Espaço de endereços** | O espaço de endereço para sua rede virtual, como `10.128.0.0/16`. | 
    | **Subscrição** | A assinatura em que reside a instância gerenciada primária e o grupo de recursos. |
    | **Região** | O local onde você vai implantar sua instância gerenciada secundária. |
    | **Sub-rede** | O nome da sua sub-rede. o `default` é fornecido para você por padrão. |
    | **Intervalo de endereços**| O intervalo de endereços da sua sub-rede. Isso deve ser diferente do intervalo de endereços de sub-rede usado pela rede virtual de sua instância gerenciada primária, como `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Valores de rede virtual secundária](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Esta etapa só será necessária se você estiver usando o portal do Azure para implantar sua instância gerenciada. Pule para a etapa 3 se você estiver usando o PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3-criar uma instância gerenciada secundária
Nesta etapa, você criará uma instância gerenciada secundária no portal do Azure, que também irá configurar a rede entre as duas instâncias gerenciadas. 

Sua segunda instância gerenciada deve:
- Ficar vazio. 
- Ter uma sub-rede e um intervalo de IPS diferentes da instância gerenciada primária. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

Crie a instância gerenciada secundária usando o portal do Azure. 

1. Selecione **SQL do Azure** no menu à esquerda da portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **selecionar opção de implantação do SQL** . Você pode exibir informações adicionais sobre os bancos de dados diferentes selecionando Mostrar detalhes no bloco bancos de dados.
1. Selecione **criar** no bloco **instâncias gerenciadas do SQL** . 

    ![Selecionar instância gerenciada](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na guia **noções básicas** da página **criar instância gerenciada do banco de dados SQL do Azure** , preencha os campos obrigatórios para configurar sua instância gerenciada secundária. 

   A tabela a seguir mostra os valores necessários para a instância gerenciada secundária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Subscrição** |  A assinatura na qual a instância gerenciada primária é. |
    | **Grupo de recursos**| O grupo de recursos em que a instância gerenciada primária é. |
    | **Nome da instância gerida** | O nome da sua nova instância gerenciada secundária, como `sql-mi-secondary`  | 
    | **Região**| O local da sua instância gerenciada secundária.  |
    | **Início de sessão de administração da instância gerida** | O logon que você deseja usar para a nova instância gerenciada secundária, como `azureuser`. |
    | **Palavra-passe** | Uma senha complexa que será usada pelo logon de administrador para a nova instância gerenciada secundária.  |
    | &nbsp; | &nbsp; |

1. Na guia **rede** , para a **rede virtual**, selecione a rede virtual que você criou para a instância gerenciada secundária na lista suspensa.

   ![Rede de MI secundária](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Na guia **configurações adicionais** , para **replicação geográfica**, escolha **Sim** para _usar como failover secundário_. Selecione a instância gerenciada primária na lista suspensa. 
    1. Certifique-se de que o agrupamento e o fuso horário correspondem ao da instância gerenciada primária. A instância gerenciada primária criada neste tutorial usou o padrão de agrupamento de `SQL_Latin1_General_CP1_CI_AS` e o fuso horário de `(UTC) Coordinated Universal Time`. 

   ![Rede de MI secundária](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Selecione **examinar + criar** para examinar as configurações de sua instância gerenciada secundária. 
1. Selecione **criar** para criar sua instância gerenciada secundária. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie a instância gerenciada secundária usando o PowerShell. 

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

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma tabela de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtém um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota a uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerenciada do banco de dados SQL do Azure.  |

---

## <a name="4---create-primary-gateway"></a>4-criar gateway primário 
Para que duas instâncias gerenciadas participem de um grupo de failover, deve haver um gateway configurado entre as redes virtuais das duas instâncias gerenciadas para permitir a comunicação de rede. Você pode criar o gateway para a instância gerenciada primária usando o portal do Azure. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Crie o gateway para a rede virtual da sua instância gerenciada primária usando o portal do Azure. 


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
    | **Nome** | O nome do seu gateway de rede virtual, como `primary-mi-gateway`. | 
    | **Região** | A região onde sua instância gerenciada secundária é. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo de VPN** | Selecionar **baseado em rota** |
    | **SKU**| Mantenha o padrão de `VpnGw1`. |
    | **Localização**| O local onde a instância gerenciada primária e a rede virtual primária é.   |
    | **Rede virtual**| Selecione a rede virtual que foi criada na seção 2, como `vnet-sql-mi-primary`. |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Insira um nome para seu endereço IP, como `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **revisar + criar** para examinar as configurações do seu gateway de rede virtual.

   ![Configurações do gateway primário](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecione **criar** para criar seu novo gateway de rede virtual. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway para a rede virtual da sua instância gerenciada primária usando o PowerShell. 

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

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração de IP para um gateway de rede virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um gateway de rede virtual |


---


## <a name="5---create-secondary-gateway"></a>5-criar gateway secundário 
Nesta etapa, crie o gateway para a rede virtual da sua instância gerenciada secundária usando o portal do Azure, 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Usando o portal do Azure, repita as etapas na seção anterior para criar a sub-rede de rede virtual e o gateway para a instância gerenciada secundária. Preencha os campos obrigatórios para configurar o gateway para sua instância gerenciada secundária. 

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

Crie o gateway para a rede virtual da instância gerenciada secundária usando o PowerShell. 

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

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração de IP para um gateway de rede virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um gateway de rede virtual |

---


## <a name="6---connect-the-gateways"></a>6-conectar os gateways
Nesta etapa, crie uma conexão bidirecional entre os dois gateways das duas redes virtuais. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Conecte os dois gateways usando o portal do Azure. 


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

Conecte os dois gateways usando o PowerShell. 

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

Esta parte do tutorial usa o seguinte cmdlet do PowerShell:

| Comando | Notas |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma conexão entre os dois gateways de rede virtual.   |

---


## <a name="7---create-a-failover-group"></a>7-criar um grupo de failover
Nesta etapa, você criará o grupo de failover e adicionará as duas instâncias gerenciadas a ele. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Crie o grupo de failover usando o portal do Azure. 


1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione a instância gerenciada primária que você criou na primeira seção, como `sql-mi-primary`. 
1. Em **configurações**, navegue até **instância grupos de failover** e, em seguida, escolha **Adicionar grupo** para abrir a página **grupo de failover de instância** . 

   ![Adicionar um grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na página **grupo de failover de instância** , digite o nome do grupo de failover, como `failovergrouptutorial` e escolha a instância gerenciada secundária, como `sql-mi-secondary` na lista suspensa. Selecione **criar** para criar seu grupo de failover. 

   ![Criar grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Depois que a implantação do grupo de failover for concluída, você será levado de volta à página **grupo de failover** . 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Crie o grupo de failover usando o PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Esta parte do tutorial usa o seguinte cmdlet do PowerShell:

| Comando | Notas |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de failover de instância gerenciada do banco de dados SQL do Azure.  |


---


## <a name="8---test-failover"></a>8-failover de teste
Nesta etapa, você falhará no grupo de failover para o servidor secundário e, em seguida, fará o failback usando o portal do Azure. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Failover de teste usando o portal do Azure. 


1. Navegue até sua instância gerenciada dentro do [portal do Azure](https://portal.azure.com) e selecione **grupos de failover de instância** em configurações. 
1. Examine qual instância gerenciada é a primária e qual instância gerenciada é a secundária. 
1. Selecione **failover** e, em seguida, selecione **Sim** no aviso sobre as sessões de TDS sendo desconectadas. 

   ![Fazer failover do grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Examine qual instância gerenciada é a primária e qual é a instância secundária. Se o failover for bem-sucedido, as duas instâncias deverão ter funções alternadas. 

   ![Instâncias gerenciadas têm funções alternadas após o failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Selecione **failover** novamente para falhar a instância primária de volta para a função primária. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Failover de teste usando o PowerShell. 

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


Reverter grupo de failover de volta para o servidor primário:

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

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista os grupos de failover de instância gerenciada.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa um failover de um grupo de failover de instância gerenciada. | 

---



## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos excluindo primeiro a instância gerenciada, depois o cluster virtual, todos os recursos restantes e, por fim, o grupo de recursos. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Navegue até o grupo de recursos na [portal do Azure](https://portal.azure.com). 
1. Selecione a instância gerenciada e, em seguida, selecione **excluir**. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. Esse processo pode levar algum tempo para ser concluído em segundo plano e até que ele seja concluído, você não poderá excluir o *cluster virtual* nem outros recursos dependentes. Monitore a exclusão na guia atividade para confirmar se a instância gerenciada foi excluída. 
1. Depois que a instância gerenciada for excluída, exclua o *cluster virtual* selecionando-o em seu grupo de recursos e, em seguida, escolhendo **excluir**. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. 
1. Exclua todos os recursos restantes. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. 
1. Exclua o grupo de recursos selecionando **excluir grupo de recursos**, digitando o nome do grupo de recursos, `myResourceGroup`e, em seguida, selecionando **excluir**. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Você precisará remover o grupo de recursos duas vezes. Remover o grupo de recursos na primeira vez removerá a instância gerenciada e os clusters virtuais, mas irá falhar com a mensagem de erro `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Execute o comando Remove-AzResourceGroup uma segunda vez para remover todos os recursos residuais, bem como o grupo de recursos.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Esta parte do tutorial usa o seguinte cmdlet do PowerShell:

| Comando | Notas |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. |

---

## <a name="full-script"></a>Script completo

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma tabela de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtém um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota a uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerenciada do banco de dados SQL do Azure.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Retorna informações sobre a instância do banco de dados gerenciado do Azure SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração de IP para um gateway de rede virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um gateway de rede virtual |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma conexão entre os dois gateways de rede virtual.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de failover de instância gerenciada do banco de dados SQL do Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista os grupos de failover de instância gerenciada.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa um failover de um grupo de failover de instância gerenciada. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. | 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

Não há nenhum script disponível para o portal do Azure.

---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configurou um grupo de failover entre duas instâncias gerenciadas. Aprendeu a:

> [!div class="checklist"]
> - Criar uma instância gerenciada primária
> - Crie uma instância gerenciada secundária como parte de um [grupo de failover](sql-database-auto-failover-group.md). 
> - Ativação pós-falha de teste

Avance para o próximo guia de início rápido sobre como se conectar à sua instância gerenciada e como restaurar um banco de dados para sua instância gerenciada: 

> [!div class="nextstepaction"]
> [Conectar-se à sua instância gerenciada](sql-database-managed-instance-configure-vm.md)
> [restaurar um banco de dados para uma instância gerenciada](sql-database-managed-instance-get-started-restore.md)


