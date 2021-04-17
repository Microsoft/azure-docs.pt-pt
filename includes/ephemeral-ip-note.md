---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529581"
---
> [!NOTE]
> O Azure fornece um IP efémero para máquinas virtuais Azure que não são atribuídas a um endereço IP público, ou estão no pool de backend de um Balancer de Carga Básico Interno. O mecanismo IP efémero fornece um endereço IP de saída que não é configurável. 
>
>O IP efémero é desativado quando um endereço IP público é atribuído à máquina virtual ou a máquina virtual é colocada no pool de backend de um Balancer de Carga Padrão com ou sem regras de saída. Se um recurso de gateway [DE REDE VIRTUAL Azure](../articles/virtual-network/nat-overview.md) for atribuído à sub-rede da máquina virtual, o IP efémero é desativado.
>
> Para obter mais informações sobre as ligações de saída em Azure, consulte [utilizar a tradução de endereços de rede de origem (SNAT) para ligações de saída](../articles/load-balancer/load-balancer-outbound-connections.md).