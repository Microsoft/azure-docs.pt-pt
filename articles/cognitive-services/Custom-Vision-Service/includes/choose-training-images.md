---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130108"
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
* formato. jpg,. png,. bmp ou. gif
* sem mais de 6 MB de tamanho (4MB para imagens de previsão)
* Não há menos de 256 pixels na borda mais curta; qualquer imagem menor que isso será automaticamente dimensionada pelo Serviço de Visão Personalizada
