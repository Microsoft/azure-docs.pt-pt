---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274684"
---
## <a name="azure-cognitive-service-resource-types"></a>Tipos de recursos de serviço cognitiva do Azure

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Você pode acessar os serviços cognitivas do Azure por meio de dois recursos diferentes: Um recurso de vários serviços ou um único serviço. Essas assinaturas permitem que você se conecte a um único serviço cognitiva ou a vários serviços cognitivas de uma só vez.

### <a name="multi-service-resource"></a>Recurso de vários serviços

Inscrevendo-se em um recurso de serviços cognitivas de vários serviços:
* Permite que você use um único recurso do Azure para a maioria dos serviços cognitivas do Azure.
* Você Obtém uma única chave que pode ser usada com vários serviços cognitivas do Azure.
* Consolida a cobrança dos serviços que você usa. Consulte [preços de serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter informações adicionais.

>[!WARNING]
> Neste momento, esses serviços **não** oferecem suporte a chaves de vários serviços: QnA Maker, serviços de fala, Visão Personalizada e detector de anomalias.

### <a name="single-service-resource"></a>Recurso de serviço único

Inscrevendo-se em um recurso de serviços cognitivas de serviço único:
* Permite usar um serviço de cognitiva especificado para o qual você cria o recurso (como Pesquisa Visual Computacional ou serviços de fala).
* Você Obtém uma chave específica para o serviço cognitiva para o qual cria um recurso.