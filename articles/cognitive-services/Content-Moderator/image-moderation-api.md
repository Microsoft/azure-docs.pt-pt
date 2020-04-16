---
title: Moderação de imagem - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a ferramenta de moderação de imagem assistida por máquina sanções assistida pelo Moderador de Conteúdo e a ferramenta de revisão humana no loop para moderar imagens para conteúdos adultos e picantes.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 36777208dc8ac179f1aaf345c374a33001e3f8bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404254"
---
# <a name="learn-image-moderation-concepts"></a>Aprenda conceitos de moderação de imagem

Utilize a ferramenta de moderação de imagem assistida por máquina sanções assistida pelo Moderador de Conteúdo e a ferramenta de revisão humana no [loop](Review-Tool-User-Guide/human-in-the-loop.md) para moderar imagens para conteúdos adultos e picantes. Scaneie imagens para conteúdo de texto e extrai esse texto e detete rostos. Pode combinar imagens com listas personalizadas e tomar mais medidas.

## <a name="evaluating-for-adult-and-racy-content"></a>Avaliação de conteúdos adultos e picantes

A operação **Avaliar** devolve uma pontuação de confiança entre 0 e 1. Também devolve dados booleanos iguais aos verdadeiros ou falsos. Estes valores preveem se a imagem contém conteúdo potencialmente adulto ou picante. Quando liga para a API com a sua imagem (ficheiro ou URL), a resposta devolvida inclui as seguintes informações:

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
> - Os resultados estão entre 0 e 1. Quanto maior for a pontuação, maior é o modelo que prevê que a categoria pode ser aplicável. Esta pré-visualização baseia-se num modelo estatístico e não em resultados codificados manualmente. Recomendamos testar com o seu próprio conteúdo para determinar como cada categoria se alinha com os seus requisitos.
> - Os valores booleanos são verdadeiros ou falsos dependendo dos limiares de pontuação interna. Os clientes devem avaliar se utilizam este valor ou decidem sobre os limiares personalizados com base nas suas políticas de conteúdo.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Deteção de texto com reconhecimento ótico de caracteres (OCR)

A operação ótica de reconhecimento de **caracteres (OCR)** prevê a presença de conteúdo de texto numa imagem e extrai-o para moderação de texto, entre outras utilizações. Pode especificar a língua. Se não especificar um idioma, a deteção não se aplica ao inglês.

A resposta inclui as seguintes informações:
- O texto original.
- Os elementos de texto detetados com as suas pontuações de confiança.

Extrato de exemplo:

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

Detetar rostos ajuda a detetar dados pessoais, como rostos nas imagens. Deteta potenciais rostos e o número de rostos potenciais em cada imagem.

Uma resposta inclui esta informação:

- Rostos contam
- Lista de locais de rostos detetados

Extrato de exemplo:


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

## <a name="creating-and-managing-custom-lists"></a>Criação e gestão de listas personalizadas

Em muitas comunidades online, depois de os utilizadores carregarem imagens ou outro tipo de conteúdo, os itens ofensivos podem ser partilhados várias vezes ao longo dos dias, semanas e meses seguintes. Os custos de digitalizar e filtrar repetidamente a mesma imagem ou mesmo versões ligeiramente modificadas da imagem de vários lugares podem ser caros e propensos a erros.

Em vez de moderar a mesma imagem várias vezes, adiciona as imagens ofensivas à sua lista personalizada de conteúdo bloqueado. Desta forma, o seu sistema de moderação de conteúdos compara as imagens de entrada com as suas listas personalizadas e impede qualquer processamento adicional.

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

O Moderador de Conteúdo fornece uma API completa de Gestão de [Listas](try-image-list-api.md) de Imagem com operações para gerir listas de imagens personalizadas. Comece com as listas de [imagem Da Consola API](try-image-list-api.md) e utilize as amostras de código REST API. Consulte também a Lista de [Imagens .NET quickstart](image-lists-quickstart-dotnet.md) if you are familiar with Visual Studio and C#.

## <a name="matching-against-your-custom-lists"></a>Combinando com as suas listas personalizadas

A operação Match permite a correspondência de imagens de entrada com qualquer uma das suas listas personalizadas, criadas e geridas utilizando as operações da Lista.

Se for encontrada uma correspondência, a operação devolve o identificador e as etiquetas de moderação da imagem correspondida. A resposta inclui esta informação:

- Pontuação do jogo (entre 0 e 1)
- Imagem igualada
- Etiquetas de imagem (atribuídas durante a moderação anterior)
- Etiquetas de imagem

Extrato de exemplo:

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

## <a name="review-tool"></a>Ferramenta de revisão

Para casos mais matizados, utilize a ferramenta Content Moderator [Review](Review-Tool-User-Guide/human-in-the-loop.md) e a sua API para emergir os resultados e conteúdos de moderação na revisão para os seus moderadores humanos. Eles revêem as etiquetas atribuídas à máquina e confirmam as suas decisões finais.

![Revisão de imagem para moderadores humanos](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Passos seguintes

Teste a [consola Image Moderation API](try-image-api.md) e utilize as amostras de código REST API. Consulte também a secção de moderação de imagem do [quickstart .NET SDK](dotnet-sdk-quickstart.md) se estiver familiarizado com o Visual Studio e o C#.
