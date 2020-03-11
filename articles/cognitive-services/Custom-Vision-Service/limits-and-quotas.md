---
title: Limites e quotas - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo explica os diferentes tipos de chaves de licenciamento e sobre os limites e quotas para o Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081782"
---
# <a name="limits-and-quotas"></a>Limites e quotas

Existem dois níveis de chaves para o serviço Custom Vision. Pode inscrever-se para uma subscrição f0 (grátis) ou S0 (standard) através do portal Azure. Consulte a página de Preços dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) correspondente para obter detalhes sobre preços e transações.

Espera-se que o número de imagens de formação por projeto e etiquetas por projeto aumente ao longo do tempo para os projetos S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projetos|2|100|
|Formação de imagens por projeto |5\.000|100 000|
|Previsões / mês|10,000 |Ilimitado|
|Tags / projeto|50|500|
|Iterações |10|10|
|Imagens min rotuladas por Tag, Classificação (50+ recomendado) |5|5|
|Imagens min rotuladas por Tag, Deteção de Objetos (50+ recomendado)|15|15|
|Quanto tempo as imagens de previsão armazenadas|30 dias|30 dias|
|[Operações](https://go.microsoft.com/fwlink/?linkid=865445) de previsão com armazenamento (Transações por segundo)|2|10|
|[Operações](https://go.microsoft.com/fwlink/?linkid=865445) de previsão sem armazenamento (Transações por segundo)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (Chamadas API por segundo)|2|10|
|[Outras chamadas API](https://go.microsoft.com/fwlink/?linkid=865446) (Transações por segundo)|10|10|
|Tipos de imagem aceites|jpg, png, bmp, gif|jpg, png, bmp, gif|
|Altura/largura da imagem min em pixels|256 (ver nota)|256 (ver nota)|
|Altura/largura de imagem máxima em pixels|ilimitado|ilimitado|
|Tamanho máximo da imagem (upload de imagem de treino) |6 MB|6 MB|
|Tamanho máximo da imagem (previsão)|4 MB|4 MB|
|Regiões max por imagem de treino de deteção de objetos|300|300|
|Etiquetas max por imagem de classificação|100|100|

> [!NOTE]
> Imagens inferiores a 256 pixels serão aceites, mas de luxo.
