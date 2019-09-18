---
title: Endereços IP públicos (clássicos) de nível de instância do Azure | Microsoft Docs
description: Entenda os endereços ILPIP (IP público em nível de instância) e como gerenciá-los usando o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: d92832d1eee995e8883dc6c8ed0f58c9755e40f8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058411"
---
# <a name="instance-level-public-ip-classic-overview"></a>Visão geral do IP público em nível de instância (clássico)
Um ILPIP (IP público em nível de instância) é um endereço IP público que você pode atribuir diretamente a uma instância de função de serviços de nuvem ou VM, em vez de ao serviço de nuvem em que a sua VM ou instância de função reside. Um ILPIP não assume o lugar do VIP (IP virtual) atribuído ao seu serviço de nuvem. Em vez disso, é um endereço IP adicional que você pode usar para se conectar diretamente à sua VM ou instância de função.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda a criação de VMs por meio do Resource Manager. Verifique se você entendeu como os [endereços IP](virtual-network-ip-addresses-overview-classic.md) funcionam no Azure.

![Diferença entre ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Como mostra a Figura 1, o serviço de nuvem é acessado usando um VIP, enquanto as VMs individuais normalmente são acessadas&gt;usando VIP:&lt;número da porta. Ao atribuir um ILPIP a uma VM específica, essa VM pode ser acessada diretamente usando esse endereço IP.

Quando você cria um serviço de nuvem no Azure, registros DNS A correspondentes são criados automaticamente para permitir o acesso ao serviço por meio de um FQDN (nome de domínio totalmente qualificado), em vez de usar o VIP real. O mesmo processo ocorre para um ILPIP, permitindo o acesso à VM ou à instância de função pelo FQDN em vez de ILPIP. Por exemplo, se você criar um serviço de nuvem chamado *contosoadservice*e configurar uma função Web chamada *contosoweb* com duas instâncias e no. cscfg `domainNameLabel` for definido como *WebPublicIP*, o Azure registrará os seguintes registros a para o ocasiões


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> Você pode atribuir apenas um ILPIP para cada VM ou instância de função. Você pode usar até 5 ILPIPs por assinatura. Não há suporte para ILPIPs para VMs com várias NICs.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Por que eu solicitaria um ILPIP?
Se você quiser ser capaz de se conectar à sua VM ou instância de função por um endereço IP atribuído diretamente a ela, em vez de usar o número&lt;&gt;de porta vip do serviço de nuvem, solicite um ILPIP para sua VM ou sua instância de função.

* **FTP ativo** -ao atribuir um ILPIP a uma VM, ele pode receber tráfego em qualquer porta. Os pontos de extremidade não são necessários para a VM receber o tráfego.  Consulte [visão geral do protocolo FTP](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) para obter detalhes sobre o protocolo FTP.
* **IP de saída** -o tráfego de saída proveniente da VM é mapeado para o ILPIP como a origem e o ILPIP identifica exclusivamente a VM para entidades externas.

> [!NOTE]
> No passado, um endereço ILPIP era chamado de endereço IP público (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Gerenciar um ILPIP para uma VM
As tarefas a seguir permitem criar, atribuir e remover ILPIPs de VMs:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Como solicitar um ILPIP durante a criação da VM usando o PowerShell
O script do PowerShell a seguir cria um serviço de nuvem chamado *FTPService*, recupera uma imagem do Azure, cria uma VM denominada *FTPInstance* usando a imagem recuperada, define a VM para usar um ILPIP e adiciona a VM ao novo serviço:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
Se desejar especificar outra conta de armazenamento como o local do novo disco de VM, você poderá usar o parâmetro **MediaLocation** :

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Como recuperar informações de ILPIP para uma VM
Para exibir as informações de ILPIP para a VM criada com o script anterior, execute o seguinte comando do PowerShell e observe os valores de *PublicIPAddress* e *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Resultado esperado:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Como remover um ILPIP de uma VM
Para remover o ILPIP adicionado à VM no script anterior, execute o seguinte comando do PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Como adicionar um ILPIP a uma VM existente
Para adicionar um ILPIP à VM criada usando o script anterior, execute o seguinte comando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Gerenciar um ILPIP para uma instância de função dos serviços de nuvem

Para adicionar um ILPIP a uma instância de função de serviços de nuvem, conclua as seguintes etapas:

1. Baixe o arquivo. cscfg para o serviço de nuvem concluindo as etapas no artigo [como configurar os serviços de nuvem](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) .
2. Atualize o arquivo. cscfg adicionando o `InstanceAddress` elemento. O exemplo a seguir adiciona um ILPIP chamado *MyPublicIP* a uma instância de função chamada *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Carregue o arquivo. cscfg para o serviço de nuvem concluindo as etapas no artigo [como configurar os serviços de nuvem](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) .

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Como recuperar informações de ILPIP para um serviço de nuvem
Para exibir as informações de ILPIP por instância de função, execute o seguinte comando do PowerShell e observe os valores de *PublicIPAddress*, *PublicIPName*, *PublicIPDomainNameLabel* e *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Você também pode usar `nslookup` para consultar o registro a de um subdomínio:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Passos Seguintes
* Entenda como o [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implantação clássico.
* Saiba mais sobre [IPS reservados](virtual-networks-reserved-public-ip.md).
