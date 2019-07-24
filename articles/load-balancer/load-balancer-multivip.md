---
title: Vários VIPs para um serviço de nuvem
titlesuffix: Azure Load Balancer
description: Visão geral de multiVIP e como definir múltiplos VIPs em um serviço de nuvem
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3e97bea85d4d97b159168b21b4a6e932e655ccfb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274694"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurar vários VIPs para um serviço de nuvem

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Você pode acessar os serviços de nuvem do Azure pela Internet pública usando um endereço IP fornecido pelo Azure. Esse endereço IP público é conhecido como um VIP (IP virtual), pois ele está vinculado ao Azure Load Balancer e não às instâncias de VM (máquina virtual) no serviço de nuvem. Você pode acessar qualquer instância de VM em um serviço de nuvem usando um único VIP.

No entanto, há cenários em que você pode precisar de mais de um VIP como um ponto de entrada para o mesmo serviço de nuvem. Por exemplo, seu serviço de nuvem pode hospedar vários sites que exigem conectividade SSL usando a porta padrão de 443, pois cada site é hospedado para um cliente ou locatário diferente. Nesse cenário, você precisa ter um endereço IP voltado para o público diferente para cada site. O diagrama a seguir ilustra uma hospedagem de Web multilocatário típica com uma necessidade de vários certificados SSL na mesma porta pública.

![Cenário de SSL de vários VIPs](./media/load-balancer-multivip/Figure1.png)

No exemplo acima, todos os VIPs usam a mesma porta pública (443) e o tráfego é redirecionado para uma ou mais VMs com balanceamento de carga em uma porta privada exclusiva para o endereço IP interno do serviço de nuvem que hospeda todos os sites.

> [!NOTE]
> Outra situação que exige o uso de vários VIPs é hospedar vários ouvintes do grupo de disponibilidade AlwaysOn do SQL no mesmo conjunto de máquinas virtuais.

Os VIPs são dinâmicos por padrão, o que significa que o endereço IP real atribuído ao serviço de nuvem pode mudar ao longo do tempo. Para evitar que isso aconteça, você pode reservar um VIP para seu serviço. Para saber mais sobre VIPs reservados, consulte [IP público reservado](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Consulte os [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/) para obter informações sobre preços em VIPs e IPS reservados.

Você pode usar o PowerShell para verificar os VIPs usados por seus serviços de nuvem, bem como adicionar e remover VIPs, associar um VIP a um ponto de extremidade e configurar o balanceamento de carga em um VIP específico.

## <a name="limitations"></a>Limitações

Neste momento, a funcionalidade multi-VIP é limitada aos seguintes cenários:

* **Somente IaaS**. Você só pode habilitar vários VIPs para serviços de nuvem que contêm VMs. Você não pode usar vários VIPs em cenários de PaaS com instâncias de função.
* **Somente PowerShell**. Você só pode gerenciar vários VIPs usando o PowerShell.

Essas limitações são temporárias e podem ser alteradas a qualquer momento. Certifique-se de revisitar esta página para verificar as alterações futuras.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Como adicionar um VIP a um serviço de nuvem
Para adicionar um VIP ao seu serviço, execute o seguinte comando do PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Esse comando exibe um resultado semelhante ao exemplo a seguir:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Como remover um VIP de um serviço de nuvem
Para remover o VIP adicionado ao seu serviço no exemplo acima, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Você só poderá remover um VIP se ele não tiver pontos de extremidade associados a ele.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Como recuperar informações de VIP de um serviço de nuvem
Para recuperar os VIPs associados a um serviço de nuvem, execute o seguinte script do PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

O script exibe um resultado semelhante ao exemplo a seguir:

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

Neste exemplo, o serviço de nuvem tem 3 VIPs:

* **Vip1** é o VIP padrão, você sabe que, como o valor de IsDnsProgrammedName é definido como true.
* **VIP2** e **Vip3** não são usados, pois não têm nenhum endereço IP. Eles só serão usados se você associar um ponto de extremidade ao VIP.

> [!NOTE]
> Sua assinatura será cobrada somente por VIPs adicionais quando eles estiverem associados a um ponto de extremidade. Para obter mais informações sobre preços, consulte [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Como associar um VIP a um ponto de extremidade

Para associar um VIP em um serviço de nuvem a um ponto de extremidade, execute o seguinte comando do PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

O comando cria um ponto de extremidade vinculado ao VIP chamado *VIP2* na *porta 80*e o vincula à VM denominada *myVM1* em um serviço de nuvem  chamado MyService usando *TCP* na porta *8080*.

Para verificar a configuração, execute o seguinte comando do PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

A saída é semelhante ao exemplo a seguir:

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

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Como habilitar o balanceamento de carga em um VIP específico

Você pode associar um único VIP a várias máquinas virtuais para fins de balanceamento de carga. Por exemplo, você tem um serviço de nuvem chamado MyService e duas máquinas virtuais chamadas *myVM1* e *myVM2*. E seu serviço de nuvem tem vários VIPs, um deles chamado *VIP2*. Se você quiser garantir que todo o tráfego para a porta *81* em *VIP2* seja balanceado entre *myVM1* e *myVM2* na porta *8181*, execute o seguinte script do PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Você também pode atualizar o balanceador de carga para usar um VIP diferente. Por exemplo, se você executar o comando do PowerShell abaixo, você alterará o conjunto de balanceamento de carga para usar um VIP chamado Vip1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Próximos Passos

[Logs de Azure Monitor para o Azure Load Balancer](load-balancer-monitor-log.md)

[Visão geral do balanceador de carga voltado para a Internet](load-balancer-internet-overview.md)

[Introdução ao balanceador de carga voltado para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Virtual Network Overview](../virtual-network/virtual-networks-overview.md) (Descrição Geral da Rede Virtual)

[IP Reservado APIs REST](https://msdn.microsoft.com/library/azure/dn722420.aspx)
