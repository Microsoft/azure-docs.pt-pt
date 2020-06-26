---
title: Limites e quotas - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo explica os diferentes tipos de chaves de licenciamento e sobre os limites e quotas para o Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: 089d5dbb1eece2904f104dfecb3de8adec052dfc
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391745"
---
# <a name="limits-and-quotas"></a>Limites e quotas

Existem dois níveis de chaves para o serviço De Visão Personalizada. Pode inscrever-se para uma subscrição F0 (gratuita) ou S0 (standard) através do portal Azure. Consulte a [página de preços dos serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) correspondentes para obter detalhes sobre preços e transações.

Espera-se que o número de imagens de formação por projeto e etiquetas por projeto aumente ao longo do tempo para os projetos S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projetos|2|100|
|Imagens de formação por projeto |5000|100.000|
|Previsões /mês|10,000 |Ilimitado|
|Tags / projeto|50|500|
|Iterações |10|10|
|Imagens min rotuladas por Tag, Classificação (50+ recomendado) |5|5|
|Imagens com rótulo min por Tag, Deteção de Objetos (50+ recomendado)|15|15|
|Quanto tempo de previsão de imagens armazenadas|30 dias|30 dias|
|[Operações de previsão](https://go.microsoft.com/fwlink/?linkid=865445) com armazenamento (Transações por Segundo)|2|10|
|[Operações de previsão](https://go.microsoft.com/fwlink/?linkid=865445) sem armazenamento (Transações por Segundo)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chamadas API por segundo)|2|10|
|[Outras chamadas da API](https://go.microsoft.com/fwlink/?linkid=865446) (Transações por Segundo)|10|10|
|Tipos de imagem aceites|jpg, png, bmp, gif|jpg, png, bmp, gif|
|Min altura/largura de imagem em pixéis|256 (ver nota)|256 (ver nota)|
|Altura/largura de imagem máxima em pixéis|ilimitado|ilimitado|
|Tamanho de imagem max (upload de imagem de treino) |6 MB|6 MB|
|Tamanho da imagem máxima (previsão)|4 MB|4 MB|
|Regiões max por imagem de treino de deteção de objetos|300|300|
|Etiquetas máximas por imagem de classificação|100|100|

> [!NOTE]
> Imagens inferiores a 256 pixels serão aceites, mas de luxo.
