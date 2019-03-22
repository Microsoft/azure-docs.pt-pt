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
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 07fdaa22532f48cc39b6c524d85fdfe625f8b80c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337144"
---
# <a name="categorize-images"></a>Categorizar imagens

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

| Imagem | Categoria |
|-------|----------|
| ![Quatro pessoas colocadas em conjunto como uma família](./Images/family_photo.png) | people_group |
| ![Um lançamento será feito num campo grassy](./Images/cute_dog.png) | animal_dog |
| ![Uma pessoa seguindo um uma pedra das regiões montanhosas em sunset](./Images/mountain_vista.png) | outdoor_mountain |
| ![Uma pilha de funções de pão numa tabela](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Passos Seguintes

Conheça os conceitos [marcação de imagens](concept-tagging-images.md) e [descrevendo imagens](concept-describing-images.md).
