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
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: d116f6bd389b1404ea723c965111cd05880e6c30
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662825"
---
# <a name="scalability-and-performance"></a>Escalabilidade e desempenho

Sites e aplicativos de alto desempenho e de alto tráfego têm dois fatores principais a serem considerados com o personalizador para escalabilidade e desempenho:

* Mantendo baixa latência ao fazer chamadas à API de classificação
* Garantindo que a taxa de transferência de treinamento acompanha a entrada do evento

A personalização pode retornar uma classificação muito rapidamente, com a maior parte da duração da chamada dedicada à comunicação por meio da API REST. O Azure fará o dimensionamento automático da capacidade de responder às solicitações rapidamente.

##  <a name="low-latency-scenarios"></a>Cenários de baixa latência

Alguns aplicativos exigem latências baixas ao retornar uma classificação. Isso é necessário:

* Para impedir que o usuário aguarde uma quantidade perceptível de tempo antes de exibir o conteúdo classificado.
* Para ajudar um servidor que está apresentando tráfego extremo, evite a conexão de conexões de rede e de tempo de computação escassos.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Escalabilidade e produtividade de treinamento

O personalizador funciona atualizando um modelo que é retreinado com base nas mensagens enviadas de forma assíncrona pelo personalizador após as APIs de classificação e recompensa. Essas mensagens são enviadas usando um EventHub do Azure para o aplicativo.

 É improvável que a maioria dos aplicativos alcance a taxa de transferência máxima de junção e treinamento do personalizador. Embora atingir esse máximo não torne o aplicativo mais lento, isso significa que as filas do hub de eventos estão sendo preenchidas internamente mais rapidamente do que podem ser limpas.

## <a name="how-to-estimate-your-throughput-requirements"></a>Como estimar seus requisitos de taxa de transferência

* Estimar o número médio de bytes por evento de classificação adicionando os comprimentos dos documentos JSON de contexto e ação.
* Dividir 20 MB/s por esta média de bytes estimados.

Por exemplo, se a carga média tiver 500 recursos e cada um for um número estimado de 20 caracteres, cada evento será de aproximadamente 10 KB. Com essas estimativas, 20 milhões/10.000 = 2.000 eventos/s, que é de cerca de 173 milhões eventos/dia. 

Se você estiver atingindo esses limites, entre em contato com nossa equipe de suporte para obter conselhos sobre arquitetura.

## <a name="next-steps"></a>Passos Seguintes

[Criar e configurar](how-to-settings.md)o personalizador.