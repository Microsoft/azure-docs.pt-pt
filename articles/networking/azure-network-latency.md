---
title: Estatísticas de latência de ida e volta da rede Azure Microsoft Docs
description: Conheça as estatísticas de latência de ida e volta entre as regiões de Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 05/26/2020
ms.author: kumud
ms.openlocfilehash: 8cf5d07bb071217a5ecafca8f351c94590291603
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994017"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede Azure

O Azure monitoriza continuamente a latência (velocidade) das áreas centrais da sua rede utilizando ferramentas de monitorização internas, bem como medições recolhidas pela [ThousandEyes](https://thousandeyes.com), um serviço de monitorização sintética de terceiros.

## <a name="how-are-the-measurements-collected"></a>Como são recolhidas as medições?

As medições de latência são recolhidas junto de agentes da ThousandEyes, alojados em regiões de nuvem azure em todo o mundo, que enviam continuamente sondas de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência são derivadas da média das amostras recolhidas para o mês.

## <a name="april-2020-round-trip-latency-figures"></a>Abril de 2020 números de latência de ida e volta

Os tempos médios mensais de ida e volta entre as regiões de Azure nos últimos 30 dias (que terminam em 30 de abril de 2020) são apresentados abaixo. As seguintes medições são alimentadas pela [ThousandEyes](https://thousandeyes.com).

[![Estatísticas de latência inter-região de Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Próximos passos

Conheça [as regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
