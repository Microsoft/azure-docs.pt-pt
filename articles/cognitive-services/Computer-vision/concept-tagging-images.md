---
title: Marcas de conteúdo-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Aprenda os conceitos relacionados ao recurso de marcação de imagens do API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ad01751f7f7c573352c8fd15cc0f8cc2ebbbe700
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945112"
---
# <a name="applying-content-tags-to-images"></a>Aplicação de etiquetas de conteúdo a imagens

Pesquisa Visual Computacional retorna marcas com base em milhares de objetos reconhecíveis, seres de vida, cenário e ações. Quando as etiquetas são ambíguas ou não são do conhecimento geral, a resposta da API fornece "sugestões" para clarificar o significado da etiqueta no contexto de um cenário conhecido. As etiquetas não são organizadas como uma taxonomia e não existem hierarquias de herança. Uma coleção de etiquetas de conteúdos é a base da "descrição" de uma imagem apresentada como um idioma legível por humanos e formatada em frases completas. Tenha em atenção que, neste momento, o inglês é o único idioma suportado para a descrição de imagens.

Depois de carregar uma imagem ou de especificar uma URL de imagem, Pesquisa Visual Computacional as marcas de saída dos algoritmos com base nos objetos, nos seres de vida e nas ações identificadas na imagem. As etiquetas não se limitam ao tema principal, como por exemplo uma pessoa em primeiro plano, mas também incluem o cenário (interior ou exterior), o mobiliário, ferramentas, plantas, animais, acessórios, gadgets, etc.

## <a name="image-tagging-example"></a>Exemplo de marcação de imagem

A resposta JSON a seguir ilustra o que Pesquisa Visual Computacional retorna ao marcar os recursos visuais detectados na imagem de exemplo.

![Uma casa azul e o jardim frontal](./Images/house_yard.png).

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

Aprenda conceitos sobre como [categorizar imagens](concept-categorizing-images.md) e [descrever imagens](concept-describing-images.md).
