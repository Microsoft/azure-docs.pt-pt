---
title: Conceitos de deteção de rosto e atributos
titleSuffix: Azure Cognitive Services
description: A deteção facial é a ação de localizar rostos humanos numa imagem e retornar opcionalmente diferentes tipos de dados relacionados com o rosto.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743065"
---
# <a name="face-detection-and-attributes"></a>Deteção e atributos faciais

Este artigo explica os conceitos de deteção facial e dados de atributos faciais. A deteção facial é a ação de localizar rostos humanos numa imagem e retornar opcionalmente diferentes tipos de dados relacionados com o rosto.

Usa o [Rosto - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) a operação para detetar rostos numa imagem. No mínimo, cada rosto detetado corresponde a um campo retângulo facena na resposta. Este conjunto de coordenadas de pixel para a esquerda, superior, largura e altura marcam a face localizada. Usando estas coordenadas, você pode obter a localização do rosto e seu tamanho. Na resposta da API, os rostos são listados em ordem de tamanho de maior a menor.

## <a name="face-id"></a>Face ID

O ID facial é uma cadeia de identificação única para cada rosto detetado numa imagem. Pode solicitar uma identificação facial no seu [Rosto - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) a chamada da API.

## <a name="face-landmarks"></a>Pontos de referência do rosto

Os marcos faciais são um conjunto de pontos fáceis de encontrar num rosto, como as pupilas ou a ponta do nariz. Por predefinição, existem 27 pontos de referência predefinidos. O número seguinte mostra todos os 27 pontos:

![Um diagrama facial com todos os 27 marcos rotulados](../Images/landmarks.1.jpg)

As coordenadas dos pontos são devolvidas em unidades de pixels.

## <a name="attributes"></a>Atributos

Os atributos são um conjunto de funcionalidades que podem opcionalmente ser detetadas pelo [Face - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) a API. Podem ser detetados os seguintes atributos:

* **Idade.** A idade estimada em anos de um rosto particular.
* **Borrão.** A borrada do rosto na imagem. Este atributo devolve um valor entre zero e um e uma classificação informal de baixo, médio ou alto.
* **Emoção.** Uma lista de emoções com a sua confiança de deteção para o rosto. As pontuações de confiança estão normalizadas, e as pontuações em todas as emoções somam-se a uma. As emoções devolvidas são felicidade, tristeza, neutro, raiva, desprezo, nojo, surpresa e medo.
* **Exposição**. A exposição do rosto na imagem. Este atributo devolve um valor entre zero e um e uma classificação informal de underExposure, goodExposure ou overExposure.
* **Pelos faciais.** A presença estimada dos pelos faciais e o comprimento para o rosto dado.
* **Género.** O sexo estimado do rosto dado. Os valores possíveis são masculinos, femininos e sem género.
* **Óculos.** Se o rosto dado tem óculos. Os valores possíveis são NoGlasses, ReadingGlasses, Óculos de Sol e Óculos de Natação.
* **O cabelo.** O tipo de cabelo do rosto. Este atributo mostra se o cabelo é visível, se a calvície é detetada e quais as cores do cabelo detetadas.
* **Pose da cabeça.** A orientação do rosto no espaço 3D. Este atributo é descrito pelos ângulos de arremesso, rolo e bocejando em graus. Os intervalos de valor são de -90 graus a 90 graus, -180 graus a 180 graus, e -90 graus a 90 graus, respectivamente. Consulte o seguinte diagrama para mapeamento sinuoso:

    ![Uma cabeça com o arremesso, rolo, e eixos boceh](../Images/headpose.1.jpg)
* **Maquilhagem.** Se o rosto tem maquilhagem. Este atributo devolve um valor Boolean para eyeMakeup e lipMakeup.
* **Barulho.** O ruído visual detetado na imagem facial. Este atributo devolve um valor entre zero e um e uma classificação informal de baixo, médio ou alto.
* **Oclusão.** Se há objetos bloqueando partes do rosto. Este atributo devolve um valor booleano para eyeOccluded, testaOccluded, e bocaOccluded.
* **Sorria.** A expressão do sorriso do rosto dado. Este valor é entre zero sem sorriso e um para um sorriso claro.

> [!IMPORTANT]
> Os atributos faciais são previstos através do uso de algoritmos estatísticos. Podem nem sempre ser precisos. Tenha cuidado quando tomar decisões com base em dados de atributos.

## <a name="input-data"></a>Dados de entrada

Utilize as seguintes dicas para se certificar de que as imagens de entrada dão os resultados de deteção mais precisos:

* Os formatos de imagem de entrada suportados são JPEG, PNG, GIF para o primeiro quadro e BMP.
* O tamanho do ficheiro de imagem não deve ser superior a 4 MB.
* A gama de tamanho facial detetável é de 36 x 36 a 4096 x 4096 pixels. Rostos fora desta gama não serão detetados.
* Alguns rostos podem não ser detetados devido a desafios técnicos. Ângulos extremos do rosto (pose da cabeça) ou oclusão facial (objetos como óculos de sol ou mãos que bloqueiam parte do rosto) podem afetar a deteção. Rostos frontais e quase frontais dão os melhores resultados.

Se estiver a detetar rostos a partir de um feed de vídeo, poderá melhorar o desempenho ajustando determinadas definições na sua câmara de vídeo:

* **Suavização**: Muitas câmaras de vídeo aplicam um efeito de suavização. Deve desligá-lo se puder porque cria um borrão entre quadros e reduz a clareza.
* **Velocidade do obturador**: Uma velocidade mais rápida do obturador reduz a quantidade de movimento entre os quadros e torna cada quadro mais claro. Recomendamos velocidades de obturador de 1/60 segundos ou mais rápidos.
* **Ângulo do obturador**: Algumas câmaras especificam o ângulo do obturador em vez da velocidade do obturador. Deve utilizar um ângulo inferior do obturador, se possível. Isto resultará em molduras de vídeo mais claras.

    >[!NOTE]
    > Uma câmara com um ângulo inferior do obturador receberá menos luz em cada quadro, para que a imagem seja mais escura. Terá de determinar o nível certo para usar.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com conceitos de deteção facial, aprenda a escrever um guião que detete rostos numa dada imagem.

* [Detetar rostos numa imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)