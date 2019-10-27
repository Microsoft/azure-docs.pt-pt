---
title: Prefixo de endereço IPv6 público na rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o prefixo de endereço IPv6 público na rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965178"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Prefixo de endereço IPv6 público reservado (versão prévia)

No Azure, as redes virtuais (VNet) e as VMs (máquinas virtuais) de pilha dupla (IPv4 + IPv6) são protegidas por padrão, pois não têm conectividade com a Internet. Você pode adicionar facilmente uma conectividade com a Internet IPv6 aos balanceadores de carga e às VMs do Azure com os endereços IPv6 públicos obtidos do Azure.

Todos os IPs públicos reservados estão associados a uma região do Azure de sua escolha e com sua assinatura do Azure. Você pode mover um IP público IPv6 reservado (estático) entre qualquer um dos balanceadores de carga do Azure ou VMs em sua assinatura. Você pode desassociar totalmente o IP público IPv6 e ele será mantido para seu uso quando você estiver pronto.

> [!WARNING]
> Tome cuidado para não excluir seus endereços IP públicos acidentalmente. Excluir um IP público o Remove da sua assinatura e você não poderá recuperá-lo (nem mesmo com a ajuda do suporte do Azure).

Além de reservar endereços IPv6 individuais, Reserve intervalos contíguos de endereços IPv6 do Azure (conhecido como prefixo IP) para seu uso.  Semelhante a endereços IP individuais, os prefixos reservados são associados a uma região do Azure de sua escolha e com sua assinatura do Azure. Reservar um intervalo previsível e contíguo de endereços tem muitos usos. Por exemplo, você pode simplificar bastante a *lista* de permissões de IP de seus aplicativos hospedados no Azure por sua empresa e seus clientes, já que seus intervalos de IP estáticos podem ser prontamente programados em firewalls locais.  Você pode criar IPs públicos individuais do seu prefixo IP conforme necessário e, quando você exclui esses IPs públicos individuais, eles são *retornados* para o intervalo reservado para que você possa reutilizá-los mais tarde. Todos os endereços IP em seu prefixo IP são reservados para seu uso exclusivo até o momento em que você excluir o prefixo.

> [!Important]
> O IPv6 para a rede virtual do Azure está atualmente em visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

## <a name="ipv6-prefix-sizes"></a>Tamanhos de prefixo IPv6
Os seguintes tamanhos de prefixo IP público estão disponíveis:

-  Tamanho mínimo do prefixo IPv6:/127 = 2 endereços
-  Tamanho máximo de prefixo IPv6:/124 = 16 endereços

O tamanho do prefixo é especificado como um tamanho de máscara CIDR (roteamento entre domínios sem classificação). Por exemplo, uma máscara de/128 representa um endereço IPv6 individual, pois os endereços IPv6 são compostos de 128 bits.

## <a name="pricing"></a>Preços
 
Para obter os custos associados ao uso de IPs públicos do Azure, endereços IP individuais e intervalos de IP, consulte [preços de endereço IP público](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Limitações
O IPv6 tem suporte apenas em IPs públicos básicos com alocação "dinâmica", o que significa que o endereço IPv6 será alterado se você excluir e reimplantar o aplicativo (balanceadores de carga ou VM) no Azure. O IP público padrão do IPv6 dá suporte exclusivamente à alocação estática (reservada), embora os balanceadores de carga internos padrão também possam oferecer suporte à alocação dinâmica de dentro da sub-rede à qual eles são atribuídos.  

Como prática recomendada, recomendamos que você use IPs públicos padrão e balanceadores de carga padrão para seus aplicativos IPv6.

## <a name="next-steps"></a>Passos seguintes
- Reserve um [prefixo de endereço IPv6](ipv6-reserve-public-ip-address-prefix.md)público.
- Saiba mais sobre [endereços IPv6](ipv6-overview.md).
- Saiba mais sobre [como criar e usar IPs públicos](virtual-network-public-ip-address.md) (IPv4 e IPv6) no Azure.
