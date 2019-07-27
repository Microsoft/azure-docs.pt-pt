---
title: Limites e cotas-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre os limites e cotas para o Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 37921c655cc3c5de5c3c5079eda47fb7513fdf9f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560952"
---
# <a name="limits-and-quotas"></a>Limites e quotas

Há duas camadas de chaves para o serviço de Visão Personalizada. Você pode se inscrever para uma assinatura F0 (gratuita) ou S0 (Standard) por meio do portal do Azure. Consulte a [página de preços de serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) correspondente para obter detalhes sobre preços e transações.

Espera-se que o número de imagens de treinamento por projeto e marcas por projeto aumente ao longo do tempo para projetos S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projetos|2|100|
|Imagens de treinamento por projeto |5,000|100,000|
|Previsões/mês|10,000 |Ilimitado|
|Marcas/projeto|50|500|
|Iterações |10|10|
|Mínimo de imagens rotuladas por marca, classificação (50 + recomendado) |5|5|
|Mínimo de imagens rotuladas por marca, detecção de objeto (mais de 50 + recomendado)|15|15|
|Por quanto tempo as imagens de previsão são armazenadas|30 dias|30 dias|
|Operações de [previsão](https://go.microsoft.com/fwlink/?linkid=865445) com armazenamento (transações por segundo)|2|10|
|Operações de [previsão](https://go.microsoft.com/fwlink/?linkid=865445) sem armazenamento (transações por segundo)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (Chamadas de API por segundo)|2|10|
|[Outras chamadas à API](https://go.microsoft.com/fwlink/?linkid=865446) (Transações por segundo)|10|10|
|Tamanho máximo da imagem (upload de imagem de treinamento) |6 MB|6 MB|
|Tamanho máximo da imagem (previsão)|4 MB|4 MB|
|Imagem de treinamento da detecção de regiões máximas por objeto|200|200|
|Máximo de marcas por imagem de classificação|30|30|
