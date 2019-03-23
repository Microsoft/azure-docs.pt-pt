---
title: Preços e limites - serviço de visão personalizada
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os limites e quotas para o serviço de visão personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: a3fdd39cdbd4204fece145bde23b23e155500bdb
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351393"
---
# <a name="pricing-and-limits"></a>Preços e limites

Existem três camadas de chaves para o serviço de visão personalizada. Recursos de projeto de avaliação limitados são anexados ao seu início de sessão de visão personalizada (ou seja, uma conta do Azure Active Directory ou uma conta MSA). Eles se destinam a ser utilizado para avaliações abreviadas do serviço. Pode inscrever-se para uma F0 (gratuito) ou uma subscrição (standard) de S0 através do portal do Azure. Ver o correspondente [página de preços de serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) para obter detalhes sobre preços e transações.

As contas criadas durante a pré-visualização gratuita antecipada, antes da introdução das pré-visualizações do Azure (1 de Março de 2018), irão manter as respetivas quotas anteriores para as versões de avaliação limitado.

O número de imagens de formação por projeto e etiquetas por projeto devem aumentar ao longo do tempo para projetos de S0.

||**Versão de avaliação limitada**|**F0**|**S0**|
|-----|-----|-----|-----|
|Projetos|2|2|100|
|Imagens de formação por projeto, classificação|5.000|5.000|50,000|
|Imagens de formação por projeto, deteção de objetos|5.000|5.000|10,000|
|Predições / mês|10,000 |10,000|Ilimitado|
|Com as etiquetas /Project|50|50|250|
|Iterações |10|10|10|
|Mínimo rotulado como imagens por etiqueta de classificação (mais de 50 recomendado) |5|5|5|
|Mínimo rotulado como imagens por marca, deteção de objetos (mais de 50 recomendado)|15|15|15|
|O tempo em que as imagens de predição são armazenadas|30 dias|30 dias|30 dias|
|[Predição](https://go.microsoft.com/fwlink/?linkid=865445) operações com o armazenamento (transações por segundo)|2|2|10|
|[Predição](https://go.microsoft.com/fwlink/?linkid=865445) operações sem armazenamento (transações por segundo)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chamadas à API por segundo)|2|2|10|
|[Outras chamadas de API](https://go.microsoft.com/fwlink/?linkid=865446) (transações por segundo)|10|10|10|
|Tamanho máximo de imagem (carregamento de imagens de formação) |6 MB|6 MB|6 MB|
|Tamanho máximo de imagem (predição)|4 MB|4 MB|4 MB|