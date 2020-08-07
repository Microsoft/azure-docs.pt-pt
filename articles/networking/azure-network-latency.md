---
title: Estatísticas de latência de ida e volta da rede Azure Microsoft Docs
description: Saiba mais sobre estatísticas de latência de ida e volta entre as regiões de Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 1898bcf619f1fc9aaf19384968225e35c9589688
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847448"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede Azure

O Azure monitoriza continuamente a latência (velocidade) das áreas centrais da sua rede utilizando ferramentas de monitorização internas, bem como medições recolhidas pela ThousandEyes , um serviço de monitorização sintética de [terceiros.](https://thousandeyes.com)

## <a name="how-are-the-measurements-collected"></a>Como são recolhidas as medições?

As medições de latência são recolhidas a partir de agentes da ThousandEyes, hospedados em regiões de nuvem Azure em todo o mundo, que enviam continuamente sondas de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência derivam da média das amostras recolhidas para o mês.

## <a name="july-2020-round-trip-latency-figures"></a>Julho de 2020 números de latência de ida e volta

Os tempos médios mensais de ida e volta entre as regiões de Azure nos últimos 31 dias (terminando em 31 de julho de 2020) são apresentados abaixo. As seguintes medições são alimentadas por [ThousandEyes](https://thousandeyes.com).

[![Estatísticas de latência inter-região do Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Conheça as [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
