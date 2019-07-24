---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ee2c1fd1fc1cad07b14a2c99318be20be30db9c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423560"
---
No mínimo, recomendamos que você use pelo menos 30 imagens por marca no conjunto de treinamento inicial. Você também desejará coletar algumas imagens adicionais para testar seu modelo quando ele for treinado.

Para treinar seu modelo com eficiência, use imagens com a variedade Visual. Selecione as imagens que variam de acordo com:
* ângulo da câmera
* ilumina
* seguindo
* estilo visual
* entidade (ões) individual/agrupado (s)
* size
* type

Além disso, verifique se todas as suas imagens de treinamento atendem aos seguintes critérios:
* formato. jpg,. png ou. bmp
* sem mais de 6 MB de tamanho (4MB para imagens de previsão)
* Não há menos de 256 pixels na borda mais curta; qualquer imagem menor que isso será automaticamente dimensionada pelo Serviço de Visão Personalizada