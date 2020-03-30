---
title: Configure endereços IP privados para VMs (Clássico) - Azure PowerShell / Microsoft Docs
description: Saiba como configurar endereços IP privados para máquinas virtuais (Clássicas) utilizando o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00a4f282da3a943516ffce584247ac20935526b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058712"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Configure endereços IP privados para uma máquina virtual (Clássica) usando powerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [gerir um endereço IP privado estático no modelo](virtual-networks-static-private-ip-arm-ps.md)de implementação do Gestor de Recursos.

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

A amostra que o PowerShell comanda abaixo espera um ambiente simples já criado. Se quiser executar os comandos tal como são apresentados neste documento, primeiro construa o ambiente de teste descrito no [Create a VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Como verificar se existe um endereço IP específico
Para verificar se o endereço IP *192.168.1.101* está disponível num VNet chamado *TestVNet,* execute o seguinte comando PowerShell e verifique o valor para *IsAvailable:*

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Resultado esperado:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar um VM
O script PowerShell abaixo cria um novo serviço em nuvem chamado *TestService,* depois recupera uma imagem do Azure, cria um VM chamado *DNS01* no novo serviço de nuvem usando a imagem recuperada, define o VM para estar numa subnet chamada *FrontEnd,* e define *192.168.1.7* como um endereço IP privado estático para o VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Resultado esperado:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações estáticas de endereço ip privado para um VM
Para visualizar as informações estáticas de endereço ip privado para o VM criado com o script acima, executar o seguinte comando PowerShell e observar os valores para *IpAddress:*

    Get-AzureVM -Name DNS01 -ServiceName TestService

Resultado esperado:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
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

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de um VM
Para remover o endereço IP privado estático adicionado ao VM no script acima, execute o seguinte comando PowerShell:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Resultado esperado:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a um VM existente
Para adicionar um endereço IP privado estático ao VM criado com o script acima, execute o seguinte comando:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Resultado esperado:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP dentro do sistema operativo

Recomenda-se que não atribua estáticamente o IP privado atribuído à máquina virtual Azure dentro do sistema operativo de um VM, a menos que seja necessário. Se definir manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que é o mesmo endereço do endereço IP privado atribuído ao VM Azure, ou pode perder a conectividade com a máquina virtual. Nunca deve atribuir manualmente o endereço IP público atribuído a uma máquina virtual Azure dentro do sistema operativo da máquina virtual.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre endereços [IP públicos reservados.](virtual-networks-reserved-public-ip.md)
* Conheça os endereços [IP (ILPIP) públicos de nível de instância.](virtual-networks-instance-level-public-ip.md)
* Consulte as [APIs DE REPOUSO IP Reservadas](https://msdn.microsoft.com/library/azure/dn722420.aspx).

