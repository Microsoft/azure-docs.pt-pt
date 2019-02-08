---
title: Aplicar etiquetas de conteúdo para imagens - o de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com as funcionalidade da API de imagem digitalizada de marcação de imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f5013c83f3575e7e1fbaa6dff614e4679abf09b6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882238"
---
# <a name="applying-content-tags-to-images"></a>Aplicação de conteúdo de marcas a imagens

Imagem digitalizada devolve etiquetas com base em milhares de objetos reconhecíveis, seres vivos, paisagens e ações. Quando as etiquetas são ambíguas ou não são do conhecimento geral, a resposta da API fornece "sugestões" para clarificar o significado da etiqueta no contexto de um cenário conhecido. As etiquetas não são organizadas como uma taxonomia e não existem hierarquias de herança. Uma coleção de etiquetas de conteúdos é a base da "descrição" de uma imagem apresentada como um idioma legível por humanos e formatada em frases completas. Tenha em atenção que, neste momento, o inglês é o único idioma suportado para a descrição de imagens.

Depois de carregar uma imagem ou especificar um URL de imagem, algoritmos de imagem digitalizada saída etiquetas com base em objetos, seres vivos e ações identificadas na imagem. As etiquetas não se limitam ao tema principal, como por exemplo uma pessoa em primeiro plano, mas também incluem o cenário (interior ou exterior), o mobiliário, ferramentas, plantas, animais, acessórios, gadgets, etc.

## <a name="image-tagging-example"></a>Exemplo de marcação de imagem

A resposta JSON seguinte ilustra o que o de imagem digitalizada devolve quando as funcionalidades visual detetadas na imagem de exemplo de identificação.

![Uma casa azul e o front-yard](./Images/house_yard.png).

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

## <a name="next-steps"></a>Passos Seguintes

Conheça os conceitos [categorizar imagens](concept-categorizing-images.md) e [descrevendo imagens](concept-describing-images.md).
