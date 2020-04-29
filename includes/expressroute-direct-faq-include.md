---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1f6c65d6d8da275d6c52ac2802092dbb5e4e507d
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73182262"
---
### <a name="what-is-expressroute-direct"></a>O que é o ExpressRoute Direct?

O ExpressRoute Direct oferece aos clientes a possibilidade de se ligarem diretamente à rede global da Microsoft em localizações de peering estrategicamente distribuídas em todo o mundo. O ExpressRoute Direct fornece conectividade dupla de 100 ou 10 Gbps, que suporta conectividade Ative/Ative em escala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Como é que os clientes se ligam ao ExpressRoute Direct? 

Os clientes terão de trabalhar com as suas transportadoras locais e fornecedores de co-localização para obter conectividade com os routers ExpressRoute para tirar partido do ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Que localizações suportam atualmente o ExpressRoute Direct? 

Por favor, verifique a disponibilidade na página de [localização](../articles/expressroute/expressroute-locations-providers.md). 

### <a name="what-is-the-sla-for-expressroute-direct"></a>O que é o SLA para ExpressRoute Direct?

O ExpressRoute Direct utilizará o mesmo [grau de empresa da ExpressRoute.](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Que cenários os clientes devem considerar com o ExpressRoute Direct?  

O ExpressRoute Direct fornece aos clientes pares de portas diretas de 100 ou 10 Gbps na espinha dorsal global da Microsoft. Os cenários que proporcionarão aos clientes os maiores benefícios incluem: Ingestão massiva de dados, isolamento físico para mercados regulados e capacidade dedicada para cenário de explosão, como renderização. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual é o modelo de faturação do ExpressRoute Direct? 

O ExpressRoute Direct será cobrado para o par de portas a um valor fixo. Os circuitos standard serão incluídos sem horas adicionais e o prémio terá uma ligeira carga adicional. A egress será cobrada por circuito com base na zona do local de observação.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Quando começa a faturação para os pares de portas ExpressRoute Direct?

Os pares portuários do ExpressRoute Direct são faturados 45 dias para a criação do recurso ExpressRoute Direct ou quando 1 ou ambos os links estão ativados, o que vier em primeiro lugar. O período de carência de 45 dias é concedido para permitir que os clientes completem o processo de ligação cruzada com o fornecedor de co-localização.
