---
title: Escalabilidade e Desempenho - Personalizer
titleSuffix: Azure Cognitive Services
description: 'Sites e aplicações de alto desempenho e de alto tráfego têm dois fatores principais a considerar com o Personalizer para escalabilidade e desempenho: latência e produção de formação.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73490782"
---
# <a name="scalability-and-performance"></a>Escalabilidade e Desempenho

Sites e aplicações de alto desempenho e de alto tráfego têm dois fatores principais a considerar com o Personalizer para escalabilidade e desempenho:

* Manter baixa latência ao fazer chamadas de Rank API
* Certificando-se de que a entrada de treino acompanha a entrada do evento

A personalização pode devolver rapidamente uma classificação, com a maior parte da duração da chamada dedicada à comunicação através da API REST. O Azure irá automaticamente escalar a capacidade de responder rapidamente aos pedidos.

##  <a name="low-latency-scenarios"></a>Cenários de baixa latência

Algumas aplicações requerem baixas tardios ao devolver uma patente. São necessárias baixas tardios:

* Para evitar que o utilizador aguarde uma quantidade notável de tempo antes de apresentar conteúdo classificado.
* Para ajudar um servidor que está a experimentar tráfego extremo, evite ligar o tempo escasso da computação e as ligações de rede.


## <a name="scalability-and-training-throughput"></a>Escalabilidade e formação

O Personalizer trabalha atualizando um modelo que é retreinado com base em mensagens enviadas assincronicamente pela Personalizer após ABIs de Rank e Reward. Estas mensagens são enviadas através de um Azure EventHub para a aplicação.

 É improvável que a maioria das aplicações atinja o máximo de adesão e formação do Personalizer. Ao atingir este máximo não irá abrandar a aplicação, isso implicaria que as filas do Event Hub estão a ser preenchidas internamente mais rapidamente do que podem ser limpas.

## <a name="how-to-estimate-your-throughput-requirements"></a>Como estimar os seus requisitos de entrada

* Calcule o número médio de bytes por evento de classificação adicionando os comprimentos do contexto e da ação documentos JSON.
* Divida 20MB/seg por este bytes médios estimados.

Por exemplo, se a sua carga média tem 500 funcionalidades e cada uma tem cerca de 20 caracteres, então cada evento é aproximadamente 10kb. Com estas estimativas, 20.000.000 / 10.000 = 2.000 eventos/seg, que são cerca de 173 milhões de eventos/dia. 

Se você está atingindo estes limites, por favor contacte a nossa equipe de apoio para obter conselhos de arquitetura.

## <a name="next-steps"></a>Passos seguintes

[Criar e configurar personalizer](how-to-settings.md).