---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 9f8eadea198bbae3de2ffc1b3aaac48925719586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100105989"
---
No mínimo, recomendamos que utilize pelo menos 30 imagens por etiqueta no conjunto de treino inicial. Também vai querer recolher algumas imagens extra para testar o seu modelo uma vez treinado.

Para treinar eficazmente o seu modelo, utilize imagens com variedade visual. Selecione imagens que variam por:
* ângulo da câmera
* iluminação
* fundo
* estilo visual
* sujeito individual/agrupado
* size
* tipo

Além disso, certifique-se de que todas as suas imagens de treino cumprem os seguintes critérios:
* .jpg, .png, .bmp ou .gif formato
* não superior a 6MB de tamanho (4MB para imagens de previsão)
* nada menos que 256 pixels na borda mais curta; quaisquer imagens mais curtas do que esta serão automaticamente dimensionada pelo Serviço de Visão Personalizada

> [!NOTE]
> Precisa de um conjunto mais amplo de imagens para completar o seu treino? O Trove, um projeto da Microsoft Garage, permite-lhe recolher e adquirir conjuntos de imagens para fins de treino. Uma vez recolhidas as suas imagens, pode descarregá-las e depois importá-las para o seu projeto De Visão Personalizada da forma habitual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.