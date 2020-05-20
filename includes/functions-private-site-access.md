---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648805"
---
O acesso ao site privado refere-se a tornar a sua aplicação acessível apenas a partir de uma rede privada, como uma rede virtual Azure.

* O acesso ao site privado está disponível nos planos [Premium](../articles/azure-functions/functions-premium-plan.md), [Consumption](../articles/azure-functions/functions-scale.md#consumption-plan)e [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) quando os pontos finais do serviço estão configurados.
    * Os pontos finais do serviço podem ser configurados numa base por app em **funções de Plataforma de**  >  **Configuração de**  >  **Configuração De Regra**de Adição de Restrições de Acesso  >  **Add Rule**Configurados . As redes virtuais podem agora ser selecionadas como um tipo de regra.
    * Para mais informações, consulte [pontos finais](../articles/virtual-network/virtual-network-service-endpoints-overview.md)do serviço de rede Virtual .
    * Tenha em mente que, com pontos finais de serviço, a sua função ainda tem acesso total à internet, mesmo com a integração virtual da rede configurada.
* O acesso ao site privado também está disponível dentro de um App Service Environment que está configurado com um equilibrista de carga interna (ILB). Para mais informações, consulte [Criar e utilizar um equilibrador de carga interno com um Ambiente de Serviço de Aplicações](../articles/app-service/environment/create-ilb-ase.md).

Para saber como configurar o acesso ao site privado, consulte o acesso privado ao [site do Azure Functions](../articles/azure-functions/functions-create-private-site-access.md).