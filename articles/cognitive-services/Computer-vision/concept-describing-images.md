---
title: Descrições de imagem - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a característica de descrição da imagem da API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7d2b87710b2590f9bcf511ec0cbd5637913fb5c4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538449"
---
# <a name="describe-images-with-human-readable-language"></a>Descreva imagens com linguagem legível pelo homem

A Visão Computacional pode analisar uma imagem e gerar uma frase legível pelo homem que descreve o seu conteúdo. O algoritmo realmente devolve várias descrições com base em diferentes características visuais, e cada descrição recebe uma pontuação de confiança. A produção final é uma lista de descrições ordenadas da mais alta à menor confiança.

## <a name="image-description-example"></a>Exemplo de descrição da imagem

A seguinte resposta JSON ilustra o que a Visão Computacional retorna ao descrever a imagem do exemplo com base nas suas características visuais.

![Uma imagem a preto e branco de edifícios em Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Utilizar a API

A funcionalidade de descrição da imagem faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Você pode chamar esta API através de um SDK nativo ou através de chamadas REST. Incluir `Description` no parâmetro de consulta **visualFeatures.** Em seguida, quando tiver a resposta JSON completa, basta analisar a corda para o conteúdo da `"description"` secção.

* [Quickstart: Visão por Computador .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analisar uma imagem (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Passos seguintes

Conheça os conceitos relacionados de [marcação](concept-tagging-images.md) de imagens e [categorização de imagens.](concept-categorizing-images.md)
