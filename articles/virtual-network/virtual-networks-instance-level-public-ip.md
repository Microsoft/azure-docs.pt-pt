---
title: Endereços de IP públicos (clássico) de nível de instância do Azure | Documentos da Microsoft
description: Compreender a instância de endereços IP públicos ao nível (ILPIP) e como geri-los com o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: 2f6db23e02c836dea6d640757d12275b654ad468
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60186808"
---
# <a name="instance-level-public-ip-classic-overview"></a>Instância pública IP (clássico) Descrição geral de nível
Uma instância IP público ao nível (ILPIP) é um endereço IP público que pode atribuir diretamente para uma instância de função VM ou serviços Cloud, em vez de para o serviço em nuvem que sua instância VM ou função residem em. Um ILPIP não tomar o lugar do IP virtual (VIP) atribuído ao seu serviço cloud. Em vez disso, é um endereço IP adicional que pode utilizar para ligar diretamente à sua instância VM ou função.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda a criação de VMs através do Resource Manager. Certifique-se de que compreende como [endereços IP](virtual-network-ip-addresses-overview-classic.md) profissional no Azure.

![Diferença entre ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Conforme mostrado na figura 1, o serviço em nuvem é acessado com um VIP, enquanto as VMs individuais são normalmente acedidas VIP a utilizar:&lt;número de porta&gt;. Ao atribuir um ILPIP para uma VM específica, essa VM pode ser acedida diretamente com esse endereço IP.

Quando criar um serviço cloud no Azure, correspondentes registos DNS são criados automaticamente para permitir o acesso ao serviço através de um nome de domínio completamente qualificado (FQDN), em vez de usar o VIP real. O mesmo processo acontece para um ILPIP, permitindo o acesso para a instância de função de VM ou através do FQDN, em vez do ILPIP. Por exemplo, se criar um serviço em nuvem com o nome *contosoadservice*, e configurar uma função da web com o nome *contosoweb* com duas instâncias e em. cscfg `domainNameLabel` está definido como  *WebPublicIP*, o Azure a uma seguintes regista para as instâncias de registros:


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> Pode atribuir apenas um ILPIP para cada instância VM ou função. Pode usar até 5 ILPIPs por subscrição. ILPIPs não são suportados para VMs de multi-NIC.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Por que motivo posso pedir um ILPIP?
Se quiser ser capaz de ligar à sua instância de função de VM ou um endereço IP atribuído diretamente a ele, em vez de utilizar a cloud service VIP:&lt;número de porta&gt;, pedir um ILPIP para a VM ou a sua instância de função.

* **Active Directory FTP** -ao atribuir um ILPIP a uma VM, pode receber tráfego de qualquer porta. Pontos finais não são necessários para a VM receber o tráfego.  Ver [descrição geral do protocolo FTP](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) para obter detalhes sobre o protocolo FTP.
* **IP de saída** - o tráfego de saída da VM de origem é mapeado para o ILPIP como a origem e o ILPIP identifica exclusivamente a VM a entidades externas.

> [!NOTE]
> No passado, um endereço ILPIP foi referido como um endereço IP público (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Gerir um ILPIP para uma VM
As seguintes tarefas permitem-lhe criar, atribuir e remover ILPIPs de VMs:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Como solicitar um ILPIP durante a criação de VM com o PowerShell
O seguinte script do PowerShell cria um serviço em nuvem com o nome *FTPService*, obtém uma imagem do Azure, que cria uma VM com o nome *FTPInstance* com a imagem obtida, define a VM para utilizar um ILPIP e adiciona a VM para o novo serviço:

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
Se pretender especificar outra conta de armazenamento como a localização do novo disco VM, pode utilizar **MediaLocation** parâmetro:

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Como obter informações de ILPIP para uma VM
Para ver as informações de ILPIP para a VM criada com o script anterior, execute o seguinte comando do PowerShell e observe os valores para *PublicIPAddress* e *PublicIPName*:

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

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Como remover um ILPIP a partir de uma VM
Para remover o ILPIP adicionado à VM no script anterior, execute o seguinte comando do PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Como adicionar um ILPIP a uma VM existente
Para adicionar um ILPIP para a VM criada utilizando o script anterior, execute o seguinte comando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Gerir um ILPIP para uma instância de função de serviços Cloud

Para adicionar um ILPIP para uma instância de função de serviços Cloud, execute os seguintes passos:

1. Transfira o ficheiro. cscfg para o serviço em nuvem, concluindo os passos a [como configurar os serviços Cloud](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artigo.
2. Atualize o ficheiro. cscfg ao adicionar o `InstanceAddress` elemento. O exemplo seguinte adiciona um ILPIP com o nome *MyPublicIP* para uma instância de função chamada *WebRole1*: 

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
3. Carregar o ficheiro. cscfg para o serviço em nuvem, concluindo os passos a [como configurar os serviços Cloud](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artigo.

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Como obter informações de ILPIP para um serviço Cloud
Para ver as informações de ILPIP por instância de função, execute o seguinte comando do PowerShell e observe os valores para *PublicIPAddress*, *PublicIPName*, *PublicIPDomainNameLabel* e *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Também pode usar `nslookup` para consultar o subdomínio de um registo:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Passos Seguintes
* Compreender como [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implementação clássica.
* Saiba mais sobre [reservado IPs](virtual-networks-reserved-public-ip.md).
