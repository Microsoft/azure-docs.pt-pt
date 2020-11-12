---
title: Conteúdo específico do domínio - Visão de Computador
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
ms.openlocfilehash: 5cd872d66088e165bfc8356ab6d96a0a6135a0e0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538313"
---
# <a name="detect-domain-specific-content"></a>Detetar conteúdo específico de um domínio

Além da marcação e categorização de alto nível, a Computer Vision também suporta análises específicas de domínio, utilizando modelos treinados em dados especializados.

Existem duas formas de utilizar os modelos específicos do domínio: por si só (análise de âmbito) ou como um melhoramento para a característica de categorização.

### <a name="scoped-analysis"></a>Análise scoped

Pode analisar uma imagem utilizando apenas o modelo específico de domínio escolhido, chamando os [Modelos/ \<model\> /Analisar](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) a API.

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

Também pode utilizar modelos específicos de domínio para complementar a análise geral da imagem. Fá-lo como parte de [uma categorização de alto nível](concept-categorizing-images.md) especificando modelos específicos de domínio no parâmetro de *detalhes* da chamada [API de análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)

Neste caso, o classificador de taxonomia de 86 categorias é chamado primeiro. Se alguma das categorias detetadas tiver um modelo específico de domínio correspondente, a imagem também é transmitida através desse modelo e os resultados são adicionados.

A seguinte resposta JSON mostra como a análise específica do domínio pode ser incluída como nó `detail` numa análise de categorização mais ampla.

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

## <a name="list-the-domain-specific-models"></a>Listar os modelos específicos do domínio

Atualmente, a Visão De Computador suporta os seguintes modelos específicos de domínio:

| Nome | Descrição |
|------|-------------|
| celebridades | Reconhecimento de celebridades, suportado para imagens classificadas na `people_` categoria |
| marcos | Reconhecimento de marco, suportado para imagens classificadas nas `outdoor_` categorias ou `building_` categorias |

Chamando a API [de modelos,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20e) a API devolverá esta informação juntamente com as categorias às quais cada modelo pode aplicar-se:

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

Aprenda conceitos sobre [categorizar imagens.](concept-categorizing-images.md)
