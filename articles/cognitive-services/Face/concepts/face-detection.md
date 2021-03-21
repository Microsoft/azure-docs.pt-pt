---
title: Conceitos de deteção de rosto e atributos
titleSuffix: Azure Cognitive Services
description: A deteção facial é a ação de localizar rostos humanos numa imagem e devolver opcionalmente diferentes tipos de dados relacionados com o rosto.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 68d1e9744d937cf80327c3f41cc69f4af97d3400
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600181"
---
# <a name="face-detection-and-attributes"></a>Deteção de rosto e atributos

Este artigo explica os conceitos de deteção facial e dados de atributos faciais. A deteção facial é a ação de localizar rostos humanos numa imagem e devolver opcionalmente diferentes tipos de dados relacionados com o rosto.

Utiliza a operação [Face - Deteta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rostos numa imagem. No mínimo, cada face detetada corresponde a um campo faceRectangle na resposta. Este conjunto de coordenadas de pixel para a esquerda, topo, largura e altura marcam a face localizada. Usando estas coordenadas, você pode obter a localização do rosto e do seu tamanho. Na resposta da API, os rostos são listados em ordem de tamanho de maior para menor.

## <a name="face-id"></a>Face ID

O ID do rosto é uma corda de identificação única para cada rosto detetado numa imagem. Pode solicitar uma identificação facial no [seu rosto - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) a chamada API.

## <a name="face-landmarks"></a>Pontos de referência do rosto

Marcos faciais são um conjunto de pontos fáceis de encontrar em um rosto, como as pupilas ou a ponta do nariz. Por predefinição, existem 27 pontos de referência predefinidos. O seguinte número mostra todos os 27 pontos:

![Um diagrama de rosto com todos os 27 marcos marcados](../Images/landmarks.1.jpg)

As coordenadas dos pontos são devolvidas em unidades de pixels.

## <a name="attributes"></a>Atributos

Os atributos são um conjunto de funcionalidades que podem ser detetadas opcionalmente pelo [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Podem ser detetados os seguintes atributos:

* **Idade.** A idade estimada em anos de um rosto particular.
* **Borrão.** A desfocagem do rosto na imagem. Este atributo devolve um valor entre zero e um e uma classificação informal de baixo, médio ou alto.
* **Emoção.** Uma lista de emoções com a sua confiança de deteção para o rosto dado. As pontuações de confiança estão normalizadas, e as pontuações em todas as emoções somam-se a uma. As emoções devolvidas são felicidade, tristeza, neutra, raiva, desprezo, nojo, surpresa e medo.
* **Exposição**. A exposição do rosto na imagem. Este atributo devolve um valor entre zero e um e uma classificação informal de subExposure, goodExposure ou overExposure.
* **Pelos faciais.** A presença estimada de pelos faciais e o comprimento para o rosto dado.
* **Género.** O sexo estimado do rosto dado. Os valores possíveis são masculinos, femininos e sem género.
* **Óculos.** Se o rosto dado tem óculos. Os valores possíveis são NoGlasses, ReadingGlasses, Óculos de Sol e Óculos de Natação.
* **Cabelo.** O tipo de cabelo do rosto. Este atributo mostra se o cabelo é visível, se a calvície é detetada e quais as cores do cabelo detetadas.
* **Pose de cabeça**. A orientação do rosto no espaço 3D. Este atributo é descrito pelos ângulos de arremesso, rolo e bocejo em graus. Os valores são de -90 graus a 90 graus, -90 graus a 90 graus, e -90 graus a 90 graus, respectivamente. Consulte o seguinte diagrama para mapeamentos de ângulo:

    ![Uma cabeça com o arremesso, rolo e eixos de bocejo rotulados](../Images/headpose.1.jpg)
* **Maquilhagem.** Se o rosto tem maquilhagem. Este atributo devolve um valor Boolean para eyeMakeup e lipMakeup.
* **Barulho.** O ruído visual detetado na imagem do rosto. Este atributo devolve um valor entre zero e um e uma classificação informal de baixo, médio ou alto.
* **Oclusão.** Se há objetos a bloquear partes do rosto. Este atributo devolve um valor booleano para os olhosOccluded, testaOccluded, e mouthOccluded.
* **Sorria.** A expressão do sorriso do rosto dado. Este valor é entre zero para nenhum sorriso e um para um sorriso claro.

> [!IMPORTANT]
> Os atributos faciais são previstos através da utilização de algoritmos estatísticos. Podem nem sempre ser precisos. Tenha cuidado quando tomar decisões com base em dados de atributos.

## <a name="input-data"></a>Dados de entrada

Utilize as seguintes dicas para se certificar de que as suas imagens de entrada dão os resultados de deteção mais precisos:

* Os formatos de imagem de entrada suportados são JPEG, PNG, GIF para o primeiro quadro e BMP.
* O tamanho do ficheiro de imagem não deve ser superior a 6 MB.
* O tamanho mínimo de rosto detetável é de 36 x 36 pixels numa imagem que não é maior que 1920 x 1080 pixels. As imagens com maiores de 1920 x 1080 pixels têm um tamanho mínimo proporcionalmente maior. A redução do tamanho do rosto pode fazer com que alguns rostos não sejam detetados, mesmo que sejam maiores do que o tamanho mínimo detetável do rosto.
* O tamanho máximo de rosto detetável é de 4096 x 4096 pixels.
* Não serão detetados rostos fora da gama de tamanhos de 36 x 36 a 4096 x 4096 pixels.
* Alguns rostos podem não ser detetados devido a desafios técnicos. Ângulos faciais extremos (pose da cabeça) ou oclusão facial (objetos como óculos de sol ou mãos que bloqueiam parte da face) podem afetar a deteção. Rostos frontais e quase frontais dão os melhores resultados.

Se estiver a detetar rostos a partir de um feed de vídeo, poderá ser capaz de melhorar o desempenho ajustando determinadas definições na sua câmara de vídeo:

* **Suavização**: Muitas câmaras de vídeo aplicam um efeito de suavização. Deve desligá-lo se puder, porque cria um borrão entre as molduras e reduz a clareza.
* **Velocidade do obturador**: Uma velocidade mais rápida do obturador reduz a quantidade de movimento entre as molduras e torna cada quadro mais claro. Recomendamos velocidades de obturação de 1/60 segundos ou mais rápido.
* **Ângulo do obturador**: Algumas câmaras especificam o ângulo do obturador em vez da velocidade do obturador. Deve utilizar um ângulo de obturação mais baixo, se possível. Isto resultará em molduras de vídeo mais claras.

    >[!NOTE]
    > Uma câmara com um ângulo de obturação inferior receberá menos luz em cada quadro, para que a imagem fique mais escura. Terá de determinar o nível certo para usar.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com conceitos de deteção facial, aprenda a escrever um script que detete rostos numa determinada imagem.

* [Detetar rostos numa imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
