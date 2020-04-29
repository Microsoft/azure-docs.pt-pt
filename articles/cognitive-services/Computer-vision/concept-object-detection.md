---
title: Deteção de objetos - Visão computorizada
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a funcionalidade de deteção de objetos da API computer Vision - utilização e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80131661"
---
# <a name="detect-common-objects-in-images"></a>Detetar objetos comuns em imagens

A deteção de objetos é semelhante à [marcação,](concept-tagging-images.md)mas a API devolve as coordenadas da caixa de delimitação (em pixels) para cada objeto encontrado. Por exemplo, se uma imagem contiver um cão, um gato e uma pessoa, a operação Detect listará esses objetos juntamente com as suas coordenadas na imagem. Pode utilizar esta funcionalidade para processar as relações entre os objetos numa imagem. Também permite determinar se existem múltiplos casos da mesma etiqueta numa imagem.

A API deteta etiquetas com base nos objetos ou seres vivos identificados na imagem. Atualmente, não existe uma relação formal entre a taxonomia de marcação e a taxonomia de deteção de objetos. A nível conceptual, a API deteta apenas objetos e seres vivos, enquanto a Marca API também pode incluir termos contextuais como "indoor", que não podem ser localizados com caixas de limitadores.

## <a name="object-detection-example"></a>Exemplo de deteção de objetos

A resposta json seguinte ilustra o que a Visão computacional retorna ao detetar objetos na imagem do exemplo.

![Uma mulher usando um dispositivo Microsoft Surface em uma cozinha](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Limitações

É importante notar as limitações da deteção de objetos para que possa evitar ou mitigar os efeitos de falsos negativos (objetos perdidos) e detalhes limitados.

* Os objetos geralmente não são detetados se são pequenos (menos de 5% da imagem).
* Geralmente, os objetos não são detetados se estiverem dispostos em conjunto (uma pilha de placas, por exemplo).
* Os objetos não são diferenciados por nomes de marca ou produto (diferentes tipos de refrigerantes numa prateleira de loja, por exemplo). No entanto, pode obter informações da marca a partir de uma imagem utilizando a funcionalidade de deteção da [Marca.](concept-brand-detection.md)

## <a name="use-the-api"></a>Utilizar a API

A funcionalidade de deteção de objetos faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Você pode chamar isto de API através de um SDK nativo ou através de chamadas REST. Incluir `Objects` no parâmetro de consulta **visualFeatures.** Em seguida, quando obtém a resposta completa da JSON, `"objects"` basta analisar a corda para o conteúdo da secção.

* [Quickstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analise uma imagem (REST API)](./quickstarts/csharp-analyze.md)