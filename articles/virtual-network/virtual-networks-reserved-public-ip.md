---
title: Gerenciar endereços IP reservados do Azure (clássico) | Microsoft Docs
description: Entenda os endereços IP reservados (clássico) e como gerenciá-los usando Azure PowerShell e CLI do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 675e7a7b557a3f19ea4d8d4960316c3859cbb9c1
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058482"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Endereços de IP Reservado (implantação clássica)

 Os endereços IP no Azure se enquadram em duas categorias: dinâmica e reservada. Os endereços IP públicos gerenciados pelo Azure são dinâmicos por padrão. Isso significa que o endereço IP usado para um determinado serviço de nuvem (VIP) ou para acessar uma VM ou instância de função diretamente (ILPIP) pode mudar de tempos em tempos, quando os recursos são desligados ou interrompidos (desalocados).

Para impedir que os endereços IP sejam alterados, você pode reservar um endereço IP. Os IPs reservados podem ser usados somente como um VIP, garantindo que o endereço IP do serviço de nuvem permaneça o mesmo, mesmo que os recursos sejam desligados ou interrompidos (desalocados). Além disso, você pode converter IPs dinâmicos existentes usados como um VIP para um endereço IP reservado.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como reservar um endereço IP público estático usando o [modelo de implantação do Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Para saber mais sobre endereços IP no Azure, leia o artigo [endereços IP](virtual-network-ip-addresses-overview-classic.md) .

## <a name="when-do-i-need-a-reserved-ip"></a>Quando preciso de um IP reservado?
* **Você deseja garantir que o IP seja reservado em sua assinatura**. Se você quiser reservar um endereço IP que não seja liberado da sua assinatura em nenhuma circunstância, use um IP público reservado.  
* **Você deseja que seu IP permaneça com seu serviço de nuvem mesmo entre as VMs (estado parado ou desalocado)** . Se você quiser que seu serviço seja acessado usando um endereço IP que não é alterado, mesmo quando as VMs no serviço de nuvem são desligadas ou interrompidas (desalocadas).
* **Você deseja garantir que o tráfego de saída do Azure use um endereço IP previsível**. Você pode ter o firewall local configurado para permitir somente o tráfego de endereços IP específicos. Ao reservar um IP, você sabe o endereço IP de origem e não precisa atualizar suas regras de firewall devido a uma alteração de IP.

## <a name="faqs"></a>FAQs
- Posso usar um IP reservado para todos os serviços do Azure?
    Não. IPs reservados só podem ser usados para VMs e funções de instância de serviço de nuvem expostas por meio de um VIP.
- Quantos IPs reservados posso ter?
    Para obter detalhes, consulte o artigo [limites do Azure](../azure-subscription-service-limits.md#networking-limits) .
- Há um encargo para IPs reservados?
    Casos. Para obter detalhes de preços, consulte a página de [detalhes de preços do IP reservado address](https://go.microsoft.com/fwlink/?LinkID=398482) .
- Como fazer reservar um endereço IP?
    Você pode usar o PowerShell, a [API REST de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx)ou o [portal do Azure](https://portal.azure.com) para reservar um endereço IP em uma região do Azure. Um endereço IP reservado está associado à sua assinatura.
- Posso usar um IP reservado com VNets com base em grupo de afinidade?
    Não. Somente há suporte para IPs reservados em VNets regionais. Não há suporte para IPs reservados para VNets associados a grupos de afinidade. Para obter mais informações sobre como associar uma VNet a uma região ou grupo de afinidade, consulte o artigo [sobre VNets regionais e grupos de afinidade](virtual-networks-migrate-to-regional-vnet.md) .

## <a name="manage-reserved-vips"></a>Gerenciar VIPs reservados

### <a name="using-azure-powershell-classic"></a>Usando Azure PowerShell (clássico)

Para poder usar IPs reservados, você deve adicioná-lo à sua assinatura. Crie um IP reservado do pool de endereços IP públicos disponíveis no local *EUA Central* da seguinte maneira:

> [!NOTE]
> Para o modelo de implantação clássica, você deve instalar a versão de gerenciamento de serviços do Azure PowerShell. Para obter mais informações, veja [Instalar o módulo Service Management do Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Observe, no entanto, que você não pode especificar qual IP está sendo reservado. Para exibir quais endereços IP estão reservados em sua assinatura, execute o seguinte comando do PowerShell e observe os valores para *ReservedIPName* e *endereço*:

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
>Ao criar um endereço IP reservado com o PowerShell, você não pode especificar um grupo de recursos no qual criar o IP reservado. O Azure o coloca em um grupo de recursos chamado *Default-Networking* automaticamente. Se você criar o IP reservado usando o [portal do Azure](https://portal.azure.com), poderá especificar qualquer grupo de recursos que escolher. No entanto, se você criar o IP reservado em um grupo de recursos diferente de *Default-Networking* , sempre que fizer referência ao `Get-AzureReservedIP` IP `Remove-AzureReservedIP`reservado com comandos como e, você deverá fazer referência ao grupo de nomes *Resource-Group-Name reservado-IP-Name*.  Por exemplo, se você criar um IP reservado chamado *myReservedIP* em um grupo de recursos chamado *MyResource*Group, deverá fazer referência ao nome do IP reservado como *grupo MyResource Group myReservedIP*.   


Quando um IP é reservado, ele permanece associado à sua assinatura até que você o exclua. Exclua um IP reservado da seguinte maneira:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Usando CLI do Azure (clássico)
Crie um IP reservado do pool de endereços IP públicos disponíveis no local de *EUA Central* como usando a CLI clássica do Azure a seguir:

> [!NOTE]
> Para a implantação clássica, você deve usar a CLI clássica do Azure. Para obter informações sobre como instalar a CLI clássica do Azure, consulte [instalar a CLI clássica do Azure](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Linha
 
```azurecli
azure network reserved-ip create <name> <location>
```
Exemplo:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Você pode exibir quais endereços IP são reservados em sua assinatura usando CLI do Azure da seguinte maneira: 

Linha
```azurecli
azure network reserved-ip list
```
Quando um IP é reservado, ele permanece associado à sua assinatura até que você o exclua. Exclua um IP reservado da seguinte maneira:

Linha

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Exemplo:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Reservar o endereço IP de um serviço de nuvem existente
Você pode reservar o endereço IP de um serviço de nuvem existente adicionando o `-ServiceName` parâmetro. Reserve o endereço IP de um serviço de nuvem *TestService* no local *EUA Central* da seguinte maneira:

- Usando Azure PowerShell (clássico):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Usando CLI do Azure (clássico):
  
    Linha

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Exemplo:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associar um IP reservado a um novo serviço de nuvem
O script a seguir cria um novo IP reservado e o associa a um novo serviço de nuvem chamado *TestService*.

### <a name="using-azure-powershell-classic"></a>Usando Azure PowerShell (clássico)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Ao criar um IP reservado para usar com um serviço de nuvem, você ainda se refere à VM usando *VIP:&lt;número da porta >* para comunicação de entrada. Reservar um IP não significa que você pode se conectar à VM diretamente. O IP reservado é atribuído ao serviço de nuvem no qual a VM foi implantada. Se você quiser se conectar a uma VM diretamente por IP, precisará configurar um IP público em nível de instância. Um IP público em nível de instância é um tipo de IP público (chamado de ILPIP) atribuído diretamente à sua VM. Ele não pode ser reservado. Para obter mais informações, leia o artigo [IP público em nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md) .
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Remover um IP reservado de uma implantação em execução

Remova um IP reservado adicionado a um novo serviço de nuvem da seguinte maneira: 
### <a name="using-azure-powershell-classic"></a>Usando Azure PowerShell (clássico)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Usando CLI do Azure (clássico)
Linha

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Exemplo:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> A remoção de um IP reservado de uma implantação em execução não remove a reserva de sua assinatura. Ele simplesmente libera o IP a ser usado por outro recurso em sua assinatura.
> 

Para remover completamente um IP reservado de uma assinatura, execute o seguinte comando:

Linha

```azurecli
azure network reserved-ip delete <name>
```
Exemplo:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associar um IP reservado a uma implantação em execução

### <a name="using-azure-powershell-classic"></a>Usando Azure PowerShell (clássico)

Os comandos a seguir criam um serviço de nuvem chamado *TestService2* com uma nova VM denominada *TestVM2*. O IP reservado existente denominado *MyReservedIP* é então associado ao serviço de nuvem.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Usando CLI do Azure (clássico)
Você pode associar um novo IP reservado à implantação do serviço de nuvem em execução usando CLI do Azure da seguinte maneira:

Linha
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Exemplo:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço
Você também pode associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço (CSCFG). O XML de exemplo a seguir mostra como configurar um serviço de nuvem para usar um VIP reservado denominado *MyReservedIP*:
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
* Entenda como o [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implantação clássico.
* Saiba mais sobre [endereços IP privados reservados](virtual-networks-reserved-private-ip.md).
* Saiba mais sobre [endereços IP públicos de nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md).

