---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: ccompy
ms.openlocfilehash: 0b93111357cf0d6e57eeb5495d50bd18a15dca77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97812801"
---
* Os sistemas multitenantes que suportam toda a gama de planos de preços, exceto isolados.
* O Ambiente de Serviço de Aplicações, que se implanta no seu VNet e suporta aplicações de planos de preços isolados.

A funcionalidade de Integração VNet é utilizada em aplicações multitenantes. Se a sua aplicação está no Ambiente de Serviço de [Aplicações,][ASEintro]então já está num VNet e não necessita de utilização da funcionalidade de Integração VNet para alcançar recursos no mesmo VNet. Para obter mais informações sobre todas as funcionalidades de networking, consulte as [funcionalidades de networking do Serviço de Aplicações][Networkingfeatures].

A VNet Integration dá à sua aplicação acesso a recursos no seu VNet, mas não concede acesso privado à sua aplicação a partir do VNet. O acesso ao site privado refere-se a tornar uma aplicação acessível apenas a partir de uma rede privada, como por exemplo dentro de uma rede virtual Azure. A VNet Integration é usada apenas para fazer chamadas de saída da sua app para o seu VNet. A funcionalidade de Integração VNet comporta-se de forma diferente quando é usada com VNet na mesma região e com vNet noutras regiões. A funcionalidade de Integração VNet tem duas variações:

* **Integração Regional VNet**: Quando ligar às redes virtuais do Azure Resource Manager na mesma região, deve ter uma sub-rede dedicada no VNet com a qual se está a integrar.
* **Integração VNet exigida pelo Gateway**: Quando se conecta ao VNet noutras regiões ou a uma rede virtual clássica na mesma região, precisa de um gateway de rede virtual Azure a provisionado no VNet alvo.

As funcionalidades de Integração VNet:

* Requer um plano de preços Standard, Premium, PremiumV2, PremiumV3 ou Elastic Premium.
* Apoiar TCP e UDP.
* Trabalhe com aplicações e aplicações de função do Azure App Service.

Há algumas coisas que a Integração VNet não suporta, como:

* Montar uma unidade.
* Integração do Diretório Ativo.
* O NetBIOS.

A Integração VNet exigida pelo Gateway fornece acesso a recursos apenas no VNet alvo ou em redes ligadas ao VNet alvo com peering ou VPNs. A Integração VNet exigida pelo Gateway não permite o acesso aos recursos disponíveis em todas as ligações Azure ExpressRoute ou trabalha com pontos finais de serviço.

Independentemente da versão utilizada, a VNet Integration dá à sua aplicação acesso a recursos no seu VNet, mas não concede acesso privado à sua aplicação a partir do VNet. O acesso ao site privado refere-se a tornar a sua aplicação acessível apenas a partir de uma rede privada, como por exemplo dentro de um Azure VNet. A Integração VNet destina-se apenas a fazer chamadas de saída da sua app para o seu VNet.

<!--Links-->
[ASEintro]: ../articles/app-service/environment/intro.md
[Networkingfeatures]: ../articles/app-service/networking-features.md
