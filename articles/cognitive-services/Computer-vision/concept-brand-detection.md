---
title: Deteção de marcas - Visão Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo discute um modo especializado de deteção de objetos; deteção de marca e/ou logotipo utilizando a API computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80131744"
---
# <a name="detect-popular-brands-in-images"></a>Detete marcas populares em imagens

A deteção de marcas é um modo especializado de deteção de [objetos](concept-object-detection.md) que utiliza uma base de dados de milhares de logótipos globais para identificar marcas comerciais em imagens ou vídeo. Pode utilizar esta funcionalidade, por exemplo, para descobrir quais as marcas mais populares nas redes sociais ou mais predominantes na colocação de produtos de mídia.

O serviço Computer Vision deteta se existem logótipos da marca numa determinada imagem; em caso afirmativo, devolve o nome da marca, uma pontuação de confiança, e as coordenadas de uma caixa de delimitação em torno do logotipo.

A base de dados de logotipo incorporado abrange marcas populares em eletrónica de consumo, vestuário e muito mais. Se descobrir que a marca que procura não é detetada pelo serviço Computer Vision, poderá ser melhor servida a criar e treinar o seu próprio detetor de logótipos utilizando o serviço [Custom Vision.](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="brand-detection-example"></a>Exemplo de deteção de marcas

As seguintes respostas da JSON ilustram o que a Computer Vision retorna ao detetar marcas nas imagens de exemplo.

![Uma camisa vermelha com uma etiqueta e logotipo da Microsoft](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

Em alguns casos, o detetor da marca irá captar tanto a imagem do logotipo como a marca estilizada como dois logótipos separados.

![Uma camisola cinzenta com uma etiqueta e logotipo da Microsoft](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Utilizar a API

A funcionalidade de deteção da marca faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Você pode chamar isto de API através de um SDK nativo ou através de chamadas REST. Incluir `Brands` no parâmetro de consulta **visualFeatures.** Em seguida, quando obtém a resposta completa da JSON, `"brands"` basta analisar a corda para o conteúdo da secção.

* [Quickstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analise uma imagem (REST API)](./quickstarts/csharp-analyze.md)
