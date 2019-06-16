---
title: Aprendizagem por reforço - Personalizer
titleSuffix: Azure Cognitive Services
description: 'Web sites de tráfego elevado e de elevado desempenho e aplicações têm dois fatores principais a serem considerados com Personalizer para escalabilidade e desempenho: latência e débito de treinamento.'
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 242cc1b96c08cd79dc3e2ef5efbbe96a934b8ad3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058442"
---
# <a name="scalability-and-performance"></a>Desempenho e escalabilidade

Web sites de tráfego elevado e de elevado desempenho e aplicações têm dois fatores principais a serem considerados com Personalizer para escalabilidade e desempenho:

* Manter a baixa latência ao efetuar chamadas de API de classificação
* Certificar-se de que o débito de treinamento acompanha das entradas de evento

Personalização pode devolver uma classificação muito rapidamente, com a maioria da duração da chamada dedicada a comunicação através da API REST. Azure será dimensionado automaticamente a capacidade de responder a pedidos rapidamente.

##  <a name="low-latency-scenarios"></a>Cenários de baixa latência

Algumas aplicações necessitam de latências baixas ao retornar uma classificação. Isso é necessário:

* Para manter o usuário de aguardar o processamento uma quantidade considerável de tempo antes de exibir com a classificação de conteúdo.
* Para ajudar a um servidor que está a ter o tráfego extreme evitar prender ligações de rede e de tempo de computação escassos.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Escalabilidade e o débito de treinamento

Personalizer funciona atualizando um modelo que é reestruturar com base em mensagens enviadas de forma assíncrona por Personalizer após a classificação e APIs de recompensa. Estas mensagens são enviadas através de um EventHub do Azure para a aplicação.

 Não é provável que a maioria dos aplicativos atingirá o máximo de associação e o débito de treinamento de Personalizer. Embora a atingir esse máximo não diminuirá o aplicativo, poderia implicar filas do Hub de eventos são obtenção preenchidas internamente mais rapidamente do que eles podem ser limpos.

## <a name="how-to-estimate-your-throughput-requirements"></a>Como estimar os requisitos de débito

* Calcule o número médio de bytes por eventos de classificação a adicionar os comprimentos dos contexto e a ação de documentos JSON.
* 20MB/seg de divisão por esta estimado média de bytes.

Por exemplo, se a carga média tem 500 recursos e cada uma tem um estimado 20 carateres, em seguida, cada evento é aproximadamente 10kb. Com essas estimativas, 20.000.000 / 10 000 = 2.000 eventos/seg, que é cerca de 173 milhões de eventos/dia. 

Se estiver a atingir estes limites, contacte a nossa equipa de suporte para orientação de arquitetura.

## <a name="next-steps"></a>Passos Seguintes

[Criar e configurar Personalizer](how-to-settings.md).