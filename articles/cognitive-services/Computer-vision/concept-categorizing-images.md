---
title: Categorizar imagens - o de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a funcionalidade de categorização de imagem da API de visão do computador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 257da397e11843ee96e93f7b3e9bc5ada29822cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203355"
---
# <a name="categorize-images-by-subject-matter"></a>Categorizar imagens no assunto

Para além das etiquetas e uma descrição, imagem digitalizada devolve as categorias baseada em taxonomia, detetadas numa imagem. Ao contrário das etiquetas, categorias estão organizadas numa hierarquia de hereditary pai/filho e existirem menos um deles (86, em vez de milhares de etiquetas). Todos os nomes de categoria estão em inglês. Categorização pode ser feita por si só ou em conjunto com o modelo de etiquetas mais recente.

## <a name="the-86-category-concept"></a>O conceito de 86 categorias

Imagem digitalizada pode categorizar uma imagem amplamente ou especificamente, utilizando a lista de 86 categorias no diagrama seguinte. Para obter a taxonomia completa no formato de texto, veja [Taxonomia de 86 Categorias](category-taxonomy.md).

![listas agrupadas de todas as categorias a taxonomia da categoria](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Exemplos de categorização de imagem

A resposta JSON seguinte ilustra o que o de imagem digitalizada devolve quando categorizar a imagem de exemplo com base nos seus recursos do visual.

![Uma mulher no teto de um apartamento](./Images/woman_roof.png)

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

A tabela a seguir ilustra um conjunto típico de imagem e a categoria devolvido pela visão do computador para cada imagem.

| Image | Category |
|-------|----------|
| ![Quatro pessoas colocadas em conjunto como uma família](./Images/family_photo.png) | people_group |
| ![Um lançamento será feito num campo grassy](./Images/cute_dog.png) | animal_dog |
| ![Uma pessoa seguindo um uma pedra das regiões montanhosas em sunset](./Images/mountain_vista.png) | outdoor_mountain |
| ![Uma pilha de funções de pão numa tabela](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Passos Seguintes

Conheça os conceitos [marcação de imagens](concept-tagging-images.md) e [descrevendo imagens](concept-describing-images.md).
