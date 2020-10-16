---
title: Deteção de marca - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Este artigo discute um modo especializado de deteção de objetos; deteção de marca e/ou logotipo utilizando a API de Visão De Computador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80131744"
---
# <a name="detect-popular-brands-in-images"></a>Detetar marcas populares em imagens

A deteção de marcas é um modo especializado de deteção de [objetos](concept-object-detection.md) que utiliza uma base de dados de milhares de logótipos globais para identificar marcas comerciais em imagens ou vídeos. Pode utilizar esta funcionalidade, por exemplo, para descobrir quais as marcas mais populares nas redes sociais ou as mais predominantes na colocação de produtos de mídia.

O serviço de Visão Computacional deteta se existem logótipos da marca numa determinada imagem; em caso afirmativo, devolve a marca, uma pontuação de confiança, e as coordenadas de uma caixa de delimitação em torno do logotipo.

A base de dados de logotipo incorporada abrange marcas populares em eletrónica de consumo, vestuário e muito mais. Se descobrir que a marca que procura não é detetada pelo serviço de Visão De Computador, poderá ser melhor servido criando e treinando o seu próprio detetor de logotipo utilizando o serviço [Custom Vision.](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="brand-detection-example"></a>Exemplo de deteção de marca

As seguintes respostas JSON ilustram o que a Visão Computacional retorna ao detetar marcas nas imagens de exemplo.

![Uma camisa vermelha com uma etiqueta microsoft e logotipo nele](./Images/red-shirt-logo.jpg)

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

Em alguns casos, o detetor de marcas irá captar tanto a imagem do logotipo como a marca estilizada como dois logotipos separados.

![Uma camisola cinzenta com uma etiqueta microsoft e logotipo nele](./Images/gray-shirt-logo.jpg)

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

A funcionalidade de deteção da marca faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Você pode chamar esta API através de um SDK nativo ou através de chamadas REST. Incluir `Brands` no parâmetro de consulta **visualFeatures.** Em seguida, quando tiver a resposta JSON completa, basta analisar a corda para o conteúdo da `"brands"` secção.

* [Quickstart: Visão por Computador .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analisar uma imagem (REST API)](./quickstarts/csharp-analyze.md)
