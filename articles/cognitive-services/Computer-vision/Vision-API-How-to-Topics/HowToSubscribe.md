---
title: Obter chaves de subscrição - de imagem digitalizada
titlesuffix: Azure Cognitive Services
description: Saiba como obter chaves de subscrição para chamadas para a API de imagem digitalizada nos serviços cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 03e519520d4a956a5c9690dc1327089505aafced
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120861"
---
# <a name="how-to-obtain-subscription-keys"></a>Como obter chaves de subscrição

Serviços de visão do computador exigem chaves de subscrição especial. Todas as chamadas para a API de Imagem Digitalizada necessitam de uma chave de subscrição. Esta chave tem de ser transmitida através de um parâmetro de cadeia de consulta ou pode ser especificada no cabeçalho do pedido.

Para se inscrever para chaves de subscrição, veja [subscrições](https://azure.microsoft.com/try/cognitive-services/). É gratuito para se inscrever. Os preços para estes serviços são sujeitos a alterações.

> [!NOTE]
> As chaves de subscrição são válidas para apenas um destes [regiões do Microsoft Azure](https://azure.microsoft.com/regions/). 

| Região | Endereço |
|---|---|
| EUA Oeste | westus.API.cognitive.microsoft.com |
| EUA Leste 2 | eastus2.api.cognitive.microsoft.com |
| EUA Centro-Oeste | westcentralus.api.cognitive.microsoft.com |
| Europa Ocidental | westeurope.api.cognitive.microsoft.com |
| Sudeste Asiático | southeastasia.api.cognitive.microsoft.com |

Se se inscrever com a avaliação gratuita de imagem digitalizada, as chaves de subscrição são válidas para o **westcentral** região (`https://westcentralus.api.cognitive.microsoft.com/`). Que é o caso mais comum. No entanto, se se inscrever para imagem digitalizada com o Microsoft Azure da conta através da [ https://azure.microsoft.com/ ](https://azure.microsoft.com/) Web site, deve especificar a região para a versão de avaliação na lista anterior de regiões.

Por exemplo, se inscreva-se de imagem digitalizada com sua conta do Microsoft Azure e é especificar `westus` da sua região, tem de utilizar o `westus` região para suas chamadas de REST API (`https://westus.api.cognitive.microsoft.com/`).

Se se esquecer a região para a sua chave de subscrição depois de obter a chave de avaliação, pode encontrar a sua região na [ https://azure.microsoft.com/try/cognitive-services/my-apis/ ](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Links relacionados:

* [Opções de preços para APIs serviços cognitivos do Azure](https://azure.microsoft.com/pricing/details/cognitive-services/)
