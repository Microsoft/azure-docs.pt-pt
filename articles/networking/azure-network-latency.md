---
title: Estatísticas de latência de ida e volta da rede do Azure | Microsoft Docs
description: Saiba mais sobre estatísticas de latência de ida e volta entre regiões do Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 01/08/2020
ms.author: mnayak
ms.openlocfilehash: 91b528cc6900a3ec91ff7189f58f941226b8acd5
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779727"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede do Azure

O Azure monitora continuamente a latência (velocidade) das áreas principais de sua rede usando ferramentas de monitoramento internas, bem como as medidas coletadas pelo [ThousandEyes](https://thousandeyes.com), um serviço de monitoramento sintético de terceiros.

## <a name="how-are-the-measurements-collected"></a>Como as medições são coletadas?

As medições de latência são coletadas de agentes ThousandEyes, hospedados em regiões de nuvem do Azure em todo o mundo, que enviam continuamente investigações de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência são derivadas da média dos exemplos coletados para o mês.

## <a name="december-2019-latency-figures"></a>Números de latência de 2019 de dezembro

Os tempos médios de ida e volta mensais entre as regiões do Azure para os últimos 30 dias (terminando em 31 de dezembro de 2019) são mostrados abaixo. As medidas a seguir são alimentadas por [ThousandEyes](https://thousandeyes.com).

[![estatísticas de latência entre regiões do Azure](media/azure-network-latency/december.jpg)](media/azure-network-latency/december.jpg#lightbox)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
