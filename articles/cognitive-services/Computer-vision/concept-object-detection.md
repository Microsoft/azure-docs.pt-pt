---
title: Deteção de objetos - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a funcionalidade de deteção de objetos da API de Visão Computacional - utilização e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4269209017ecc0afa740bc3ed56cbdcbd915201e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533847"
---
# <a name="detect-common-objects-in-images"></a>Detetar objetos comuns em imagens

A deteção de objetos é semelhante à [marcação,](concept-tagging-images.md)mas a API devolve as coordenadas da caixa de delimitação (em pixels) para cada objeto encontrado. Por exemplo, se uma imagem contiver um cão, gato e pessoa, a operação Detect listará esses objetos juntamente com as suas coordenadas na imagem. Pode utilizar esta funcionalidade para processar as relações entre os objetos numa imagem. Também permite determinar se existem múltiplas instâncias da mesma etiqueta numa imagem.

A API deteta tags com base nos objetos ou seres vivos identificados na imagem. Atualmente, não existe uma relação formal entre a taxonomia da marcação e a taxonomia de deteção de objetos. A nível conceptual, a API deteta apenas objetos e seres vivos, enquanto a API tag também pode incluir termos contextuais como "interior", que não pode ser localizado com caixas de delimitação.

## <a name="object-detection-example"></a>Exemplo de deteção de objetos

A seguinte resposta JSON ilustra o que a Visão Computacional retorna ao detetar objetos na imagem de exemplo.

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

* Os objetos geralmente não são detetados se forem pequenos (menos de 5% da imagem).
* Os objetos geralmente não são detetados se forem organizados de perto (uma pilha de placas, por exemplo).
* Os objetos não são diferenciados por nomes de marcas ou produtos (diferentes tipos de refrigerantes numa prateleira de loja, por exemplo). No entanto, pode obter informações da marca a partir de uma imagem utilizando a funcionalidade [de deteção da Marca.](concept-brand-detection.md)

## <a name="use-the-api"></a>Utilizar a API

A funcionalidade de deteção de objetos faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Você pode chamar esta API através de um SDK nativo ou através de chamadas REST. Incluir `Objects` no parâmetro de consulta **visualFeatures.** Em seguida, quando tiver a resposta JSON completa, basta analisar a corda para o conteúdo da `"objects"` secção.

* [Quickstart: Computer Vision REST API ou bibliotecas de clientes](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
