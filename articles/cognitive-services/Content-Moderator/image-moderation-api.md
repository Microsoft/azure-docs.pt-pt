---
title: Moderação de imagem - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a moderação de imagem assistida por máquinas do Moderador de Conteúdo e a ferramenta de revisão humana no loop para moderar imagens para conteúdo adulto e picante.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: fe76e32bfd9b1734f3c84a400f897b7af7e3168b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85801000"
---
# <a name="learn-image-moderation-concepts"></a>Aprenda conceitos de moderação de imagem

Utilize a ferramenta de moderação e [revisão](Review-Tool-User-Guide/human-in-the-loop.md) de imagem assistida pelo Moderador de Conteúdo para moderar imagens para conteúdo adulto e picante. Digitalize as imagens para obter conteúdo de texto e extrate esse texto e detete rostos. Pode comparar imagens com listas personalizadas e tomar medidas adicionais.

## <a name="evaluating-for-adult-and-racy-content"></a>Avaliação para conteúdo adulto e picante

A operação **Avaliação** devolve uma pontuação de confiança entre 0 e 1. Também devolve dados booleans iguais aos verdadeiros ou falsos. Estes valores prevêem se a imagem contém conteúdo adulto ou picante. Quando liga para a API com a sua imagem (ficheiro ou URL), a resposta devolvida inclui as seguintes informações:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - `isImageAdultClassified` representa a presença potencial de imagens que podem ser consideradas sexualmente explícitas ou para adultos em determinadas situações.
> - `isImageRacyClassified` representa a presença potencial de imagens que podem ser consideradas sexualmente sugestivas ou para adultos em determinadas situações.
> - As notas estão entre 0 e 1. Quanto maior for a pontuação, maior é a previsão do modelo de que a categoria pode ser aplicável. Esta pré-visualização baseia-se num modelo estatístico em vez de resultados codificados manualmente. Recomendamos testar com o seu próprio conteúdo para determinar como cada categoria se alinha com os seus requisitos.
> - Os valores booleanos são verdadeiros ou falsos dependendo dos limiares de pontuação interna. Os clientes devem avaliar se utilizam este valor ou se decidem sobre limiares personalizados com base nas suas políticas de conteúdo.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Deteção de texto com reconhecimento de caracteres óticos (OCR)

A operação **de Reconhecimento de Caracteres Óticos (OCR)** prevê a presença de conteúdo de texto numa imagem e extrai-o para moderação de texto, entre outros usos. Pode especificar o idioma. Se não especificar um idioma, a deteção predefini em inglês.

A resposta inclui as seguintes informações:
- O texto original.
- Os elementos de texto detetados com as suas notas de confiança.

Extrato de exemplo:

```json
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
```

## <a name="detecting-faces"></a>Detetar faces

A deteção de rostos ajuda a detetar dados pessoais, como rostos nas imagens. Deteta potenciais rostos e o número de potenciais rostos em cada imagem.

Uma resposta inclui esta informação:

- Rostos contam
- Lista de locais de rostos detetados

Extrato de exemplo:

```json
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
```

## <a name="creating-and-managing-custom-lists"></a>Criar e gerir listas personalizadas

Em muitas comunidades online, após os utilizadores carregarem imagens ou outro tipo de conteúdo, os itens ofensivos podem ser partilhados várias vezes ao longo dos dias, semanas e meses seguintes. Os custos de digitalização e filtragem repetidas da mesma imagem ou mesmo versões ligeiramente modificadas da imagem de vários locais podem ser dispendiosos e propensos a erros.

Em vez de moderar a mesma imagem várias vezes, adicione as imagens ofensivas à sua lista personalizada de conteúdo bloqueado. Desta forma, o seu sistema de moderação de conteúdos compara as imagens recebidas com as suas listas personalizadas e impede qualquer processamento adicional.

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

O Moderador de Conteúdo fornece uma API completa [de Gestão de Listas](try-image-list-api.md) de Imagem com operações para gerir listas de imagens personalizadas. Comece com a [Consola API listas de imagens](try-image-list-api.md) e utilize as amostras de código REST API. Confira também a [lista de imagens .NET quickstart](image-lists-quickstart-dotnet.md) se estiver familiarizado com o Visual Studio e C#.

## <a name="matching-against-your-custom-lists"></a>Combinando com as suas listas personalizadas

A operação Match permite a correspondência difusa das imagens recebidas com qualquer uma das suas listas personalizadas, criadas e geridas através das operações Lista.

Se for encontrada uma correspondência, a operação devolve o identificador e as etiquetas de moderação da imagem compatível. A resposta inclui esta informação:

- Pontuação do jogo (entre 0 e 1)
- Imagem compatível
- Etiquetas de imagem (atribuídas durante moderação prévia)
- Etiquetas de imagem

Extrato de exemplo:

```json
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
```

## <a name="review-tool"></a>Ferramenta de revisão

Para casos mais matizados, utilize a [ferramenta de Análise](Review-Tool-User-Guide/human-in-the-loop.md) de Moderadores de Conteúdo e a sua API para apresentar os resultados e conteúdos de moderação na revisão para os seus moderadores humanos. Eles analisam as etiquetas atribuídas à máquina e confirmam as suas decisões finais.

![Revisão de imagem para moderadores humanos](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Passos seguintes

Teste a [consola API de moderação de imagem](try-image-api.md) e utilize as amostras de código REST API. Consulte também [revisões, fluxos de trabalho e empregos](./review-api.md) para aprender a configurar críticas humanas.
