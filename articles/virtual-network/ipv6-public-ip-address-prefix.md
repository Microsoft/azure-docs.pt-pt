---
title: Prefixo de endereço Público IPv6 na rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o prefixo de endereço iPv6 público na rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 096dbcb6b6a732bd21622658f6f30c5158a821c5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420528"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Prefixo de endereço iPv6 público reservado

Em Azure, as redes virtuais de dupla pilha (IPv4+IPv6) e as máquinas virtuais (VMs) são seguras por padrão, uma vez que não têm conectividade com a Internet. Pode facilmente adicionar uma conectividade IPv6 à Internet aos seus Equilíbrios de Carga Azure e VMs com endereços IPv6 públicos que obtém do Azure.

Quaisquer IPs públicos que reserve estão associados a uma região Azure à sua escolha e à sua subscrição Azure. Pode mover um IP público IPv6 (estático) reservado entre qualquer um dos Saldos de Carga Azure ou VMs na sua subscrição. Poderá dissociar totalmente o IP público IPv6 e será mantido para o seu uso quando estiver pronto.

> [!WARNING]
> Tenha cuidado para não apagar acidentalmente os seus endereços IP públicos. A eliminação de um IP público retira-o da sua subscrição e não poderá recuperá-lo (nem mesmo com a ajuda do suporte do Azure).

Além de reservar endereços IPv6 individuais, pode reservar gamas contíguas de endereços Azure IPv6 (conhecidos como prefixo IP) para a sua utilização.  Semelhantes aos endereços IP individuais, prefixos reservados estão associados a uma região Azure à sua escolha e à sua subscrição Azure. Reservar uma previsível e contígua gama de endereços tem muitos usos. Por exemplo, pode simplificar consideravelmente a lista de *ip* das suas aplicações hospedadas no Azure pela sua empresa e pelos seus clientes, uma vez que as suas gamas ip estáticas podem ser prontamente programadas para firewalls no local.  Pode criar iPs públicos individuais a partir do seu prefixo IP, conforme necessário e quando eliminar esses IPs públicos individuais, eles são *devolvidos* ao seu intervalo reservado para que possa reutilizá-los mais tarde. Todos os endereços IP do seu Prefixo IP estão reservados para a sua utilização exclusiva até que apague o prefixo.



## <a name="ipv6-prefix-sizes"></a>Tamanhos de prefixo IPv6
Estão disponíveis os seguintes tamanhos de prefixo IP públicos:

-  Tamanho mínimo de prefixo IPv6: /127 = 2 endereços
-  Tamanho máximo da prefixação IPv6: /124 = 16 endereços

O tamanho da prefixação é especificado como um tamanho de máscara de encaminhamento inter-domínio sem classe (CIDR). Por exemplo, uma máscara de /128 representa um endereço IPv6 individual, uma vez que os endereços IPv6 são compostos por 128 bits.

## <a name="pricing"></a>Preços
 
Para os custos associados à utilização de IPs públicos azure, tanto endereços IP individuais como intervalos IP, consulte preços de endereço ip [público](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Limitações
O IPv6 é suportado apenas em IPs públicos básicos com alocação "dinâmica", o que significa que o endereço IPv6 mudará se eliminar e recolocar a sua aplicação (VM's ou equilibradores de carga) em Azure. A atribuição de suporte do IP público Standard IPv6 unicamente estático (reservado), embora os equilibradores de carga INTERNO standard também possam suportar a atribuição dinâmica a partir da subnet a que são atribuídos.  

Como uma boa prática, recomendamos que utilize IPs públicos standard e balanceadores de carga padrão para as suas aplicações IPv6.

## <a name="next-steps"></a>Passos seguintes
- Reserve um prefixo de [endereço iPv6](ipv6-reserve-public-ip-address-prefix.md)público .
- Saiba mais sobre [endereços IPv6](ipv6-overview.md).
- Saiba [como criar e usar IPs públicos](virtual-network-public-ip-address.md) (tanto iPv4 como IPv6) em Azure.
