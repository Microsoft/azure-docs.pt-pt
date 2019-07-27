---
title: Moderação de imagem-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use a moderação de imagem assistida por computador Content Moderator e a ferramenta de revisão humana no loop para imagens moderadas para conteúdo adulto e erótico.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 8b3449edb539ab56fcf206a367f9b81e43290733
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564912"
---
# <a name="learn-image-moderation-concepts"></a>Aprenda os conceitos de moderação de imagem

Use a moderação de imagem assistida por computador Content Moderator e a [ferramenta de revisão humana no loop](Review-Tool-User-Guide/human-in-the-loop.md) para imagens moderadas para conteúdo adulto e erótico. Digitalizar imagens para conteúdo de texto e extrair esse texto e detectar faces. Você pode corresponder imagens em listas personalizadas e tomar mais medidas.

## <a name="evaluating-for-adult-and-racy-content"></a>Avaliando para conteúdo adulto e erótico

A operação **Evaluate** retorna uma pontuação de confiança entre 0 e 1. Ele também retorna dados boolianos iguais a true ou false. Esses valores preveem se a imagem contém conteúdo de adulto ou erótico potencial. Quando você chama a API com sua imagem (arquivo ou URL), a resposta retornada inclui as seguintes informações:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - `isImageAdultClassified` representa a presença potencial de imagens que podem ser consideradas sexualmente explícitas ou para adultos em determinadas situações.
> - `isImageRacyClassified` representa a presença potencial de imagens que podem ser consideradas sexualmente sugestivas ou para adultos em determinadas situações.
> - As pontuações estão entre 0 e 1. Quanto maior a pontuação, mais alto o modelo está prevendo que a categoria pode ser aplicável. Essa visualização depende de um modelo estatístico em vez de resultados codificados manualmente. É recomendável testar com seu próprio conteúdo para determinar como cada categoria se alinha aos seus requisitos.
> - Os valores Boolianos são true ou false, dependendo dos limites de Pontuação internos. Os clientes devem avaliar se esse valor deve ser usado ou decidir sobre limites personalizados com base em suas políticas de conteúdo.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Detectando texto com reconhecimento óptico de caracteres (OCR)

A operação de **reconhecimento de caracteres ópticos (OCR)** prevê a presença de conteúdo de texto em uma imagem e a extrai para moderação de texto, entre outros usos. Você pode especificar o idioma. Se você não especificar um idioma, a detecção padrão será o inglês.

A resposta inclui as seguintes informações:
- O texto original.
- Os elementos de texto detectados com suas pontuações de confiança.

Exemplo de extração:

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>Detetar rostos

A detecção de faces ajuda a detectar dados pessoais, como faces nas imagens. Você detecta rostos potenciais e o número de rostos potenciais em cada imagem.

Uma resposta inclui essas informações:

- Contagem de faces
- Lista de locais de rostos detectados

Exemplo de extração:


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>Criando e Gerenciando listas personalizadas

Em muitas comunidades online, depois que os usuários carregam imagens ou outro tipo de conteúdo, os itens ofensivos podem ser compartilhados várias vezes durante os dias, semanas e meses seguintes. Os custos de verificação e filtragem repetidas da mesma imagem ou até mesmo de versões ligeiramente modificadas da imagem de vários lugares podem ser caros e sujeitos a erros.

Em vez de moderar a mesma imagem várias vezes, você adiciona as imagens ofensivas à sua lista personalizada de conteúdo bloqueado. Dessa forma, o sistema de moderação de conteúdo compara as imagens de entrada com as listas personalizadas e interrompe qualquer processamento adicional.

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

O Content Moderator fornece uma [API de gerenciamento de lista de imagens](try-image-list-api.md) completa com operações para gerenciar listas de imagens personalizadas. Comece com a [imagem lista o console de API](try-image-list-api.md) e use os exemplos de código da API REST. Confira também o guia de [início rápido do .NET da lista de imagens](image-lists-quickstart-dotnet.md) se estiver C#familiarizado com o Visual Studio e o.

## <a name="matching-against-your-custom-lists"></a>Correspondência com suas listas personalizadas

A operação de correspondência permite a correspondência difusa de imagens de entrada em qualquer uma de suas listas personalizadas, criadas e gerenciadas usando as operações de lista.

Se uma correspondência for encontrada, a operação retornará o identificador e as marcas de moderação da imagem correspondente. A resposta inclui essas informações:

- Pontuação de correspondência (entre 0 e 1)
- Imagem correspondente
- Marcas de imagem (atribuídas durante a moderação anterior)
- Rótulos de imagem

Exemplo de extração:

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="human-review-tool"></a>Ferramenta de revisão humana

Para casos mais sutis, use a [ferramenta de análise](Review-Tool-User-Guide/human-in-the-loop.md) de Content moderator e sua API para trazer os resultados de moderação e o conteúdo na revisão para seus moderadores humanos. Eles revisam as marcas atribuídas ao computador e confirmam suas decisões finais.

![Revisão de imagem para moderadores humanos](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Passos Seguintes

Teste o [console da API](try-image-api.md) de moderação de imagem e use os exemplos de código da API REST. Confira também o [início rápido do .net](image-moderation-quickstart-dotnet.md) de moderação de imagem se você estiver C#familiarizado com o Visual Studio e o.
