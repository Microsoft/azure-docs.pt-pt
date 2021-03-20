---
title: Prefixo de endereço IPv6 público na rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o prefixo de endereço iPv6 público na rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99538211"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Prefixo de endereço público reservado IPv6

Em Azure, as redes virtuais (IPv4+IPv6) e as máquinas virtuais (VMs) são seguras por padrão, uma vez que não têm conectividade com a Internet. Pode adicionar facilmente uma conectividade IPv6 à Internet dos seus Balançadores de Carga Azure e VMs com endereços IPv6 públicos que obtém do Azure.

Quaisquer IPs públicos que reserve estão associados a uma região Azure à sua escolha e à sua subscrição Azure. Pode mover um IP público IPv6 reservado (estático) entre qualquer um dos Balançadores de Carga Azure ou VMs na sua subscrição. Pode dissociar totalmente o IP público do IPv6 e será guardado para uso quando estiver pronto.

> [!WARNING]
> Tenha cuidado para não eliminar acidentalmente os seus endereços IP públicos. A eliminação de um IP público retira-o da sua subscrição e não poderá recuperá-lo (nem mesmo com a ajuda do suporte do Azure).

Além de reservar endereços IPv6 individuais, pode reservar gamas contíguas de endereços Azure IPv6 (conhecido como prefixo IP) para a sua utilização.  Semelhantes a endereços IP individuais, os prefixos reservados estão associados a uma região Azure à sua escolha e à sua subscrição Azure. Reservar uma gama previsível e contígua de endereços tem muitos usos. Por exemplo, pode simplificar consideravelmente *a filtragem* IP das suas aplicações hospedadas no Azure pela sua empresa e pelos seus clientes, uma vez que as suas gamas IP estáticas podem ser facilmente programadas em firewalls no local.  Pode criar IPs públicos individuais a partir do seu prefixo IP conforme necessário e quando eliminar os IPs públicos individuais são *devolvidos* ao seu alcance reservado para que possa reutilizá-los mais tarde. Todos os endereços IP do seu Prefixo IP estão reservados para uso exclusivo até que apague o seu Prefix.



## <a name="ipv6-prefix-sizes"></a>Tamanhos do prefixo IPv6
Estão disponíveis os seguintes tamanhos de prefixo IP públicos:

-  Tamanho mínimo do prefixo IPv6: /127 = 2 endereços
-  Tamanho máximo do prefixo IPv6: /124 = 16 endereços

O tamanho do prefixo é especificado como um tamanho de máscara de encaminhamento de Inter-Domain (CIDR) sem classe. Por exemplo, uma máscara de /128 representa um endereço IPv6 individual, uma vez que os endereços IPv6 são compostos por 128 bits.

## <a name="pricing"></a>Preços
 
Para os custos associados à utilização de IPs públicos Azure, tanto endereços IP individuais como gamas IP, consulte [os preços do Endereço IP Público](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Limitações
O IPv6 é suportado em IPs públicos básicos apenas com uma atribuição "dinâmica", o que significa que o endereço IPv6 mudará se eliminar e redistribuir a sua aplicação (VM's ou balanceadores de carga) em Azure. O suporte do IP público Standard IPv6 apenas estático (reservado) embora os balanceadores de carga INTERNO padrão também possam suportar a atribuição dinâmica a partir da sub-rede a que estão atribuídos.  

Como uma boa prática, recomendamos que utilize iPs públicos standard e balanceadores de carga padrão para as suas aplicações IPv6.

## <a name="next-steps"></a>Passos seguintes
- Reserve um [prefixo de endereço IPv6](ipv6-reserve-public-ip-address-prefix.md)público.
- Saiba mais sobre [os endereços IPv6](ipv6-overview.md).
- Saiba [como criar e utilizar os IPs públicos](virtual-network-public-ip-address.md) (tanto IPv4 como IPv6) em Azure.
