---
title: Escalabilidade e Performance - Personalização
titleSuffix: Azure Cognitive Services
description: 'Sites e aplicações de alto desempenho e de alto tráfego têm dois fatores principais a considerar com Personalizer para escalabilidade e desempenho: latência e produção de formação.'
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 52d22fce64a26267e73cb9a1df8614129bb96c3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132692"
---
# <a name="scalability-and-performance"></a>Escalabilidade e Performance

Sites e aplicações de alto desempenho e de alto tráfego têm dois fatores principais a considerar com o Personalizer para escalabilidade e desempenho:

* Manter baixa latência ao fazer chamadas de API de classificação
* Certificando-se de que a produção de treino mantém-se com a entrada do evento

A personalização pode devolver rapidamente uma classificação, com a maior parte da duração da chamada dedicada à comunicação através da API REST. O Azure irá autodimensionar a capacidade de responder rapidamente aos pedidos.

##  <a name="low-latency-scenarios"></a>Cenários de baixa latência

Algumas aplicações requerem baixas latências ao devolver um posto. São necessárias baixas latências:

* Para evitar que o utilizador espere um tempo notável antes de exibir o conteúdo classificado.
* Para ajudar um servidor que está a experimentar tráfego extremo, evite a ligar o tempo de computação escasso e as ligações de rede.


## <a name="scalability-and-training-throughput"></a>Escalabilidade e produção de formação

O personalizador funciona atualizando um modelo que é retreinado com base em mensagens enviadas assíncroneamente pelo Personaler after Rank e Reward APIs. Estas mensagens são enviadas através de um Azure EventHub para a aplicação.

 É improvável que a maioria das aplicações atinja o máximo de adesão e formação do Personalizer. Embora atingir este máximo não abrande a aplicação, isso implicaria que as filas do Event Hub estão a ser preenchidas internamente mais rapidamente do que podem ser limpas.

## <a name="how-to-estimate-your-throughput-requirements"></a>Como estimar os seus requisitos de produção

* Estimar o número médio de bytes por evento de classificação adicionando os comprimentos do contexto e ação documentos JSON.
* Divida 20MB/seg por esta média estimada bytes.

Por exemplo, se a sua carga útil média tem 500 funcionalidades e cada uma é estimada em 20 caracteres, então cada evento é de aproximadamente 10kb. Com estas estimativas, 20.000.000 / 10.000 = 2.000 eventos/seg, que é cerca de 173 milhões de eventos/dia. 

Se estiver a atingir estes limites, contacte a nossa equipa de apoio para obter conselhos de arquitetura.

## <a name="next-steps"></a>Passos seguintes

[Criar e configurar personalizante.](how-to-settings.md)