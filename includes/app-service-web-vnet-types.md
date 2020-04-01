---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419583"
---
* Os sistemas multiarrendatários que suportam toda a gama de planos de preços exceto isolados.
* O App Service Environment, que se implanta na sua rede virtual e suporta aplicações de planos de preços isolados.

A funcionalidade de Integração VNet é utilizada em aplicações multiarrendatárias. Se a sua aplicação estiver no [App Service Environment][ASEintro], então já se encontra numa rede virtual e não necessita da utilização da funcionalidade de Integração VNet para chegar a recursos na mesma rede virtual. Para obter mais informações sobre todas as funcionalidades de networking, consulte as funcionalidades de [rede do Serviço de Aplicações.][Networkingfeatures]

A VNet Integration dá à sua aplicação acesso aos recursos da sua rede virtual, mas não concede acesso privado à sua aplicação a partir da rede virtual. O acesso ao site privado refere-se a tornar uma aplicação acessível apenas a partir de uma rede privada, como por exemplo dentro de uma rede virtual Azure. A VNet Integration é usada apenas para fazer chamadas de saída da sua aplicação para a sua rede virtual. A funcionalidade de Integração VNet comporta-se de forma diferente quando é utilizada com redes virtuais na mesma região e com redes virtuais noutras regiões. A funcionalidade de Integração VNet tem duas variações:

* **Integração VNet Regional**: Quando se conecta a redes virtuais do Azure Resource Manager na mesma região, deve ter uma subrede dedicada na rede virtual com a qual está a integrar.
* **Integração VNet exigida**por Gateway : Quando se conecta a redes virtuais de outras regiões ou a uma rede virtual clássica na mesma região, precisa de um gateway de rede virtual Azure aprovisionado na rede virtual alvo.

As funcionalidades de Integração VNet:

* Exija um plano de preços Standard, Premium, PremiumV2 ou Elástico Premium.
* Suporte TCP e UDP.
* Trabalhe com aplicações do Azure App Service e aplicações de função.

Há algumas coisas que a VNet Integração não suporta, como:

* Montando uma unidade.
* Integração de Diretório ativo.
* NetBIOS.

A VNet Integration exigida por Gateway fornece acesso a recursos apenas na rede virtual alvo ou em redes ligadas à rede virtual alvo com peering ou VPNs. A VNet Integration exigida por Gateway não permite o acesso aos recursos disponíveis em ligações Azure ExpressRoute ou trabalha com pontos finais de serviço.

Independentemente da versão utilizada, a VNet Integration dá à sua aplicação acesso aos recursos da sua rede virtual, mas não concede acesso privado à sua aplicação a partir da rede virtual. O acesso ao site privado refere-se a tornar a sua aplicação acessível apenas a partir de uma rede privada, como por exemplo dentro de uma rede virtual Azure. A VNet Integration destina-se apenas a fazer chamadas de saída da sua aplicação para a sua rede virtual.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
