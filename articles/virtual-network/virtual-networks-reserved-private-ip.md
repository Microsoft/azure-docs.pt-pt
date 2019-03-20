---
title: Estático interno clássico IP - VM do Azure - privado
description: Noções básicas sobre IPs internos estáticos (DIPs) e como geri-los
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b83a6e2c81eac9993c481561e3cebbed681d2c4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096049"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Como definir um endereço IP de privada interno estático com o PowerShell (clássico)
Na maioria dos casos, não tem de especificar um endereço IP estático interno para a máquina virtual. As VMs numa rede virtual irão receber automaticamente um endereço IP interno de um intervalo que especificou. Mas em certos casos, a especificação de um endereço IP estático para uma VM específica faz sentido. Por exemplo, se a VM irá executar o DNS ou vai ser um controlador de domínio. Um endereço IP estático interno permanece com a VM, mesmo através de um Estado de paragem/desaprovisionamento. 

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o [modelo de implementação do Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Instalar o módulo de gestão de serviço do Azure PowerShell

Antes de executar os comandos seguintes, certifique-se de que o [módulo de gestão de serviço do Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) está instalado na máquina. Para o histórico de versões do módulo de gestão de serviço do Azure PowerShell, consulte [módulo do Azure na galeria do PowerShell](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Como verificar se um endereço IP específico está disponível
Para verificar se o endereço IP *10.0.0.7* está disponível numa vnet com o nome *TestVnet*, execute o seguinte comando do PowerShell e verificar o valor das *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Se desejar testar o comando acima num ambiente seguro, siga as diretrizes [criar uma rede virtual (clássico)](virtual-networks-create-vnet-classic-pportal.md) para criar uma vnet com o nome *TestVnet* e certifique-se de que ele usa o *10.0.0.0/8*  espaço de endereços.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Como especificar um IP estático interno ao criar uma VM
O script do PowerShell abaixo cria um novo serviço cloud com o nome *TestService*, em seguida, obtém uma imagem do Azure, em seguida, cria uma VM com o nome *TestVM* no novo serviço de cloud com a imagem obtida, define o VM esteja numa sub-rede denominada *Subnet-1*e define *10.0.0.7* como um IP estático interno para a VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Como obter informações de IP internas estáticas para uma VM
Para ver as informações de IP estáticas internas para a VM criada com o script acima, execute o seguinte comando do PowerShell e observe os valores para *IpAddress*:

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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Como remover um IP estático interno a partir de uma VM
Para remover o IP estático interno, adicionado à VM no script acima, execute o seguinte comando do PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Como adicionar um IP interno estático a uma VM existente
Para adicionar um IP estático interno para a VM criada utilizando o script acima, execute o seguinte comando:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Passos Seguintes
[IP reservado](virtual-networks-reserved-public-ip.md)

[IP público de nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md)

[APIs REST do IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx)

