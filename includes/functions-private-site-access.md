---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648805"
---
O acesso ao site privado refere-se a tornar a sua aplicação acessível apenas a partir de uma rede privada, como uma rede virtual Azure.

* O acesso ao site privado está disponível nos planos [Premium,](../articles/azure-functions/functions-premium-plan.md) [Consumo](../articles/azure-functions/functions-scale.md#consumption-plan)e [Serviço de Aplicações](../articles/azure-functions/functions-scale.md#app-service-plan) quando os pontos finais do serviço estão configurados.
    * Os pontos finais de serviço podem ser configurados numa base por aplicação na **Plataforma, as funcionalidades**  >  **Networking**  >  **Configure Access Restrictions**  >  **Add Rule**. As redes virtuais podem agora ser selecionadas como um tipo de regra.
    * Para obter mais informações, consulte [os pontos finais do serviço de rede Virtual.](../articles/virtual-network/virtual-network-service-endpoints-overview.md)
    * Tenha em mente que, com os pontos finais do serviço, a sua função ainda tem acesso total à internet, mesmo com integração de rede virtual configurada.
* O acesso ao site privado também está disponível dentro de um Ambiente de Serviço de Aplicações que está configurado com um equilibrador de carga interno (ILB). Para obter mais informações, consulte [Criar e utilizar um equilibrador de carga interno com um Ambiente de Serviço de Aplicações.](../articles/app-service/environment/create-ilb-ase.md)

Para aprender a configurar o acesso ao site privado, consulte [o acesso ao site privado Do Azure Functions.](../articles/azure-functions/functions-create-private-site-access.md)