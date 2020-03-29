---
title: Conteúdo específico do domínio - Visão computacional
titleSuffix: Azure Cognitive Services
description: Saiba especificar um domínio de categorização de imagem para devolver informações mais detalhadas sobre uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68946328"
---
# <a name="detect-domain-specific-content"></a>Detetar conteúdo específico de um domínio

Além da marcação e categorização de alto nível, a Computer Vision também suporta uma análise mais específica do domínio utilizando modelos que foram treinados em dados especializados.

Existem duas formas de utilizar os modelos específicos do domínio: por si só (análise de âmbito) ou como um melhoramento para a função de categorização.

### <a name="scoped-analysis"></a>Análise de âmbito

Pode analisar uma imagem usando apenas o modelo específico de domínio escolhido, chamando os [\<Modelos/Modelo\>/Analisar](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API.

Segue-se uma resposta JSON de amostra devolvida pelos **modelos/celebridades/analisar** a API para a imagem dada:

![Satya Nadella de pé, sorrindo](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Análise de categorização melhorada

Também pode utilizar modelos específicos de domínio para complementar a análise geral da imagem. Faça-o como parte da [categorização de alto nível](concept-categorizing-images.md) especificando modelos específicos de domínio no parâmetro de *detalhes* da chamada [API de análise.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)

Neste caso, o classificador de taxonomia de 86 categorias é chamado primeiro. Se alguma das categorias detetadas tiver um modelo específico de domínio correspondente, a imagem também é transmitida através desse modelo e os resultados são adicionados.

A resposta json seguinte mostra como a análise `detail` específica do domínio pode ser incluída como o nó numa análise de categorização mais ampla.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Enumerar os modelos específicos do domínio

Atualmente, a Computer Vision suporta os seguintes modelos específicos de domínio:

| Nome | Descrição |
|------|-------------|
| celebridades | Reconhecimento de celebridades, suportado `people_` por imagens classificadas na categoria |
| marcos | Reconhecimento de marcos, suportado `outdoor_` para `building_` imagens classificadas nas categorias ou categorias |

A chamada para os [Modelos](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API devolverá esta informação juntamente com as categorias a que cada modelo pode aplicar:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre [categorização de imagens.](concept-categorizing-images.md)
