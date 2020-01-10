---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437004"
---
|  | **Peering privado** | **Peering da Microsoft** |  **Peering público** (preterido para os novos circuitos do) |
| --- | --- | --- | --- |
| **Máx. prefixos de # suportados por peering** |4000 por padrão, 10 000 com o ExpressRoute Premium |200 |200 |
| **Intervalos de endereços IP suportados** |Qualquer endereço IP válido na sua WAN. |Endereços IP públicos pertencentes ao utilizador ou o fornecedor de conectividade. |Endereços IP públicos pertencentes ao utilizador ou o fornecedor de conectividade. |
| **COMO os requisitos de número** |Privados e públicos como números. Tem de o proprietário público como número se optar por utilizar um. |Privados e públicos como números. No entanto, tem de provar a propriedade de endereços IP públicos. |Privados e públicos como números. No entanto, tem de provar a propriedade de endereços IP públicos. |
| **Protocolos IP suportados**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Encaminhamento de IP de Interface de endereços** |Endereços IP público e de RFC1918 |Endereços IP públicos registados nos registos de encaminhamento. |Endereços IP públicos registados nos registos de encaminhamento. |
| **Suporte de MD5 Hash** |Sim |Sim |Sim |