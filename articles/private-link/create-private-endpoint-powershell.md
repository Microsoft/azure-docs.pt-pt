---
title: Quickstart - Criar um Azure Private Endpoint usando Azure PowerShell
description: Utilize este quickstart para aprender a criar um Ponto Final Privado utilizando a Azure PowerShell.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: afd1f8b6c80cfd7aa8d5142841458d76511a9e96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562916"
---
# <a name="quickstart-create-an-azure-private-endpoint-using-azure-powershell"></a>Quickstart: Criar um Azure Private Endpoint usando Azure PowerShell

Começa com o Azure Private Link utilizando um Private Endpoint para ligar de forma segura a uma aplicação web Azure.

Neste arranque rápido, você vai criar um ponto final privado para uma aplicação web Azure e implementar uma máquina virtual para testar a ligação privada.  

Os pontos finais privados podem ser criados para diferentes tipos de serviços Azure, tais como Azure SQL e Azure Storage.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma App Web Azure com um plano de serviço de aplicações **premiumV2** ou superior implementado na sua subscrição Azure.  
    * Para obter mais informações e um exemplo, consulte [Quickstart: Crie uma aplicação web Core ASP.NET em Azure](../app-service/quickstart-dotnetcore.md). 
    * Para um tutorial detalhado sobre a criação de uma aplicação web e um ponto final, consulte [Tutorial: Conecte-se a uma aplicação web utilizando um Azure Private Endpoint](tutorial-private-endpoint-webapp-portal.md).

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [o New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
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
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, irá criar o ponto final e a ligação privados utilizando:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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

3. Selecione **CreatePrivateEndpointQs-rg**.

4. Selecione **myVM**.

5. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

6. Selecione o botão **azul Use Bastion.**

7. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

8. Abra o Windows PowerShell no servidor depois de ligar.

9. Introduza `nslookup <your-webapp-name>.azurewebsites.net`. **\<your-webapp-name>** Substitua-o pelo nome da aplicação web que criou nos passos anteriores.  Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Um endereço IP privado de **10.0.0.5** é devolvido para o nome da aplicação web.  Este endereço encontra-se na sub-rede da rede virtual que criou anteriormente.

10. Na ligação de bastião ao **myVM,** abra o Internet Explorer.

11. Introduza o url da sua aplicação web, **https:// \<your-webapp-name> .azurewebsites.net**.

12. Receberá a página de aplicações web padrão se a sua aplicação não tiver sido implementada:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Página de aplicativo web padrão." border="true":::

13. Feche a ligação ao **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando terminar de usar o ponto final privado e o VM, utilize o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:

* Rede virtual e hospedeiro de bastião.
* Máquina virtual.
* Ponto final privado para uma App Web Azure.

Usou a máquina virtual para testar a conectividade de forma segura para a aplicação web em todo o ponto final privado.

Para obter mais informações sobre os serviços que suportam um ponto final privado, consulte:
> [!div class="nextstepaction"]
> [Disponibilidade de Ligação Privada](private-link-overview.md#availability)
