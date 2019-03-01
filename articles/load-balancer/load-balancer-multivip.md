---
title: Vários VIPs para um serviço cloud
titlesuffix: Azure Load Balancer
description: Descrição geral de um multiVIP e como configurar vários VIPs num serviço cloud
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: bf5721e206316a4ce576253743e9ac65de47094a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194049"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurar vários VIPs para um serviço em nuvem

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Pode acessar serviços cloud do Azure através da Internet pública utilizando um endereço IP fornecido pelo Azure. Este endereço IP público é referido como um VIP (virtual IP), uma vez que ele está vinculado ao balanceador de carga do Azure e não a Máquina Virtual (VM) instâncias no serviço cloud. Pode acessar qualquer instância VM num serviço cloud utilizando um único VIP.

No entanto, existem cenários em que poderá ter mais do que um VIP como uma entrada de apontar para o mesmo serviço cloud. Por exemplo, o seu serviço cloud pode alojar vários Web sites que requerem conectividade SSL a utilizar a porta padrão 443, como cada site é hospedado para outro cliente ou inquilino. Neste cenário, tem de ter um destinado ao endereço IP público diferente para cada Web site. O diagrama abaixo ilustra uma típica com a necessidade de vários certificados SSL na mesma porta pública de alojamento na web multi-inquilino.

![Cenário de vários VIP SSL](./media/load-balancer-multivip/Figure1.png)

No exemplo acima, todos os VIPs utilizam a mesma porta pública (443) e o tráfego será redirecionado para uma ou com balanceamento de carga mais VMs numa porta privada exclusiva para o endereço IP interno do serviço em nuvem que aloja os Web sites.

> [!NOTE]
> Outra situação exigir a utilização de vários VIPs está a alojar várias escutas de grupo de Disponibilidade AlwaysOn do SQL no mesmo conjunto de máquinas virtuais.

VIPs são dinâmicas por predefinição, o que significa que o endereço IP real atribuído ao serviço cloud pode mudar ao longo do tempo. Para evitar que aconteça, pode reservar um VIP para o seu serviço. Para saber mais sobre VIPs reservadas, consulte [IP públicos reservados](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Veja [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses/) para obter informações sobre preços em VIPs e IPs reservados.

Pode utilizar o PowerShell para verificar os VIPs utilizados pelo seu serviço cloud, bem como adicionar e remover VIPs, associar um VIP para um ponto final e configurar um VIP específico de balanceamento de carga.

## <a name="limitations"></a>Limitações

Neste momento, a funcionalidade de vários VIP está limitada para os cenários seguintes:

* **IaaS apenas**. Apenas pode ativar várias VIP para serviços em nuvem que contêm as VMs. Não é possível utilizar várias VIP em cenários de PaaS com instâncias de função.
* **PowerShell apenas**. Só pode gerir várias VIP com o PowerShell.

Essas limitações são temporárias e podem ser alterados em qualquer altura. Certifique-se de rever esta página para verificar as alterações futuras.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Como adicionar um VIP para um serviço em nuvem
Para adicionar um VIP ao seu serviço, execute o seguinte comando do PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Este comando apresenta um resultado semelhante ao exemplo seguinte:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Como remover um VIP de um serviço em nuvem
Para remover o VIP adicionado ao seu serviço no exemplo acima, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Só é possível remover um VIP se ele não tem pontos finais associados a ele.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Como obter informações de VIP a partir de um serviço em nuvem
Para obter os VIPs associados a um serviço em nuvem, execute o seguinte script do PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

O script exibe um resultado semelhante ao exemplo seguinte:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Neste exemplo, o serviço em nuvem tem 3 VIPs:

* **Vip1** é o VIP predefinido, sabe que uma vez que o valor para IsDnsProgrammedName é definido como true.
* **Vip2** e **Vip3** não são utilizados como não têm quaisquer endereços IP. Só irá ser utilizados se associar um ponto final para o VIP.

> [!NOTE]
> Sua assinatura apenas será cobrada para VIPs Extras assim que estiverem associados um ponto de extremidade. Para obter mais informações sobre os preços, consulte [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Como associar um VIP para um ponto final

Para associar um VIP num serviço cloud para um ponto de extremidade, execute o seguinte comando do PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

O comando cria um ponto de extremidade associado para o VIP chamado *Vip2* na porta *80*e associa-a VM com o nome *myVM1* num serviço cloud com o nome *myService* usando *TCP* na porta *8080*.

Para verificar a configuração, execute o seguinte comando do PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

O resultado será semelhante ao seguinte exemplo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Como ativar um VIP específico de balanceamento de carga

Pode associar um VIP único com várias máquinas virtuais para fins de balanceamento de carga. Por exemplo, tem um serviço em nuvem com o nome *myService*e duas máquinas virtuais com o nome *myVM1* e *myVM2*. E o seu serviço cloud tem múltiplos VIPs, um com o nome *Vip2*. Se pretender certificar-se de que todo o tráfego para a porta *81* nos *Vip2* seja balanceada entre *myVM1* e *myVM2* na porta *8181* , execute o seguinte script do PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Também pode atualizar o Balanceador de carga para utilizar um VIP diferente. Por exemplo, se executar o comando do PowerShell abaixo, irá alterar o conjunto para utilizar um VIP com o nome Vip1 de balanceamento de carga:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Próximos Passos

[Registos de Monitor do Azure para o balanceamento de carga do Azure](load-balancer-monitor-log.md)

[Descrição de geral do Internet destinado ao load balancer](load-balancer-internet-overview.md)

[Começar a funcionar com o Balanceador de carga com acesso à Internet](load-balancer-get-started-internet-arm-ps.md)

[Virtual Network Overview](../virtual-network/virtual-networks-overview.md) (Descrição Geral da Rede Virtual)

[APIs REST do IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx)
