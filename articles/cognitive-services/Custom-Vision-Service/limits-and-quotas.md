---
title: Limites e quotas - serviço de visão personalizada
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os limites e quotas para o serviço de visão personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 85abc4a50710629d3485d05115698e59a93fc96e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60995636"
---
# <a name="limits-and-quotas"></a>Limites e quotas

Existem dois escalões de chaves para o serviço de visão personalizada. Pode inscrever-se para uma F0 (gratuito) ou uma subscrição (standard) de S0 através do portal do Azure. Ver o correspondente [página de preços de serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) para obter detalhes sobre preços e transações.

O número de imagens de formação por projeto e etiquetas por projeto devem aumentar ao longo do tempo para projetos de S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projetos|2|100|
|Imagens de formação por projeto |5,000|100,000|
|Predições / mês|10,000 |Ilimitado|
|Com as etiquetas /Project|50|500|
|Iterações |10|10|
|Min rotulado como imagens por etiqueta de classificação (mais de 50 recomendado) |5|5|
|Min rotulado como imagens por marca, deteção de objetos (mais de 50 recomendado)|15|15|
|O tempo em que as imagens de predição são armazenadas|30 dias|30 dias|
|[Predição](https://go.microsoft.com/fwlink/?linkid=865445) operações com o armazenamento (transações por segundo)|2|10|
|[Predição](https://go.microsoft.com/fwlink/?linkid=865445) operações sem armazenamento (transações por segundo)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chamadas à API por segundo)|2|10|
|[Outras chamadas de API](https://go.microsoft.com/fwlink/?linkid=865446) (transações por segundo)|10|10|
|Tamanho máximo de imagem (carregamento de imagens de formação) |6 MB|6 MB|
|Tamanho máximo de imagem (predição)|4 MB|4 MB|
