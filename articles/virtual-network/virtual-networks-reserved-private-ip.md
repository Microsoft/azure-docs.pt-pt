---
title: IP privado interno estático-VM do Azure-clássico
description: Noções básicas sobre IPs internos estáticos (DIPs) e como gerenciá-los
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a13a0a54e9ded48cc5848843f4c329b2dea90f65
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975222"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Como definir um endereço IP privado estático interno usando o PowerShell (clássico)
Na maioria dos casos, você não precisará especificar um endereço IP interno estático para sua máquina virtual. As VMs em uma rede virtual receberão automaticamente um endereço IP interno de um intervalo que você especificar. Mas, em determinados casos, a especificação de um endereço IP estático para uma VM específica faz sentido. Por exemplo, se sua VM vai executar o DNS ou será um controlador de domínio. Um endereço IP interno estático permanece com a VM mesmo por meio de um estado de interrupção/desprovisionamento. 

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../azure-resource-manager/management/deployment-models.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que a maioria das implantações novas usem o [modelo de implantação do Gerenciador de recursos](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Instalar o módulo de gerenciamento de serviços Azure PowerShell

Antes de executar os comandos a seguir, verifique se o [módulo de gerenciamento de serviços Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) está instalado no computador. Para obter o histórico de versão do módulo de gerenciamento de serviços Azure PowerShell, consulte o [módulo do Azure no Galeria do PowerShell](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Como verificar se um endereço IP específico está disponível
Para verificar se o endereço IP *10.0.0.7* está disponível em uma vnet denominada *TestVnet*, execute o seguinte comando do PowerShell e verifique o valor de *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Se você quiser testar o comando acima em um ambiente seguro, siga as diretrizes em [criar uma rede virtual (clássica)](virtual-networks-create-vnet-classic-pportal.md) para criar uma vnet chamada *TestVnet* e verifique se ela usa o espaço de endereço *10.0.0.0/8* .
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Como especificar um IP interno estático ao criar uma VM
O script do PowerShell abaixo cria um novo serviço de nuvem chamado *TestService*, em seguida, recupera uma imagem do Azure e, em seguida, cria uma VM denominada *TestVM* no novo serviço de nuvem usando a imagem recuperada, define a VM como em uma sub-rede chamada *Subnet-1*e define *10.0.0.7* como um IP interno estático para a VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Como recuperar informações de IP interno estático para uma VM
Para exibir as informações de IP interno estático para a VM criada com o script acima, execute o seguinte comando do PowerShell e observe os valores para *IPAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Como remover um IP interno estático de uma VM
Para remover o IP interno estático adicionado à VM no script acima, execute o seguinte comando do PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Como adicionar um IP interno estático a uma VM existente
Para adicionar um IP interno estático à VM criada usando o script acima, execute o seguinte comando:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Passos seguintes
[IP Reservado](virtual-networks-reserved-public-ip.md)

[IP público em nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md)

[IP Reservado APIs REST](https://msdn.microsoft.com/library/azure/dn722420.aspx)

