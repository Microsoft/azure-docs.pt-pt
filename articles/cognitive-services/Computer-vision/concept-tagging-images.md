---
title: Etiquetas de conteúdo - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a característica de marcação de imagens da API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a75e55cd51890173b6733ab5f8c0f1bcbfeef0f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532980"
---
# <a name="applying-content-tags-to-images"></a>Aplicação de etiquetas de conteúdo a imagens

A Visão Computacional devolve etiquetas baseadas em milhares de objetos reconhecíveis, seres vivos, cenários e ações. Quando as etiquetas são ambíguas ou não são do conhecimento geral, a resposta da API fornece "sugestões" para clarificar o significado da etiqueta no contexto de um cenário conhecido. As etiquetas não são organizadas como uma taxonomia e não existem hierarquias de herança. Uma coleção de etiquetas de conteúdos é a base da "descrição" de uma imagem apresentada como um idioma legível por humanos e formatada em frases completas. Tenha em atenção que, neste momento, o inglês é o único idioma suportado para a descrição de imagens.

Depois de carregar uma imagem ou especificar um URL de imagem, os algoritmos de Imagem Digitalizada produzem etiquetas com base nos objetos, seres vivos e ações identificados na imagem. As etiquetas não se limitam ao tema principal, como por exemplo uma pessoa em primeiro plano, mas também incluem o cenário (interior ou exterior), o mobiliário, ferramentas, plantas, animais, acessórios, gadgets, etc.

## <a name="image-tagging-example"></a>Exemplo de marcação de imagem

A seguinte resposta JSON ilustra o que a Visão Computacional retorna ao marcar funcionalidades visuais detetadas na imagem de exemplo.

![Uma casa azul e o jardim da frente](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Utilizar a API

A função de marcação faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Você pode chamar esta API através de um SDK nativo ou através de chamadas REST. Incluir `Tags` no parâmetro de consulta **visualFeatures.** Em seguida, quando tiver a resposta JSON completa, basta analisar a corda para o conteúdo da `"tags"` secção.

* [Quickstart: Computer Vision REST API ou bibliotecas de clientes](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Passos seguintes

Conheça os conceitos relacionados de [categorizar imagens](concept-categorizing-images.md) e [descrever imagens.](concept-describing-images.md)
