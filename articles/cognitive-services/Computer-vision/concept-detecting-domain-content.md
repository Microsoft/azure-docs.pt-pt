---
title: Detetar conteúdos específicos de domínio - de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Saiba como especificar um domínio de categorização de imagem para devolver informações mais detalhadas sobre uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 92859667e1dc53b9c6ca9e46a2db1c6dc335ae37
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339016"
---
# <a name="detect-domain-specific-content"></a>Detetar conteúdos específica do domínio

Além disso categorização a etiquetagem e de alto nível, de imagem digitalizada também suporta a análise de específicas de domínio adicional através de modelos que foram treinados em dados especializados.

Existem duas formas de utilizar os modelos de domínios específicos: por conta própria (análise de âmbito) ou como um aprimoramento a funcionalidade de categorização.

### <a name="scoped-analysis"></a>Análise de âmbito

Pode analisar uma imagem usando apenas o modelo de específicas do domínio escolhido, chamando o [modelos /\<modelo\>/analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API.

Segue-se um exemplo de resposta JSON devolvido pelos **modelos/celebridades/analisar** API para a imagem específica:

![Satya Nadella permanente](./images/satya.jpeg)

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

### <a name="enhanced-categorization-analysis"></a>Análise de categorização aprimorada

Também pode utilizar modelos de domínios específicos para complementar a análise de imagens gerais. Pode fazê-lo como parte da [categorização de alto nível](concept-categorizing-images.md) especificando os modelos de domínios específicos na *detalhes* parâmetro do [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) chamada à API.

Neste caso, o classificador de taxonomia da categoria 86 denomina-se em primeiro lugar. Se nenhuma das categorias detetadas tiver um modelo de domínio específico correspondente, a imagem é transmitida por meio de que o modelo também e os resultados são adicionados.

A resposta JSON seguinte mostra a análise de como domínio específico pode ser incluído como o `detail` nó numa análise mais ampla de categorização.

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

## <a name="list-the-domain-specific-models"></a>Listar os modelos de domínios específicos

Atualmente, a imagem digitalizada suporta os seguintes modelos de domínios específicos:

| Name | Descrição |
|------|-------------|
| celebridades | Reconhecimento de celebridades, suportado para imagens classificada no `people_` categoria |
| Pontos de referência | Reconhecimento de marcos, suportado para imagens classificada no `outdoor_` ou `building_` categorias |

Chamar o [modelos](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API irá devolver estas informações, juntamente com as categorias às quais pode aplicar a cada modelo:

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

## <a name="next-steps"></a>Passos Seguintes

Conheça os conceitos [categorizar imagens](concept-categorizing-images.md).
