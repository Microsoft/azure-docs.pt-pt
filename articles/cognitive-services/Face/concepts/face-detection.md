---
title: Deteção de rostos e conceitos de atributos
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos sobre a deteção de rostos e atributos faciais.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572548"
---
# <a name="face-detection-and-attributes"></a>Deteção de rostos e atributos

Este artigo explica os conceitos de deteção de rostos e dados de atributo de rostos. Deteção de rostos é a ação de localização rostos humanos numa imagem e, opcionalmente, retornando uma variedade de dados relacionados com a face.

Utilizar o [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) operação detetar rostos numa imagem. No mínimo, cada detetado face corresponderá a um **faceRectangle** campo na resposta. Este é um conjunto de coordenadas de pixel (à esquerda, superior, largura, altura) marcando a face localizada. Usá-las, pode obter a localização do mostrador, bem como seu tamanho. Na resposta de API, os rostos estão listados na ordem do tamanho do maior ao mais pequeno.

## <a name="face-id"></a>ID do rosto

O ID do rosto é simplesmente uma cadeia de identificador exclusivo para cada rosto detetado numa imagem. Pode pedir um ID de face no seu [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) chamada à API.

## <a name="face-landmarks"></a>Pontos de referência do rosto

Pontos de referência do rosto são um conjunto de pontos de fácil de encontrar um rosto, como os pupils ou ponta no nariz. Por predefinição, existem 27 pontos de referência predefinidos. A figura a seguir mostra todos os pontos de 27:

![Um diagrama de rostos com todos os 27 referências rotulada como](../Images/landmarks.1.jpg)

As coordenadas dos pontos são devolvidas em unidades de pixels.

## <a name="attributes"></a>Atributos

Os atributos são um conjunto de recursos face adicionais que, opcionalmente, pode ser detectada pelos [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Seguem-se os atributos que podem ser detectados:

* **Idade** a idade estimada, nos anos, de um determinado rosto.
* **Desfoca** pouco de desfoque de rosto na imagem. Esta ação devolve um valor entre zero e um, bem como uma classificação informal ("baixa", "média", "Alta").
* **Exposição** a exposição de rosto na imagem. Esta ação devolve um valor entre zero e um, bem como uma classificação informal ("underExposure", "goodExposure", "overExposure").
* **Emoções** uma lista de emoções com seus confiança de deteção para o rosto. Pontuações de confiança são normalizadas: as pontuações em todas as emoções adicionará até uma. As emoções devolvidas são felicidade, tristeza, neutral, raiva, desdém, repulsa, surpresa e medo.
* **Pelo facial, em** a presença de estimado pelo facial, em e o comprimento do mostrador da determinado.
* **Sexo** estimado gender do mostrador da determinado. Valores possíveis são "masculino", "feminino" e "genderless".
* **Óculos** se o rosto tem óculos. Valores possíveis são "NoGlasses", "ReadingGlasses", "Óculos de sol" e "Natação Goggles".
* **Cabelo** o estilo de cabelo do mostrador da. Isto mostra se o cabelo está visível, se for detetado baldness e as cores de cabelo são detetadas.
* **HEAD representam** orientação a face no espaço 3D. Isso é descrito pelo pitch, roll e yaw ângulos em graus. Os intervalos de valores são [-90, 90,] [-180, 180] e [-90, 90] graus, respetivamente. Consulte o diagrama seguinte para mapeamentos do ângulo:

    ![Um cabeçalho com o argumento de venda, roll e yaw eixos rotulada como](../Images/headpose.1.jpg)
* **Composição da** se o rosto tem como montar. Esta ação devolve um valor booleano para "eyeMakeup" e "lipMakeup".
* **Ruído** poluição visual detetada na imagem de rostos. Esta ação devolve um valor entre zero e um, bem como uma classificação informal ("baixa", "média", "Alta").
* **Oclusão** se há objetos partes do Mostrador de bloqueio. Esta ação devolve um valor booleano para "eyeOccluded", "foreheadOccluded" e "mouthOccluded".
* **Sorria** a expressão de sorriso do mostrador da determinado. Este é um valor entre zero (sem sorriso) e um (sorriso claro).

> [!IMPORTANT]
> Atributos faciais estão previstos através da utilização de algoritmos de estatísticos e poderão não ser sempre precisos. Tenha cuidado quando tomar decisões com base nos dados de atributo.

## <a name="input-data"></a>Dados de entrada

Utilize as dicas a seguir para garantir que suas imagens de entrada dar os resultados mais precisos de Deteção:

* Os formatos de entrada de imagem suportados são PNG, JPEG, BMP e GIF (o primeiro quadro).
* Tamanho do ficheiro de imagem deve ser não maior do que 4 MB.
* O intervalo de tamanho de face detetável é 36 x 36 para 4096 x 4096 pixels. Não seja detectados rostos fora deste intervalo.
* Alguns rostos poderão não ser detetados por causa de desafios técnicos. Extreme face ângulos (representam principal) ou face oclusão (objetos como óculos de sol ou mãos bloquear parte do mostrador da) pode afetar a deteção. Rostos frontal e quase frontal oferecem os melhores resultados.

## <a name="next-steps"></a>Passos Seguintes

Agora que já está familiarizado com conceitos de deteção de rostos, saiba como escrever um script simples que Deteta rostos numa determinada imagem.

* [Como detetar rostos numa imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)