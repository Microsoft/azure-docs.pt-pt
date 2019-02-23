---
title: Deteção de marca - o de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados a deteção de marca/logótipo a utilizar a API de imagem digitalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: 7bfda764c418258a7bc555697530214240a5f0d3
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734324"
---
# <a name="brand-detection"></a>Deteção de marca

Deteção de marca é um modo especializado de [deteção de objeto](concept-object-detection.md) que utiliza uma base de dados de milhares de logotipos global para identificar marcas comerciais em imagens ou vídeos. Pode utilizar esta funcionalidade, por exemplo, para descobrir quais marcas são mais populares nas redes sociais ou mais predominante no posicionamento de produto do suporte de dados.

O serviço de imagem digitalizada Deteta se existem logótipos de marca numa determinada imagem; Se assim for, ele retorna o nome de marca, uma pontuação de confiança e as coordenadas de uma caixa delimitadora em torno do logótipo.

A base de dados incorporada logótipo abrange marcas populares no electronics de consumidor, vestuário e muito mais. Se achar que a marca que procura não for detetada pelo serviço de visão do computador, pode ficar mais bem servidas criando e treinamento de seu próprio detetor logótipo a utilizar o [visão personalizada](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) serviço.

## <a name="brand-detection-example"></a>Exemplo de deteção de marca

As respostas JSON seguintes ilustram o que o de imagem digitalizada devolve quando a detecção de marcas as imagens de exemplo.

![Um sweatshirt cinza com uma etiqueta da Microsoft e o logótipo no mesmo](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
Em alguns casos, o detetor de marca selecionará a imagem do logótipo e o nome da marca stylized como dois logótipos separados.

![Um Azure red shirt com uma etiqueta da Microsoft e o logótipo no mesmo](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Utilize a API
A funcionalidade de deteção de marca faz parte do [analisar imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API. Pode chamar esta API através de um SDK nativo ou por meio de chamadas REST. Quando receber a resposta JSON completa, simplesmente analisar a cadeia para o conteúdo do `"brands"` secção.

* [Quickstart: Analisar uma imagem (SDK do .NET)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Quickstart: Analisar uma imagem (REST API)](./quickstarts/csharp-analyze.md)
