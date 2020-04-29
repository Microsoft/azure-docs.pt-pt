---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75437004"
---
|  | **Peering Privado** | **Peering da Microsoft** |  **Público** (depreciado por novos circuitos) |
| --- | --- | --- | --- |
| **Max, max. # prefixos suportados por peering** |4000 por padrão, 10.000 com ExpressRoute Premium |200 |200 |
| **Intervalos de endereçoip suportados** |Qualquer endereço IP válido dentro do seu WAN. |Endereços IP públicos que lhe pertencem ou do seu fornecedor de conectividade. |Endereços IP públicos que lhe pertencem ou do seu fornecedor de conectividade. |
| **Requisitos de número as** |Números de AS privados e públicos. Deve possuir o número de AS do público se optar por utilizar um. |Números de AS privados e públicos. No entanto, deve provar a propriedade de endereços IP públicos. |Números de AS privados e públicos. No entanto, deve provar a propriedade de endereços IP públicos. |
| **Protocolos IP suportados**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Endereços IP da Interface de Encaminhamento** |RFC1918 e endereços IP públicos |Endereços IP públicos registados em registos de encaminhamento. |Endereços IP públicos registados em registos de encaminhamento. |
| **Suporte de Hash MD5** |Sim |Sim |Sim |