---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75437004"
---
|  | **Peering Privado** | **Microsoft Peering** |  **Peering público** (depreciado para novos circuitos) |
| --- | --- | --- | --- |
| **Max. # prefixos suportados por espreitar** |4000 por defeito, 10.000 com ExpressRoute Premium |200 |200 |
| **Intervalos de endereço IP suportados** |Qualquer endereço IP válido dentro do seu WAN. |Endereços IP públicos pertencentes a si ou ao seu fornecedor de conectividade. |Endereços IP públicos pertencentes a si ou ao seu fornecedor de conectividade. |
| **Requisitos de números AS** |Números privados e públicos de AS. Deve possuir o número de AS público se optar por usar um. |Números privados e públicos de AS. No entanto, deve provar a propriedade dos endereços IP públicos. |Números privados e públicos de AS. No entanto, deve provar a propriedade dos endereços IP públicos. |
| **Protocolos IP suportados**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Endereços IP interface de encaminhamento** |ENDEREÇO RFC1918 e endereços IP públicos |Endereços IP públicos registados em registos de encaminhamento. |Endereços IP públicos registados em registos de encaminhamento. |
| **Suporte de hash MD5** |Sim |Sim |Sim |