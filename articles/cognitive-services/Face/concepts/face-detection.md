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
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891167"
---
# <a name="face-detection-and-attributes"></a>Deteção de rostos e atributos

Este artigo explica os conceitos de deteção de rostos e dados de atributo de rostos. Deteção de rostos é a ação de localização rostos humanos numa imagem e, opcionalmente, retorno diferentes tipos de dados relacionados de rostos.

Utilizar o [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) operação detetar rostos numa imagem. No mínimo, cada rosto detetado corresponde a um campo de faceRectangle na resposta. Este conjunto de coordenadas de pixel para a esquerda, a parte superior, a largura e altura marcar o mostrador localizado. Usá-las, pode obter a localização do mostrador e seu tamanho. Na resposta de API, os rostos estão listados na ordem do tamanho do maior ao mais pequeno.

## <a name="face-id"></a>ID do rosto

O face ID é uma cadeia de caracteres de identificador exclusivo para cada rosto detetado numa imagem. Pode pedir um ID de face no seu [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) chamada à API.

## <a name="face-landmarks"></a>Pontos de referência do rosto

Pontos de referência do rosto são um conjunto de pontos de fácil de encontrar um rosto, como os pupils ou ponta o nariz. Por predefinição, existem 27 pontos de referência predefinidos. A figura a seguir mostra todos os pontos de 27:

![Um diagrama de rostos com todos os 27 referências rotulada como](../Images/landmarks.1.jpg)

As coordenadas dos pontos são devolvidas em unidades de pixels.

## <a name="attributes"></a>Atributos

Os atributos são um conjunto de funcionalidades que, opcionalmente, pode ser detectada pelos [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Os seguintes atributos podem ser detetados:

* **Idade**. A idade estimada nos anos de um determinado rosto.
* **Desfoca**. Pouco de desfoque de rosto na imagem. Este atributo retorna um valor entre zero e único e uma classificação informal de baixa, média ou alta.
* **Emoções**. Uma lista de emoções com seus confiança de deteção para o rosto. Pontuações de confiança são normalizadas e as pontuações em todas as emoções adicione até uma. As emoções devolvidas são felicidade, tristeza, neutral, raiva, desdém, repulsa, surpresa e medo.
* **Exposição**. A exposição de rosto na imagem. Este atributo retorna um valor entre zero e único e uma classificação informal de underExposure, goodExposure ou overExposure.
* **Pelo facial, em**. A presença de pelo facial, em estimado e o comprimento para o rosto.
* **Sexo**. Estimado gender do mostrador da determinado. Valores possíveis são masculino, feminino e genderless.
* **Óculos**. Se o rosto tem óculos. Valores possíveis são NoGlasses, ReadingGlasses, óculos de sol e natação Goggles.
* **Hair**. O tipo de cabelo do mostrador da. Este atributo mostra se o cabelo está visível, se for detetado baldness e as cores de cabelo são detetadas.
* **HEAD representam**. Orientação do Mostrador no espaço 3D. Este atributo é descrito pelo pitch, roll e yaw ângulos em graus. Os intervalos de valores são graus de-90 a 90 graus, - 180 graus para 180 graus e graus de-90 a 90 graus, respectivamente. Consulte o diagrama seguinte para mapeamentos do ângulo:

    ![Um cabeçalho com o argumento de venda, roll e yaw eixos rotulada como](../Images/headpose.1.jpg)
* **Makeup**. Se o rosto tem como montar. Este atributo devolve um valor booleano para eyeMakeup e lipMakeup.
* **Ruído**. Poluição visual detetada na imagem de rostos. Este atributo retorna um valor entre zero e único e uma classificação informal de baixa, média ou alta.
* **Oclusão**. Se há objetos partes de bloqueios do mostrador da. Este atributo devolve um valor booleano para eyeOccluded, foreheadOccluded e mouthOccluded.
* **Sorria**. A expressão de sorriso do mostrador da determinado. Este valor está entre zero para nenhum sorriso e outro para um sorriso claro.

> [!IMPORTANT]
> Atributos faciais estão previstos através da utilização de algoritmos de estatísticos. Eles podem não ser sempre precisos. Tenha cuidado ao tomar decisões com base nos dados de atributo.

## <a name="input-data"></a>Dados de entrada

Utilize as sugestões seguintes para se certificar de que as imagens de entrada dê os resultados mais precisos de Deteção:

* Os formatos de entrada de imagem suportados são JPEG, PNG, GIF, para o primeiro quadro e BMP.
* O tamanho do ficheiro de imagem deve ser não maior do que 4 MB.
* O intervalo de tamanho de face detetável é 36 x 36 para 4096 x 4096 pixels. Não seja detectados rostos fora deste intervalo.
* Alguns rostos poderão não ser detetados por causa de desafios técnicos. Extreme face ângulos (representam principal) ou face oclusão (objetos como óculos de sol ou mãos bloquear parte do mostrador da) pode afetar a deteção. Rostos frontal e quase frontal oferecem os melhores resultados.

Se estiver a detetar rostos de uma transmissão de vídeo, poderá melhorar o desempenho ao ajustar determinadas definições em sua câmera de vídeo:

* **Suavização**: Muitos câmeras de vídeo aplicam-se um efeito de suavização. Deve desativar esta definição se puder porque cria uma indistinção entre quadros e reduz a clareza.
* **Velocidade de shutter**: Uma velocidade mais rápida de shutter reduz a quantidade de movimento entre quadros e faz com que cada quadro mais claro. Recomendamos que shutter velocidades de segundo de 1/60 ou mais rápido.
* **Shutter ângulo**: Alguns câmeras especificar ângulo shutter em vez de velocidade de shutter. Deve usar um ângulo shutter inferior se possível. Isto irá resultar em quadros de vídeo mais claras.

    >[!NOTE]
    > Uma câmara com um ângulo shutter inferior receberá menos claro em cada quadro, por isso, a imagem será mais escura. Terá de determinar o nível certo para utilizar.

## <a name="next-steps"></a>Passos Seguintes

Agora que está familiarizado com conceitos de deteção de rostos, saiba como criar um script que Deteta rostos numa determinada imagem.

* [Detetar rostos numa imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)