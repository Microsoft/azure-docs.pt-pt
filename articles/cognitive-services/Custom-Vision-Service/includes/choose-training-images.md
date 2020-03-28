---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70130108"
---
No mínimo, recomendamos que utilize pelo menos 30 imagens por etiqueta no conjunto de treino inicial. Também vai querer recolher algumas imagens extra para testar o seu modelo uma vez treinado.

Para treinar eficazmente o seu modelo, utilize imagens com variedade visual. Selecione imagens que variam por:
* ângulo da câmara
* iluminação
* fundo
* estilo visual
* sujeito s individuais/agrupados
* size
* tipo

Além disso, certifique-se de que todas as suas imagens de treino cumprem os seguintes critérios:
* .jpg, .png, .bmp ou .gif formato
* não superior a 6MB de tamanho (4MB para imagens de previsão)
* pelo menos 256 pixels na borda mais curta; quaisquer imagens mais curtas do que esta serão automaticamente dimensionadas pelo Serviço de Visão Personalizada
