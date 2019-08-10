---
title: Categorização de imagem-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Aprenda os conceitos relacionados ao recurso de categorização de imagem do API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2b9b8da550d80b027da919ba0834e43e2c83d4b4
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945318"
---
# <a name="categorize-images-by-subject-matter"></a>Categorizar imagens por assunto

Além das marcas e uma descrição, Pesquisa Visual Computacional retorna as categorias baseadas em taxonomia detectadas em uma imagem. Ao contrário das marcas, as categorias são organizadas em uma hierarquia de hereditary pai/filho e há menos delas (86, em oposição a milhares de marcas). Todos os nomes de categoria estão em inglês. A categorização pode ser feita sozinha ou junto com o modelo de marcas mais recente.

## <a name="the-86-category-concept"></a>O conceito de 86 categorias

A visão computacional pode categorizar uma imagem de forma ampla ou específica, usando a lista de categorias 86 no diagrama a seguir. Para obter a taxonomia completa no formato de texto, veja [Taxonomia de 86 Categorias](category-taxonomy.md).

![Listas agrupadas de todas as categorias na taxonomia de categoria](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Exemplos de categorização de imagem

A resposta JSON a seguir ilustra o que Pesquisa Visual Computacional retorna ao categorizar a imagem de exemplo com base em seus recursos visuais.

![Uma mulher no telhado de uma construção de apartamento](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

A tabela a seguir ilustra um conjunto de imagens típico e a categoria retornada por Pesquisa Visual Computacional para cada imagem.

| Image | Category |
|-------|----------|
| ![Quatro pessoas se juntaram como uma família](./Images/family_photo.png) | people_group |
| ![Um filhote sentado em um campo de grama](./Images/cute_dog.png) | animal_dog |
| ![Uma pessoa em uma pedra de montanha no pôr no sol](./Images/mountain_vista.png) | outdoor_mountain |
| ![Uma pilha de funções de pão em uma tabela](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Passos Seguintes

Aprenda conceitos sobre como [Marcar imagens](concept-tagging-images.md) e [descrever imagens](concept-describing-images.md).
