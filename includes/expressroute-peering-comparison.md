---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750298"
---
|  | **Peering Privado** | **Peering da Microsoft** |  **Peering público** (depreciado para novos circuitos) |
| --- | --- | --- | --- |
| **Max. # prefixos suportados por espreitar** |4000 por defeito, 10.000 com ExpressRoute Premium |200 |200 |
| **Intervalos de endereço IP suportados** |Qualquer endereço IP válido dentro do seu WAN. |Endereços IP públicos pertencentes a si ou ao seu fornecedor de conectividade. |Endereços IP públicos pertencentes a si ou ao seu fornecedor de conectividade. |
| **Requisitos de números AS** |Números privados e públicos de AS. Deve possuir o número de AS público se optar por usar um. |Números privados e públicos de AS. No entanto, deve provar a propriedade dos endereços IP públicos. |Números privados e públicos de AS. No entanto, deve provar a propriedade dos endereços IP públicos. |
| **Protocolos IP suportados**| IPv4, IPv6 (pré-visualização) |  IPv4, IPv6 | IPv4 |
| **Endereços IP interface de encaminhamento** |ENDEREÇO RFC1918 e endereços IP públicos |Endereços IP públicos registados em registos de encaminhamento. |Endereços IP públicos registados em registos de encaminhamento. |
| **Suporte de hash MD5** |Sim |Sim |Sim |