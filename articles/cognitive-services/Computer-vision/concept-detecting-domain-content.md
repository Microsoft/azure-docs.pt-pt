---
title: Conteúdo específico do domínio-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Saiba como especificar um domínio de categorização de imagem para retornar informações mais detalhadas sobre uma imagem.
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
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946328"
---
# <a name="detect-domain-specific-content"></a>Detectar conteúdo específico de domínio

Além da marcação e categorização de alto nível, o Pesquisa Visual Computacional também dá suporte à análise específica de domínio adicional usando modelos que foram treinados em dados especializados.

Há duas maneiras de usar os modelos específicos de domínio: por si só (análise com escopo) ou como um aprimoramento para o recurso de categorização.

### <a name="scoped-analysis"></a>Análise com escopo

Você pode analisar uma imagem usando apenas o modelo específico de domínio escolhido chamando a API [/Analyze Models\>/\<modelo](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) .

A seguir está um exemplo de resposta JSON retornada pela API **Models/celebridades/Analyze** para a imagem determinada:

![Satya Nadella pé, sorrindo](./images/satya.jpeg)

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

### <a name="enhanced-categorization-analysis"></a>Análise de categorização avançada

Você também pode usar modelos específicos de domínio para complementar a análise de imagem geral. Você faz isso como parte da [categorização de alto nível](concept-categorizing-images.md) especificando modelos específicos de domínio no parâmetro *Details* da chamada de API de [análise](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .

Nesse caso, o classificador de taxonomia 86-category é chamado primeiro. Se qualquer uma das categorias detectadas tiver um modelo específico de domínio correspondente, a imagem também passará por esse modelo e os resultados serão adicionados.

A resposta JSON a seguir mostra como a análise específica de domínio pode ser incluída `detail` como o nó em uma análise de categorização mais ampla.

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

## <a name="list-the-domain-specific-models"></a>Listar os modelos específicos de domínio

Atualmente, Pesquisa Visual Computacional dá suporte aos seguintes modelos específicos de domínio:

| Nome | Descrição |
|------|-------------|
| celebridades | Reconhecimento de celebridade, com suporte para imagens classificadas na `people_` categoria |
| pontos | Reconhecimento de ponto de referência, com suporte para `outdoor_` imagens `building_` classificadas nas categorias ou |

Chamar a API de [modelos](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) retornará essas informações junto com as categorias às quais cada modelo pode ser aplicado:

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

Aprenda conceitos sobre a categorização de [imagens](concept-categorizing-images.md).
