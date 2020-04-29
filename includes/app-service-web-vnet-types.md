---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81312811"
---
* Os sistemas multiarrendatários que suportam toda a gama de planos de preços exceto isolados.
* O App Service Environment, que se implanta no seu VNet e suporta aplicações de planos de preços isolados.

A funcionalidade de Integração VNet é utilizada em aplicações multiarrendatárias. Se a sua aplicação estiver no [App Service Environment][ASEintro], então já está num VNet e não requer a utilização da funcionalidade de Integração VNet para chegar a recursos no mesmo VNet. Para obter mais informações sobre todas as funcionalidades de networking, consulte as funcionalidades de [rede do Serviço de Aplicações.][Networkingfeatures]

A VNet Integration dá à sua aplicação acesso aos recursos no seu VNet, mas não concede acesso privado à sua aplicação a partir do VNet. O acesso ao site privado refere-se a tornar uma aplicação acessível apenas a partir de uma rede privada, como por exemplo dentro de uma rede virtual Azure. A VNet Integration é usada apenas para fazer chamadas de saída da sua aplicação para o seu VNet. A funcionalidade de Integração VNet comporta-se de forma diferente quando é usada com VNet na mesma região e com VNet noutras regiões. A funcionalidade de Integração VNet tem duas variações:

* **Integração VNet Regional**: Quando se conecta a redes virtuais do Azure Resource Manager na mesma região, deve ter uma subnet dedicada no VNet com o qual está a integrar.
* **Integração VNet exigida**por Gateway : Quando se conecta à VNet noutras regiões ou a uma rede virtual clássica na mesma região, precisa de um gateway de rede virtual Azure aprovisionado no Alvo VNet.

As funcionalidades de Integração VNet:

* Exija um plano de preços Standard, Premium, PremiumV2 ou Elástico Premium.
* Suporte TCP e UDP.
* Trabalhe com aplicações do Azure App Service e aplicações de função.

Há algumas coisas que a VNet Integração não suporta, como:

* Montando uma unidade.
* Integração de Diretório ativo.
* NetBIOS.

A VNet Integration exigida por gateway fornece acesso a recursos apenas no VNet alvo ou em redes ligadas ao alvo VNet com peering ou VPNs. A VNet Integration exigida por Gateway não permite o acesso aos recursos disponíveis em ligações Azure ExpressRoute ou trabalha com pontos finais de serviço.

Independentemente da versão utilizada, a VNet Integration dá à sua aplicação acesso aos recursos no seu VNet, mas não concede acesso privado à sua aplicação a partir do VNet. O acesso ao site privado refere-se a tornar a sua aplicação acessível apenas a partir de uma rede privada, como por exemplo dentro de um Azure VNet. A VNet Integration é apenas para fazer chamadas de saída da sua aplicação para o seu VNet.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
