---
title: Endereços IP privados em Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre endereços IP privados em Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223266"
---
# <a name="private-ip-addresses"></a>Endereços IP privados
Os IPs privados permitem a comunicação entre os recursos em Azure. 

Os recursos podem ser:
* Serviços Azure tais como:
    * Interfaces de rede de máquina virtual
    * Balanceadores de carga internos (ILBs)
    * Gateways de aplicação
* Numa [rede virtual.](virtual-networks-overview.md)
* Rede no local através de um gateway VPN ou circuito ExpressRoute.

Os IPs privados permitem a comunicação a estes recursos sem a utilização de um endereço IP público.

## <a name="allocation-method"></a>Método de alocação

O Azure atribui endereços IP privados a recursos a partir do intervalo de endereços da sub-rede de rede virtual onde o recurso se encontra.

O Azure reserva os primeiros quatro endereços em cada gama de endereços da sub-rede. Os endereços não podem ser atribuídos a recursos. Por exemplo, se o intervalo de endereços da sub-rede for de 10.0.0.0/16, os endereços 10.0.0.0-10.0.0.3 e 10.0.255.255 não estão disponíveis. Os endereços IP dentro do intervalo de endereços da sub-rede só podem ser atribuídos a um recurso de cada vez. 

Existem dois métodos em que é dado um endereço IP privado:

- **Dinâmica**: o Azure atribui o endereço IP não atribuído ou não reservado seguinte disponível no intervalo de endereços da sub-rede. Por exemplo, o Azure atribui o endereço 10.0.0.10 a um novo recurso caso os endereços 10.0.0.4 a 10.0.0.9 já estejam atribuídos a outros recursos. 

    Dinâmico é o método de alocação predefinido. Uma vez atribuídos, os endereços IP dinâmicos são lançados se uma interface de rede for:
    
    * Eliminado
    * Reatribuída a uma sub-rede diferente dentro da mesma rede virtual.
    * O método de atribuição é alterado para estático, e um endereço IP diferente é especificado. 
    
    Por predefinição, o Azure atribui o endereço atribuído de forma dinâmica anterior como endereço estático quando o utilizador altera o método de alocação de dinâmico para estático.

- **Estática**: o utilizador seleciona e atribui um endereço IP não atribuído ou não reservado qualquer do intervalo de endereços da sub-rede. 

    Por exemplo, o intervalo de endereços de uma sub-rede é de 10.0.0.0/16 e os endereços 10.0.0.4-10.0.0.9 são atribuídos a outros recursos. Pode atribuir qualquer endereço entre 10.0.0.0.10 e 10.0.255.254. Os endereços estáticos só são lançados se uma interface de rede for eliminada. 
    
    O Azure atribui o IP estático como o IP dinâmico quando o método de atribuição é alterado. A reatribuição ocorre mesmo que o endereço não seja o próximo disponível na sub-rede. O endereço muda quando a interface de rede é atribuída a uma sub-rede diferente.
    
    Para atribuir a interface de rede a uma sub-rede diferente, altera o método de atribuição de estática para dinâmica. Atribua a interface de rede a uma sub-rede diferente e, em seguida, altere o método de atribuição de volta para estático. Atribua um endereço IP a partir do intervalo de endereços da nova sub-rede.
    
## <a name="virtual-machines"></a>Máquinas virtuais

Um ou mais endereços IP privados são atribuídos a uma ou mais **interfaces de rede**. As interfaces de rede são atribuídas a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Pode especificar o método de alocação como dinâmico ou estático para cada endereço IP privado.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Resolução de nomes de anfitrião DNS interna (para máquinas virtuais)

As máquinas virtuais Azure são configuradas com [servidores DNS geridos pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão. Pode configurar explicitamente servidores DNS personalizados. Estes servidores DNS fornecem resolução de nome interno para máquinas virtuais que se encontram dentro da mesma rede virtual.

Um mapeamento para o nome anfitrião para o endereço IP privado de uma máquina virtual é adicionado aos servidores DNS geridos pelo Azure. 

Um nome de hospedeiro é mapeado para o IP primário da interface de rede principal quando um VM tem:

* Várias interfaces de rede
* Vários endereços IP
* Ambos

Os VMs configurados com DNS geridos pelo Azure resolvem os hostnames dentro da mesma rede virtual. Utilize um servidor DNS personalizado para resolver os nomes dos anfitriões de VMs em redes virtuais conectadas.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga internos (ILBs) e gateways de aplicação

Pode atribuir um endereço IP privado **à** configuração frontal de um:

* [Equilibrador interno](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de carga (ILB)
* [Gateway de Aplicação do Azure](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Este endereço IP privado serve como um ponto final interno. O ponto final interno é acessível apenas aos recursos dentro da sua rede virtual e às redes remotas ligadas à sua rede. Um IP dinâmico ou estático pode ser atribuído.

## <a name="at-a-glance"></a>De relance
A tabela seguinte mostra a propriedade através da qual um IP privado pode ser associado a um recurso. 

Os possíveis métodos de atribuição que podem ser utilizados também são apresentados:

* Dinâmica
* Estático

| Recurso de nível superior | Associação de endereço IP | Dinâmica | Estático |
| --- | --- | --- | --- |
| Máquina virtual |Interface de rede |Yes |Yes |
| Balanceador de carga |Configuração de front-end |Yes |Yes |
| Gateway de aplicação |Configuração de front-end |Yes |Yes |

## <a name="limits"></a>Limites
Os limites para a endereçamento de IP encontram-se no conjunto completo de [limites para a ligação](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) em rede em Azure. Os limites são por região e por subscrição. [Suporte de contato](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites de incumprimento até aos limites máximos baseados nas necessidades do seu negócio.

## <a name="next-steps"></a>Passos seguintes
Saiba mais [sobre endereços IP públicos em Azure](public-ip-addresses.md)
* [Deploy a VM with a static private IP address using the Azure portal (Implementar uma VM com um endereço IP privado estático através do portal do Azure)](virtual-networks-static-private-ip-arm-pportal.md)