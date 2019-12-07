---
title: Estatísticas de latência de ida e volta da rede do Azure | Microsoft Docs
description: Saiba mais sobre estatísticas de latência de ida e volta entre regiões do Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893095"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede do Azure

O Azure monitora continuamente a latência (velocidade) das áreas principais de sua rede usando ferramentas de monitoramento internas, bem como as medidas coletadas pelo [ThousandEyes](https://thousandeyes.com), um serviço de monitoramento sintético de terceiros.

## <a name="how-are-the-measurements-collected"></a>Como as medições são coletadas?

As medições de latência são coletadas de agentes ThousandEyes, hospedados em regiões de nuvem do Azure em todo o mundo, que enviam continuamente investigações de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência são derivadas da média dos exemplos coletados para o mês.

## <a name="november-2019-latency-figures"></a>Números de latência de 2019 de novembro

**Atualização de novembro:** 3 regiões adicionadas.

* Leste da Noruega
* Oeste da Noruega
* Austrália

Os tempos de ida e volta médio mensais entre as regiões do Azure para os últimos 30 dias (terminando em 30 de novembro de 2019) são mostrados abaixo. As medidas a seguir são alimentadas por [ThousandEyes](https://thousandeyes.com).

![Estatísticas de latência entre regiões do Azure](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
