---
title: Gerir endereços IP reservados (clássico) do Azure | Documentos da Microsoft
description: Compreender os endereços IP reservados (clássico) e como gerenciá-los com o Azure PowerShell e CLI do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 543135db8df69db7e0e6182c9d52b9c956ee80b1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996974"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Endereços IP reservados (implementação clássica)

 Endereços IP no Azure abrangem duas categorias: reservado e dinâmica. Endereços IP públicos, geridos pelo Azure são dinâmicos por predefinição. Isso significa que o endereço IP utilizado para um serviço cloud (VIP) ou aceder a uma VM ou instância de função diretamente (ILPIP) pode alterar de tempos em tempos, quando os recursos são encerrar ou parados (desalocados).

Para impedir que os endereços IP a alteração, pode reservar um endereço IP. IPs reservados pode ser utilizado apenas como um VIP, garantindo que o endereço IP para o serviço em nuvem permanece a mesma, mesmo quando os recursos são encerrar ou parados (desalocados). Além disso, pode converter os IPs dinâmicos existente utilizado como um VIP para um endereço IP reservado.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Aprenda a reservar um estático público endereço IP com o [modelo de implementação do Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Para saber mais sobre endereços IP no Azure, leia os [endereços IP](virtual-network-ip-addresses-overview-classic.md) artigo.

## <a name="when-do-i-need-a-reserved-ip"></a>Quando é necessário um IP reservado?
* **Pretender certificar-se de que o IP está reservado na sua subscrição**. Se desejar reservar um endereço IP que não é liberado da sua subscrição, sob qualquer circunstância, deve utilizar um IP público reservado.  
* **Pretende que o seu IP para manter-se com o seu serviço cloud, até mesmo na parada ou desatribuída Estado (VMs)**. Se pretender que o seu serviço a ser acedidos através de um endereço IP que não é alterado, mesmo quando as VMs no serviço cloud são encerradas ou parado (desalocadas).
* **Pretender certificar-se de que o tráfego de saída do Azure utiliza um endereço IP previsível**. Pode ter a firewall no local configurada para permitir apenas tráfego de endereços IP específicos. Ao reservar um IP, sabe o endereço IP de origem e não precisa de atualizar as regras de firewall devido a uma alteração IP.

## <a name="faqs"></a>FAQs
- Pode utilizar um IP reservado para todos os serviços do Azure?
    Não. IPs reservados só pode ser utilizado para VMs e funções de instância de serviço de cloud expostas por meio de um VIP.
- O número de IPs reservados posso ter?
    Para obter detalhes, consulte a [limites do Azure](../azure-subscription-service-limits.md#networking-limits) artigo.
- Existe um custo para IPs reservados?
    Às vezes. Para detalhes de preços, consulte a [detalhes de preços de endereços de IP reservado](https://go.microsoft.com/fwlink/?LinkID=398482) página.
- Como posso reservar um endereço IP?
    Pode utilizar o PowerShell, o [API de REST de gestão do Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx), ou o [portal do Azure](https://portal.azure.com) para reservar um endereço IP numa região do Azure. Um endereço IP reservado é associado à sua subscrição.
- Pode utilizar um IP reservado com baseado no grupo de afinidade de VNets?
    Não. IPs reservados só são suportadas nas VNets em modo regional. IPs reservados não são suportadas para VNets que estão associadas a grupos de afinidade. Para obter mais informações sobre a associação de uma VNet com uma região ou o grupo de afinidades, consulte a [sobre VNets regionais e de grupos de afinidades](virtual-networks-migrate-to-regional-vnet.md) artigo.

## <a name="manage-reserved-vips"></a>Gerir VIPs reservados

### <a name="using-azure-powershell-classic"></a>Com o Azure PowerShell (clássico)

Antes de poder utilizar IPs reservados, deve adicioná-lo à sua subscrição. Criar um IP reservado do conjunto de endereços IP públicos disponíveis na *E.U.A. Central* localização da seguinte forma:

> [!NOTE]
> Para o modelo de implementação clássica, tem de instalar a versão de gestão de serviço do Azure PowerShell. Para obter mais informações, veja [Instalar o módulo Service Management do Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
No entanto, observe que não é possível especificar o que está sendo IP reservado. Para ver os endereços IP que estão reservados na sua subscrição, execute o seguinte comando do PowerShell e repare nos valores para *ReservedIPName* e *endereço*:

```powershell
Get-AzureReservedIP
```

Resultado esperado:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Quando cria um endereço IP reservado com o PowerShell, não é possível especificar um grupo de recursos para criar o IP reservado no. Locais do Azure, num grupo de recursos denominado *sistema de rede padrão* automaticamente. Se criar o IP reservado usando o [portal do Azure](https://portal.azure.com), pode especificar qualquer grupo de recursos que escolher. Se criar o IP reservado num grupo de recursos diferente de *predefinição-redes* no entanto, sempre que referencia o IP reservado com comandos como `Get-AzureReservedIP` e `Remove-AzureReservedIP`, tem de referenciar o nome  *Reservado nome de grupo de recursos-ip-nome do grupo*.  Por exemplo, se criar um IP reservado com o nome *myReservedIP* num grupo de recursos com o nome *myResourceGroup*, tem de referenciar o nome do IP reservado como *grupo myResourceGroup myReservedIP*.   


Depois de um IP é reservado, continua a ser associado à sua subscrição até os eliminar. Elimina um IP reservado da seguinte forma:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Com a CLI do Azure (clássico)
Criar um IP reservado do conjunto de endereços IP públicos disponíveis na *E.U.A. Central* localização como utilizar o Azure CLI clássica segue:

> [!NOTE]
> Para a implementação clássica, tem de utilizar a CLI clássica do Azure. Para obter informações sobre como instalar a CLI clássica do Azure, consulte [instalar a CLI clássica do Azure](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Comando:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Exemplo:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Pode ver que endereços IP estão reservados na sua subscrição com a CLI do Azure da seguinte forma: 

Comando:
```azurecli
azure network reserved-ip list
```
Depois de um IP é reservado, continua a ser associado à sua subscrição até os eliminar. Elimina um IP reservado da seguinte forma:

Comando:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Exemplo:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Reservar o endereço IP de um serviço cloud existente
Pode reservar o endereço IP de um serviço cloud existente ao adicionar o `-ServiceName` parâmetro. Reservar o endereço IP de um serviço cloud *TestService* no *centro dos E.U.A.* localização da seguinte forma:

- Com o Azure PowerShell (clássico):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- CLI do Azure (clássico):
  
    Comando:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Exemplo:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associar um IP reservado para um novo serviço cloud
O script seguinte cria um IP reservado novo, em seguida, associa-a para um novo serviço cloud com o nome *TestService*.

### <a name="using-azure-powershell-classic"></a>Com o Azure PowerShell (clássico)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Quando cria um IP reservado para utilizar com um serviço em nuvem, ainda se referem à VM através da utilização *VIP:&lt;número de porta >* para comunicação de entrada. Reservar um IP não significa que pode ligar diretamente à VM. O IP reservado é atribuído ao serviço cloud que a VM foi implementada. Se pretender ligar diretamente a uma VM pelo IP, tem de configurar um IP público ao nível da instância. Um IP público ao nível da instância é um tipo de IP público (chamado de um ILPIP) que é atribuído diretamente à sua VM. Não pode ser reservado. Para obter mais informações, leia os [IP público ao nível da instância (ILPIP)](virtual-networks-instance-level-public-ip.md) artigo.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Remover um IP reservado da implementação em execução

Remova um IP reservado adicionado um novo serviço cloud, da seguinte forma: 
### <a name="using-azure-powershell-classic"></a>Com o Azure PowerShell (clássico)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Com a CLI do Azure (clássico)
Comando:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Exemplo:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Remover um IP reservado da implementação em execução não remove a reserva da sua subscrição. Ele simplesmente libera o IP para ser utilizado por outro recurso na sua subscrição.
> 

Para remover completamente um IP reservado a partir de uma subscrição, execute o seguinte comando:

Comando:

```azurecli
azure network reserved-ip delete <name>
```
Exemplo:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associar um IP reservado para implementação em execução

### <a name="using-azure-powershell-classic"></a>Com o Azure PowerShell (clássico)

Os comandos seguintes criam um serviço em nuvem com o nome *TestService2* com uma nova VM com o nome *TestVM2*. A atual reservado com o nome de IP *MyReservedIP* é, em seguida, associados ao serviço cloud.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Com a CLI do Azure (clássico)
Pode associar um novo IP reservado para a implementação do serviço cloud em execução com a CLI do Azure da seguinte forma:

Comando:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Exemplo:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associar um ip reservado a um serviço cloud utilizando um ficheiro de configuração de serviço
Também pode associar um IP reservado a um serviço cloud utilizando um ficheiro de configuração (. CSCFG) do serviço. O xml de exemplo seguinte mostra como configurar um serviço em nuvem para utilizar um VIP reservado com o nome *MyReservedIP*:
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>Passos Seguintes
* Compreender como [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implementação clássica.
* Saiba mais sobre [reservado de endereços IP privados](virtual-networks-reserved-private-ip.md).
* Saiba mais sobre [endereços IP de público ao nível do instância (ILPIP)](virtual-networks-instance-level-public-ip.md).

