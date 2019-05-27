---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1bcadc18172535649a0ceb482939ca6a75477e25
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170689"
---
### <a name="what-is-expressroute-direct"></a>O que é direta do ExpressRoute?

O ExpressRoute Direct oferece aos clientes a possibilidade de se ligarem diretamente à rede global da Microsoft em localizações de peering estrategicamente distribuídas em todo o mundo. ExpressRoute Direct fornece 100 dupla conectividade Gbps, que suporta a conetividade de ativo/ativo em escala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Como os clientes ligar a Direct do ExpressRoute? 

Clientes precisam trabalhar com as respetivas operadoras locais e fornecedores de localização conjunta para obter conectividade ao ExpressRoute routers para tirar partido do ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>As localizações atualmente dão suporte direto do ExpressRoute? 

As portas disponíveis serão dinâmicas e estarão disponíveis através do PowerShell para ver a capacidade. Locais incluem e *estão sujeitos a alterações com base na disponibilidade*:

* Amesterdão
* Camberra
* Chicago
* Washington, D.C.
* Dallas 
* R.A.E. Hong Kong
* Londres
* Los Angeles
* New York City
* Paris
* Perth
* Toronto
* San Antonio
* Seattle
* Seul
* Silicon Valley
* Singapura 
* Sydney

### <a name="what-is-the-sla-for-expressroute-direct"></a>O que é o SLA para o Direct do ExpressRoute?

ExpressRoute Direct passarão a utilizar o mesmo [nível empresarial do ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quais cenários devem considerar os clientes com Direct do ExpressRoute?  

ExpressRoute Direct fornece aos clientes diretos 100 pares de portas de Gbps na estrutura principal global da Microsoft. Os cenários que irão proporcionar aos clientes com maiores benefícios incluem: Ingestão de dados em massa, isolamento físico para mercados regulamentados e capacidade dedicada para o cenário de expansão, como o processamento. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>O que é o modelo de faturação direta do ExpressRoute? 

Será faturada direta do ExpressRoute para o par de porta numa quantidade fixa. Circuitos padrão serão incluídos em nenhuma horas adicionais e premium terão um custo de complemento pequeno. Saída é cobrada de forma por circuito, com base na zona da localização do peering.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Quando faz o início de faturação para os pares de porta Direct do ExpressRoute?

Pares de portas do ExpressRoute diretos são faturadas a 45 dias para a criação do recurso direto do ExpressRoute ou quando 1 ou ambas as ligações estão ativadas, o que ocorrer primeiro. O período de tolerância de 45 dias é concedido para permitir que os clientes concluir o processo de ligação cruzada com o fornecedor de colocalização.
