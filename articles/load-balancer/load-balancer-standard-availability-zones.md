---
title: Zonas de balançadores de carga e disponibilidade de Azure
titleSuffix: Azure Load Balancer
description: Com este caminho de aprendizagem, começa com o Azure Standard Load Balancer e as Zonas de Disponibilidade.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699118"
---
# <a name="load-balancer-and-availability-zones"></a>Zonas de balançadores de carga e disponibilidade

O Azure Load Balancer suporta cenários de zonas de disponibilidade. Pode utilizar o Balancer de Carga Padrão para aumentar a disponibilidade em todo o seu cenário, alinhando recursos e distribuindo por zonas.  Reveja este documento para entender estes conceitos e orientação de design de cenário fundamental

Um Balanceador de Carga pode ser **uma zona redundante, zonal** ou **não-zonal**. Para configurar as propriedades relacionadas com a zona (acima mencionadas) para o seu equilibrador de carga, selecione o tipo adequado de frontend necessário.

## <a name="zone-redundant"></a>Zona redundante

Numa região com Zonas de Disponibilidade, um Balanceador de Carga Padrão pode ser redundante em zona. Este tráfego é servido por um único endereço IP.

Um único endereço IP frontal sobreviverá à falha da zona. O IP frontal pode ser utilizado para alcançar todos os membros do pool de backend (sem impacto) independentemente da zona. Uma ou mais zonas de disponibilidade podem falhar e o caminho dos dados sobrevive enquanto uma zona da região permanecer saudável.

O endereço IP do frontend é servido simultaneamente por múltiplas infraestruturas independentes em várias zonas de disponibilidade. Qualquer retração ou restabelecimento terá sucesso noutras zonas não afetadas pela falha da zona.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Rede Virtual NAT">
</p>

*Figura: Balanceador de carga redundante zona*

## <a name="zonal"></a>Zonal

Você pode optar por ter um frontend garantido para uma única zona, que é conhecida como uma *zonal.*  Este cenário significa que qualquer fluxo de entrada ou saída é servido por uma única zona numa região.  O seu frontend partilha o destino com a saúde da zona.  O caminho dos dados não é afetado por falhas em zonas diferentes das que foram garantidas. Pode utilizar frontes zonais para expor um endereço IP por Zona de Disponibilidade.  

Adicionalmente, é suportada a utilização de frontends zonais diretamente para pontos finais equilibrados de carga dentro de cada zona. Pode utilizar esta configuração para expor por pontos finais equilibrados em zona para monitorizar individualmente cada zona. Para pontos finais públicos, pode integrá-los com um produto de equilíbrio de carga DNS como [Traffic Manager](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Rede Virtual NAT">
</p>

*Figura: Balanceador de carga zonal*

Para um frontend do balançador de carga pública, adicione um parâmetro **de zonas** ao IP público. Este IP público é referenciado pela configuração IP frontend utilizada pela respetiva regra.

Para uma frente do balançador de carga interna, adicione um parâmetro **de zonas** à configuração IP do balanço de carga interno. Um frontend zonal garante um endereço IP numa sub-rede para uma zona específica.

## <a name="design-considerations"></a><a name="design"></a> Considerações de design

Agora que compreende as propriedades relacionadas com a zona para o Standard Load Balancer, as seguintes considerações de design podem ajudar a conceber para uma alta disponibilidade.

### <a name="tolerance-to-zone-failure"></a>Tolerância à falha de zona

- Um balanceador de carga **redundante de zona** pode servir um recurso zonal em qualquer zona com um endereço IP.  O IP pode sobreviver a uma ou mais falhas de zona, desde que pelo menos uma zona permaneça saudável na região.
- Uma **frente zonal** é uma redução do serviço para uma única zona e partilha o destino com a respetiva zona. Se a zona em que a sua implantação está a diminuir, a sua implantação não sobreviverá a esta falha.

Recomenda-se a utilização de balanceador de carga redundante para as suas cargas de trabalho de produção.

### <a name="control-vs-data-plane-implications"></a>Controlo vs implicações do plano de dados

A redundância de zona não implica um avião de dados sem impacto ou um avião de controlo. Os fluxos redundantes de zona podem usar qualquer zona e os seus fluxos usarão todas as zonas saudáveis de uma região. Numa falha de zona, os fluxos de tráfego usando zonas saudáveis não são afetados.

Os fluxos de tráfego que utilizam uma zona no momento da avaria do fuso podem ser afetados, mas as aplicações podem recuperar. O tráfego continua nas zonas saudáveis da região após a retransmissão quando Azure convergiu em torno da falha da zona.

Reveja [os padrões de design em nuvem Azure](/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [As Zonas de Disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Balanceador de Carga Standard](./load-balancer-overview.md)
- Saiba como [carregar VMs de equilíbrio dentro de uma zona usando um balanceador de carga padrão zonal](./quickstart-load-balancer-standard-public-cli.md)
- Saiba como [carregar vMs de equilíbrio em zonas usando um balanceador de carga padrão redundante de zona](./quickstart-load-balancer-standard-public-cli.md)
- Conheça os [padrões de design de nuvem Azure](/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.
