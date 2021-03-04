---
title: Tutorial - Conecte-se a um servidor Azure SQL utilizando um Azure Private Endpoint - PowerShell
description: Utilize este tutorial para aprender a criar um servidor Azure SQL com um ponto final privado usando a Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 4999d8b5d5ebe5d44aae782589b5c63324a4027b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096518"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-powershell"></a>Tutorial - Conecte-se a um servidor Azure SQL utilizando um Azure Private Endpoint - Azure PowerShell

O ponto final privado Azure é o bloco de construção fundamental para private link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem com os recursos de Private Link em privado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma rede virtual e um hospedeiro de bastião.
> * Cria uma máquina virtual.
> * Crie um servidor Azure SQL e um ponto final privado.
> * Teste a conectividade com o ponto final privado do servidor SQL.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [o New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateSQLEndpointTutorial-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e hospedeiro de bastião

Nesta secção, você vai criar uma rede virtual, sub-rede e hospedeiro de bastião. 

O hospedeiro de bastião será utilizado para ligar de forma segura à máquina virtual para testar o ponto final privado.

Crie uma rede virtual e um hospedeiro de bastião com:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [Nova AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

Pode levar alguns minutos para o anfitrião do Bastião Azure ser implantado.

## <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Nesta secção, irá criar uma máquina virtual que será usada para testar o ponto final privado.

Crie a máquina virtual com:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [Novo AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Sistema operativo Set-AzVM](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreateSQLEndpointTutorial-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-an-azure-sql-server"></a>Criar um servidor Azure SQL

Nesta secção, irá criar um servidor SQL e uma base de dados utilizando:

* [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)
* [New-AzSQlDatabase](/powershell/module/az.sql/new-azsqldatabase)

Crie servidor SQL e base de dados. **\<sql-server-name>** Substitua-o pelo nome exclusivo do servidor:

```azurepowershell-interactive
## Set and administrator and password for the SQL server. ##
$cred = Get-Credential

## Create SQL server ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    SqlAdministratorCredentials = $cred
    Location = 'eastus'
}
New-AzSqlServer @parameters1

## Create database. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    DatabaseName = 'mysqldatabase'
    RequestedServiceObjectiveName = 'S0'
    SampleName = 'AdventureWorksLT'
}
New-AzSqlDatabase @parameters2
```

## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, irá criar o ponto final e a ligação privados utilizando:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place SQL server into variable. Replace <sql-server-name> with your server name ##
$server = Get-AzSqlServer -ResourceGroupName CreateSQLEndpointTutorial-rg -ServerName <sql-server-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $server.ResourceID
    GroupID = 'sqlserver'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>Configure a zona privada do DNS

Nesta secção irá criar e configurar a zona privada de DNS utilizando:

* [Zona de New-AzPrivateDns](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'privatelink.database.windows.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ZoneName = 'privatelink.database.windows.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.database.windows.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>Testar conectividade com o ponto final privado

Nesta secção, utilizará a máquina virtual que criou no passo anterior para ligar ao servidor SQL através do ponto final privado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) 
 
2. Selecione **grupos** de recursos no painel de navegação à esquerda.

3. Selecione **CreateSQLEndpointTutorial-rg**.

4. Selecione **myVM**.

5. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

6. Selecione o botão **azul Use Bastion.**

7. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

8. Abra o Windows PowerShell no servidor depois de ligar.

9. Introduza `nslookup <sqlserver-name>.database.windows.net`. **\<sqlserver-name>** Substitua-o pelo nome do servidor SQL que criou nos passos anteriores.  Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Um endereço IP privado de **10.0.0.5** é devolvido para o nome do servidor SQL.  Este endereço encontra-se na sub-rede da rede virtual que criou anteriormente.


10. Instale [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) no **myVM**.

11. Open **SQL Server Management Studio**.

12. Em **Ligar ao servidor,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor | Selecione **Motor de Base de Dados**.|
    | Nome do servidor | Insira **\<sql-server-name> .database.windows.net** |
    | Autenticação | Selecione **SQL Server Authentication** (Autenticação do SQL Server). |
    | Nome de utilizador | Introduza o nome de utilizador que inseriu durante a criação do servidor |
    | Palavra-passe | Introduza a palavra-passe que introduziu durante a criação do servidor |
    | Memorizar palavra-passe | Selecione **Yes** (Sim). |

13. Selecione **Ligar**.

14. Procure bases de dados a partir do menu esquerdo.

15. (Opcionalmente) Criar ou consultar informações a partir da **mysqldatabase**.

16. Feche a ligação do bastião ao **myVM**. 

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando terminar de usar o ponto final privado, o servidor SQL e o VM, elimine o grupo de recursos e todos os recursos que contém: 

1. Introduza **CreateSQLEndpointTutorial-rg** na caixa **de pesquisa** no topo do portal e selecione **CreateSQLEndpointTutorial-rg** a partir dos resultados da pesquisa. 

2. Selecione **Eliminar grupo de recursos**. 

3. Introduza **CreateSQLEndpointTutorial-rg** para **TYPE THE RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um:

* Rede virtual e hospedeiro de bastião.
* Máquina virtual.
* Servidor Azure SQL com ponto final privado.

Utilizou a máquina virtual para testar a conectividade de forma segura para o servidor SQL através do ponto final privado.

Como próximo passo, poderá também estar interessado na aplicação Web com conectividade privada ao cenário de arquitetura [de base de dados Azure SQL,](https://docs.microsoft.com/azure/architecture/example-scenario/private-web-app/private-web-app) que liga uma aplicação web fora da rede virtual ao ponto final privado de uma base de dados.

Saiba como criar um serviço de Ligação Privada:
> [!div class="nextstepaction"]
> [Criar um serviço de Ligação Privada](create-private-link-service-portal.md)