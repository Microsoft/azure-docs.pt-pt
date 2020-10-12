---
title: Categorização de imagem - Visão computacional
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a característica de categorização de imagem da API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 477349f1addf71a30e8ecb179266d8eac5510887
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244755"
---
# <a name="categorize-images-by-subject-matter"></a>Categorizar imagens por assunto

Além de etiquetas e uma descrição, a Visão Computacional devolve as categorias baseadas em taxonomia detetadas numa imagem. Ao contrário das etiquetas, as categorias são organizadas numa hierarquia hereditária pai/filho, e há menos delas (86, ao contrário de milhares de tags). Todos os nomes da categoria estão em inglês. A categorização pode ser feita por si só ou ao lado do modelo de etiquetas mais recentes.

## <a name="the-86-category-concept"></a>O conceito de 86 categorias

A visão computacional pode categorizar uma imagem de forma ampla ou especifica, utilizando a lista de 86 categorias no diagrama seguinte. Para obter a taxonomia completa no formato de texto, veja [Taxonomia de 86 Categorias](category-taxonomy.md).

![Listas agrupadas de todas as categorias na categoria taxonomia](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Exemplos de categorização de imagem

A seguinte resposta JSON ilustra o que a Visão Computacional retorna ao classificar a imagem do exemplo com base nas suas características visuais.

![Uma mulher no telhado de um prédio de apartamentos](./Images/woman_roof.png)

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

A tabela seguinte ilustra um conjunto de imagem típico e a categoria devolvida pela Visão de Computador para cada imagem.

| Imagem | Categoria |
|-------|----------|
| ![Quatro pessoas posando juntas como uma família](./Images/family_photo.png) | people_group |
| ![Um cachorrinho sentado em um campo gramado](./Images/cute_dog.png) | animal_dog |
| ![Uma pessoa em pé em uma rocha de montanha ao pôr do sol](./Images/mountain_vista.png) | outdoor_mountain |
| ![Uma pilha de papéis de pão em uma mesa](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Utilizar a API

A funcionalidade de categorização faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Você pode chamar esta API através de um SDK nativo ou através de chamadas REST. Incluir `Categories` no parâmetro de consulta **visualFeatures.** Em seguida, quando tiver a resposta JSON completa, basta analisar a corda para o conteúdo da `"categories"` secção.

* [Quickstart: Visão por Computador .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analisar uma imagem (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Passos seguintes

Conheça os conceitos relacionados de [marcação](concept-tagging-images.md) de imagens e [descrição de imagens.](concept-describing-images.md)
