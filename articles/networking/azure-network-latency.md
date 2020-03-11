---
title: Estatísticas de latência de ida e volta da rede Azure Microsoft Docs
description: Conheça as estatísticas de latência de ida e volta entre as regiões de Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082948"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede Azure

O Azure monitoriza continuamente a latência (velocidade) das áreas centrais da sua rede utilizando ferramentas de monitorização internas, bem como medições recolhidas pela [ThousandEyes](https://thousandeyes.com), um serviço de monitorização sintética de terceiros.

## <a name="how-are-the-measurements-collected"></a>Como são recolhidas as medições?

As medições de latência são recolhidas junto de agentes da ThousandEyes, alojados em regiões de nuvem azure em todo o mundo, que enviam continuamente sondas de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência são derivadas da média das amostras recolhidas para o mês.

## <a name="february-2020-round-trip-latency-figures"></a>Fevereiro de 2020 números de latência de ida e volta

Os tempos médios mensais de ida e volta entre as regiões de Azure nos últimos 29 dias (que terminam em 29 de fevereiro de 2020) são apresentados abaixo. As seguintes medições são alimentadas pela [ThousandEyes](https://thousandeyes.com).

[![estatísticas de latência inter-região azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Conheça [as regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
