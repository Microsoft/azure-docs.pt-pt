---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: d07a5da3b9013700694f6c20102ef2e8c5066087
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256533"
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
> O Trove, um projeto da Microsoft Garage, permite-lhe recolher e adquirir conjuntos de imagens para fins de treino. Uma vez recolhidas as suas imagens, pode descarregá-las e depois importá-las para o seu projeto De Visão Personalizada da forma habitual. Visite a [página do Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) para saber mais.