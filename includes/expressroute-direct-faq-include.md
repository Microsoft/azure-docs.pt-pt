---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 07/09/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 43d1942b1413569d77c6c17fee9fff14dc83c924
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362598"
---
### <a name="what-is-expressroute-direct"></a>O que é o ExpressRoute Direct?

O ExpressRoute Direct oferece aos clientes a possibilidade de se ligarem diretamente à rede global da Microsoft em localizações de peering estrategicamente distribuídas em todo o mundo. O ExpressRoute Direct fornece conectividade dual de 100 ou 10 Gbps, que dá suporte à conectividade ativa/ativa em escala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Como os clientes se conectam ao ExpressRoute Direct? 

Os clientes precisarão trabalhar com suas operadoras locais e provedores de colocalização para obter conectividade com os roteadores do ExpressRoute para tirar proveito do ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Quais locais atualmente dão suporte ao ExpressRoute Direct? 

As portas disponíveis serão dinâmicas e estarão disponíveis pelo PowerShell para exibir a capacidade. Os locais incluem e *estão sujeitos a alterações com base na disponibilidade*:

* Amesterdão
* Amesterdão2
* Auckland 
* Chicago
* Dallas
* Dublin
* R.A.E. Hong Kong
* Londres
* Melbourne
* New York City
* Perth
* Antonio de San
* Seattle
* Seul
* Silicon Valley
* Cingapura2 
* Sydney
* Taipé
* Tóquio
* Toronto
* Washington, D.C.
* Washington DC2

### <a name="what-is-the-sla-for-expressroute-direct"></a>O que é o SLA para o ExpressRoute Direct?

O ExpressRoute Direct usará a mesma [classificação empresarial do expressroute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quais cenários os clientes devem considerar com o ExpressRoute Direct?  

O ExpressRoute Direct fornece aos clientes pares de portas diretas de 100 ou 10 Gbps no backbone global da Microsoft. Os cenários que oferecerão aos clientes os melhores benefícios incluem: Ingestão maciça de dados, isolamento físico para mercados regulamentados e capacidade dedicada para cenários de intermitência, como renderização. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual é o modelo de cobrança do ExpressRoute Direct? 

O ExpressRoute Direct será cobrado pelo par de portas em um valor fixo. Os circuitos padrão serão incluídos sem horas adicionais e o Premium terá um pequeno encargo de complemento. A saída será cobrada por circuito com base na zona do local de emparelhamento.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Quando a cobrança começa para os pares de porta direto do ExpressRoute?

Os pares de porta do ExpressRoute Direct são cobrados 45 dias na criação do recurso do ExpressRoute Direct ou quando um ou ambos os links são habilitados, o que ocorrer primeiro. O período de carência de 45 dias é concedido para permitir que os clientes concluam o processo de conexão cruzada com o provedor de colocalização.
