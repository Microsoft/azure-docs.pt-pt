---
title: Deteção de objetos - o de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados a deteção de objetos com a API de imagem digitalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: cc5b40b6a800ff185c6c52652435b558fabe091f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57873891"
---
# <a name="object-detection"></a>Deteção de objetos

Deteção de objetos é semelhante à [marcação](concept-tagging-images.md), mas a API devolve as coordenadas da caixa delimitadora (em pixéis) para cada objeto encontrado. Por exemplo, se uma imagem contiver um cachorro, gato e person, a operação de deteção irá listar esses objetos, juntamente com suas coordenadas na imagem. Pode utilizar esta funcionalidade para processar as relações entre os objetos numa imagem. Ele também permite determinar se existem várias instâncias da mesma marca numa imagem.

A API de detectar se aplica etiquetas com base nos objetos ou vivos identificados na imagem. Neste momento, não existe nenhuma relação formal entre a taxonomia de etiquetagem e a taxonomia de deteção de objeto. Num nível conceitual, a API de detetar apenas localiza objetos e vivos, enquanto a API de marca também pode incluir contextuais termos como "fechado", que não podem ser localizados com delimitação de caixas.

## <a name="object-detection-example"></a>Exemplo de deteção de objeto

A resposta JSON seguinte ilustra o que o de imagem digitalizada devolve quando detetar objetos na imagem de exemplo.

![Uma mulher com um dispositivo Microsoft Surface numa cozinha](./Images/windows-kitchen.jpg)

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

É importante ter em conta as limitações de deteção de objetos para que possa evitar ou atenuar os efeitos de falsos negativos (objetos em falta) e os detalhes limitados.

* Objetos geralmente não são detetados que estejam pequeno (menos de 5% da imagem).
* Objetos geralmente não são detetados se são dispostos em conjunto (uma pilha de pratos, por exemplo).
* Objetos não são diferenciados pela marca ou nomes de produto (diferentes tipos de sodas em prateleiras da loja, por exemplo). No entanto, pode obter informações de marca partir de uma imagem com o [sua marca deteção](concept-brand-detection.md) funcionalidade.

## <a name="use-the-api"></a>Utilize a API

A funcionalidade de deteção de objeto é parte da [analisar imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API. Pode chamar esta API através de um SDK nativo ou por meio de chamadas REST. Quando receber a resposta JSON completa, analisar a cadeia para o conteúdo do `"objects"` secção.

* [Quickstart: Analisar uma imagem (SDK do .NET)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Quickstart: Analisar uma imagem (REST API)](./quickstarts/csharp-analyze.md)