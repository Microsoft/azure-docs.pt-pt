---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "70130108"
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
* .jpg, .png, .bmp, ou .gif formato
* não superior a 6MB de tamanho (4MB para imagens de previsão)
* nada menos que 256 pixels na borda mais curta; quaisquer imagens mais curtas do que esta serão automaticamente dimensionada pelo Serviço de Visão Personalizada
