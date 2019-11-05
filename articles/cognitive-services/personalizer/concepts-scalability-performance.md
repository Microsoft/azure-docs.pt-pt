---
title: Escalabilidade e desempenho-personalizador
titleSuffix: Azure Cognitive Services
description: 'Sites e aplicativos de alto desempenho e de alto tráfego têm dois fatores principais a serem considerados com o personalizador para escalabilidade e desempenho: taxa de transferência de latência e treinamento.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490782"
---
# <a name="scalability-and-performance"></a>Escalabilidade e desempenho

Sites e aplicativos de alto desempenho e de alto tráfego têm dois fatores principais a serem considerados com o personalizador para escalabilidade e desempenho:

* Mantendo baixa latência ao fazer chamadas à API de classificação
* Garantindo que a taxa de transferência de treinamento acompanha a entrada do evento

A personalização pode retornar uma classificação rapidamente, com a maior parte da duração da chamada dedicada à comunicação por meio da API REST. O Azure fará o dimensionamento automático da capacidade de responder às solicitações rapidamente.

##  <a name="low-latency-scenarios"></a>Cenários de baixa latência

Alguns aplicativos exigem latências baixas ao retornar uma classificação. São necessárias latências baixas:

* Para impedir que o usuário aguarde uma quantidade perceptível de tempo antes de exibir o conteúdo classificado.
* Para ajudar um servidor que está apresentando tráfego extremo, evite a conexão de conexões de rede e de tempo de computação escassos.


## <a name="scalability-and-training-throughput"></a>Escalabilidade e produtividade de treinamento

O personalizador funciona atualizando um modelo que é retreinado com base nas mensagens enviadas de forma assíncrona pelo personalizador após as APIs de classificação e recompensa. Essas mensagens são enviadas usando um EventHub do Azure para o aplicativo.

 É improvável que a maioria dos aplicativos alcance a taxa de transferência máxima de junção e treinamento do personalizador. Embora atingir esse máximo não torne o aplicativo mais lento, isso significa que as filas do hub de eventos estão sendo preenchidas internamente mais rapidamente do que podem ser limpas.

## <a name="how-to-estimate-your-throughput-requirements"></a>Como estimar seus requisitos de taxa de transferência

* Estimar o número médio de bytes por evento de classificação adicionando os comprimentos dos documentos JSON de contexto e ação.
* Dividir 20 MB/s por esta média de bytes estimados.

Por exemplo, se a carga média tiver 500 recursos e cada um for um número estimado de 20 caracteres, cada evento será de aproximadamente 10 KB. Com essas estimativas, 20 milhões/10.000 = 2.000 eventos/s, que é de cerca de 173 milhões eventos/dia. 

Se você estiver atingindo esses limites, entre em contato com nossa equipe de suporte para obter conselhos sobre arquitetura.

## <a name="next-steps"></a>Passos seguintes

[Criar e configurar o personalizador](how-to-settings.md).