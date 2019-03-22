---
title: O Content Moderator da moderação de - de imagem
titlesuffix: Azure Cognitive Services
description: Utilize a moderação de imagens assistida e a ferramenta de revisão humana em loop para imagens moderadas Content Moderator para conteúdos para adultos.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 17e4d576d4c524321fd19c589868a323f97c48a1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079726"
---
# <a name="learn-image-moderation-concepts"></a>Aprenda os conceitos de moderação de imagem

Utilize a moderação de imagens de assistida do Content Moderator e [ferramenta de revisão humana em loop](Review-Tool-User-Guide/human-in-the-loop.md) moderar as imagens para o conteúdo para adultos. Analise imagens para o conteúdo de texto e extrair esse texto para detetar rostos. Pode corresponder a imagens face às listas personalizadas e qualquer ação adicional.

## <a name="evaluating-for-adult-and-racy-content"></a>Avaliar o conteúdo para adultos

O **Evaluate** operação devolve uma pontuação de confiança entre 0 e 1. Ele também retorna dados booleano igual a VERDADEIRO ou FALSO. Estes valores preveem se a imagem contém o conteúdo de adultos potencial. Quando chama a API com a sua imagem (ficheiro ou URL), a resposta retornada inclui as seguintes informações:

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
> - As pontuações são entre 0 e 1. Quanto maior for a pontuação, maior será o modelo é a previsão que a categoria pode ser aplicável. Esta pré-visualização depende de um modelo estatístico em vez de resultados codificados manualmente. Recomendamos que teste com o seu próprio conteúdo para determinar a forma como cada categoria alinha os seus requisitos.
> - Os valores booleanos são true ou false dependendo da pontuação interna limiares. Os clientes devem avaliar se pretende utilizar este valor ou opte por utilizar limiares personalizados com base em suas diretivas de conteúdo.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Detecção de texto com reconhecimento Ótico de carateres (OCR)

O **reconhecimento Ótico de carateres (OCR)** operação prevê a presença de conteúdo de texto numa imagem e extrai-lo para moderação de texto, entre outras utilizações. Pode especificar o idioma. Se não especificar um idioma, a deteção é predefinido para inglês.

A resposta inclui as seguintes informações:
- O texto original.
- Os elementos de texto detetado com suas pontuações de confiança.

Extração de exemplo:

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

Ajuda a detetar rostos para detetar informações de identificação pessoal (PII), como rostos nas imagens. Detetar rostos potenciais e o número de rostos potenciais em cada imagem.

Uma resposta inclui estas informações:

- Contagem de rostos
- Lista de localizações de rostos detetados

Extração de exemplo:


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

## <a name="creating-and-managing-custom-lists"></a>Criar e gerir listas personalizadas

Em muitas comunidades online, depois dos utilizadores carregar imagens ou outro tipo de conteúdo, ofensivas itens podem obter compartilhados várias vezes durante os seguintes dias, semanas ou meses. Os custos de repetidamente verificação e filtragem a mesma imagem ou até mesmo ligeiramente modificadas versões da imagem a partir de vários locais podem ser dispendiosa e propenso a erros.

Em vez de moderating a mesma imagem várias vezes, é possível adicionar as imagens ofensivas à sua lista personalizada de conteúdo bloqueado. Dessa forma, o seu sistema de moderação de conteúdos compara as imagens de entrada face às suas listas personalizadas e para a qualquer processamento adicional.

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

O Content Moderator fornece uma completa [API de gestão da lista de imagens](try-image-list-api.md) com operações de gerenciamento de listas de imagens personalizadas. Começar com o [consola de API de lista de imagem](try-image-list-api.md) e utilize os exemplos de código da REST API. Verifique também a [guia de introdução do .NET de lista de imagem](image-lists-quickstart-dotnet.md) se estiver familiarizado com o Visual Studio e c#.

## <a name="matching-against-your-custom-lists"></a>Correspondência face às suas listas personalizadas

A operação de correspondência permite a correspondência difusa de imagens de entrada em relação a qualquer uma das suas listas personalizadas criadas e geridas com as operações de lista.

Se uma correspondência for encontrada, a operação devolve o identificador e as etiquetas de moderação de imagem correspondente. A resposta inclui estas informações:

- Pontuação de correspondência (entre 0 e 1)
- Imagem correspondente
- Etiquetas de imagem (atribuídas durante a moderação anterior)
- Etiquetas de imagem

Extração de exemplo:

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

Para obter mais variada casos, utilize o Content Moderator [ferramenta de revisão](Review-Tool-User-Guide/human-in-the-loop.md) e sua API para apresentar os resultados de moderação e o conteúdo da revisão para sua moderadores humanos. Eles rever as etiquetas de máquina atribuída e confirmar suas decisões finais.

![Revisão de imagem para moderadores humanos](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Passos Seguintes

Faça o test drive da [consola de API de moderação de imagens](try-image-api.md) e utilize os exemplos de código da REST API. Verifique também a [guia de introdução de .NET de moderação de imagem](image-moderation-quickstart-dotnet.md) se estiver familiarizado com o Visual Studio e c#.
