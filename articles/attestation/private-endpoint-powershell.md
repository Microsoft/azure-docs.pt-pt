---
title: Criar um ponto final privado usando a Azure PowerShell
description: Crie um ponto final privado para a Azure Attestation usando a Azure PowerShell
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 92080f119567d185ca6a5bccf0e89d8d10213f3b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732314"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>Quickstart: Criar um ponto final privado utilizando a Azure PowerShell

Inicie-se com a Azure Private Link utilizando um ponto final privado para ligar de forma segura ao Azure Attestation.

Neste arranque rápido, você vai criar um ponto final privado para a Azure Attestation e implementar uma máquina virtual para testar a ligação privada.  

> [!NOTE]
> A implementação atual inclui apenas a opção de aprovação automática. A subscrição deve ser listada em branco para poder prosseguir com a criação de pontos finais privados. Contacte a equipa de serviço ou envie um pedido de apoio ao Azure na página de apoio do [Azure](https://azure.microsoft.com/support/options/) antes de prosseguir com os passos abaixo.

## <a name="prerequisites"></a>Pré-requisitos

* Saiba mais sobre [a Azure Private Link](../private-link/private-link-overview.md)
* [Configurar a Azure Attestation com a Azure PowerShell](./quickstart-powershell.md)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [o New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
## Create to your Azure account subscription and create a resource group in a desired location. ##
Connect-AzAccount
Set-AzSubscription “mySubscription”
$rg = “CreateAttestationPrivateLinkTutorial-rg”
$loc= "eastus”
New-AzResourceGroup -Name $rg -Location $loc
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
$vnet = New-AzVirtualNetwork -Name "myAttestationTutorialVNet" -ResourceGroupName $rg -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnetConfig, $bastsubnetConfig

## Create public IP address for bastion host. ##
$publicip = New-AzPublicIpAddress -Name "myBastionIP" -ResourceGroupName $rg -Location $loc -Sku "Standard" -AllocationMethod "Static"

## Create bastion host ##
New-AzBastion -ResourceGroupName $rg -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
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

## Command to create network interface for VM ##
$nicVM = New-AzNetworkInterface -Name "myNicVM" -ResourceGroupName $rg -Location $loc -Subnet $vnet.Subnets[0] 

## Create a virtual machine configuration.##
$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2" | Set-AzVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | Set-AzVMSourceImage -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2019-Datacenter" -Version "latest" | Add-AzVMNetworkInterface -Id $nicVM.Id 

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

## <a name="create-an-attestation-provider"></a>Criar um fornecedor de atestado

```azurepowershell-interactive
## Create an attestation provider ##
$attestationProviderName = "myattestationprovider"
$attestationProvider = New-AzAttestation -Name $attestationProviderName -ResourceGroupName $rg -Location $loc
$attestationProviderId = $attestationProvider.Id

## Access the attestation provider from local machine ##
Enter nslookup <provider-name>.attest.azure.net. Replace <provider-name> with the name of the attestation provider instance you created in the previous steps. 

You'll receive a message similar to what is displayed below:

## PowerShell copy. ##
nslookup myattestationprovider.eus.attest.azure.net

Server:  cdns01.comcast.net
Address:  2001:558:feed::1

Non-authoritative answer:
Name:    eus.service.attest.azure.net
Address:  20.62.219.160
Aliases:  myattestationprovider.eus.attest.azure.net
    attesteusatm.trafficmanager.net
```

## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, irá criar o ponto final e a ligação privados utilizando:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Create private endpoint connection. ##
$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" -PrivateLinkServiceId $attestationProviderId -GroupID "Standard"

## Disable private endpoint network policy ##
 $vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled" 
$vnet | Set-AzVirtualNetwork

## Create private endpoint
New-AzPrivateEndpoint  -ResourceGroupName $rg -Name "myPrivateEndpoint" -Location $loc -Subnet $vnet.Subnets[0] -PrivateLinkServiceConnection $privateEndpointConnection
```
## <a name="configure-the-private-dns-zone"></a>Configure a zona privada do DNS

Nesta secção irá criar e configurar a zona privada de DNS utilizando:

* [Zona de New-AzPrivateDns](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Create private dns zone. ##
$zone = New-AzPrivateDnsZone -ResourceGroupName $rg -Name "privatelink.attest.azure.net"

## Create dns network link. ##
$link = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rg -ZoneName "privatelink.attest.azure.net" -Name "myLink" -VirtualNetworkId $vnet.Id

## Create DNS configuration ##
$config = New-AzPrivateDnsZoneConfig -Name "privatelink.attest.azure.net" -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group. ##
New-AzPrivateDnsZoneGroup -ResourceGroupName $rg -PrivateEndpointName "myPrivateEndpoint" -Name "myZoneGroup" -PrivateDnsZoneConfig $config
```

## <a name="test-connectivity-to-private-endpoint"></a>Testar conectividade com o ponto final privado

Nesta secção, utilizará a máquina virtual que criou no passo anterior para ligar ao servidor SQL através do ponto final privado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) 
 
2. Selecione **grupos** de recursos no painel de navegação à esquerda.

3. **Selecione CreateAttestationPrivateLinkTutorial-rg**.

4. Selecione **myVM**.

5. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

6. Selecione o botão **azul Use Bastion.**

7. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

8. Abra o Windows PowerShell no servidor depois de ligar.

9. Introduza `nslookup <provider-name>.attest.azure.net`. **\<provider-name>** Substitua-o pelo nome da instância do provedor de atestado que criou nos passos anteriores. Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell

    ## Access the attestation provider from local machine ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  cdns01.comcast.net
    Address:  2001:558:feed::1

    cdns01.comcast.net can't find myattestationprovider.eus.attest.azure.net: Non-existent domain

    ## Access the attestation provider from the VM created in the same virtual network as the private endpoint.   ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myattestationprovider.eastus.test.attest.azure.net
    ```
