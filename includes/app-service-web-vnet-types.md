---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671515"
---
* Os sistemas multi-inquilinos que suportam toda a gama de planos de preços, exceto Isolados
* O App Service Environment (ASE), que se implanta no seu VNet e suporta aplicações de planode preços isolados

Este documento passa pela funcionalidade de Integração VNet, que é para uso nas aplicações multi-inquilinos. Se a sua aplicação estiver no [App Service Environment][ASEintro], então já está num VNet e não requer a utilização da funcionalidade de Integração VNet para chegar a recursos no mesmo VNet. Para mais detalhes sobre todas as funcionalidades de networking, leia as [funcionalidades][Networkingfeatures] de rede do App Service

A VNet Integration dá à sua aplicação acesso aos recursos da sua rede virtual, mas não concede acesso privado à sua aplicação a partir do VNet. O acesso ao site privado refere-se a tornar a aplicação apenas acessível a partir de uma rede privada, como dentro de uma rede virtual Azure. A VNet Integration é apenas para fazer chamadas de saída da sua aplicação para o seu VNet. A funcionalidade de Integração VNet comporta-se de forma diferente quando utilizada com VNets na mesma região e com VNets noutras regiões. A funcionalidade de Integração VNet tem duas variações.

* Integração VNet Regional - Ao ligar-se aos VNets gestor de recursos na mesma região, deve ter uma subrede dedicada no VNet com que está a integrar. 
* Gateway exigiu integração VNet - Quando ligar a VNets em outras regiões ou a um VNet Clássico na mesma região, você precisa de um gateway de rede virtual aprovisionado no alvo VNet.

As funcionalidades de Integração VNet:

* requerem um plano de preços Standard, Premium, PremiumV2 ou Elástico Premium 
* apoiar TCP e UDP
* trabalhar com apps de Serviço de Aplicações e aplicativos função

Há algumas coisas que a VNet Integration não suporta, incluindo:

* montando uma unidade
* A integração do AD 
* A NetBios

Gateway necessário VNet Integração apenas fornece acesso a recursos no VNet alvo ou em redes ligadas ao alvo VNet com peering ou VPNs. Gateway necessário VNet Integração não permite o acesso aos recursos disponíveis através de conexões ExpressRoute ou trabalha com pontos finais de serviço. 

Independentemente da versão utilizada, a VNet Integration dá à sua aplicação acesso aos recursos da sua rede virtual, mas não concede acesso privado à sua aplicação a partir da rede virtual. O acesso ao site privado refere-se a tornar a sua aplicação apenas acessível a partir de uma rede privada, como dentro de uma rede virtual Azure. A VNet Integration é apenas para fazer chamadas de saída da sua aplicação para o seu VNet. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features