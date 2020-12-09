---
title: Estatísticas de latência de ida e volta da rede Azure Microsoft Docs
description: Saiba mais sobre estatísticas de latência de ida e volta entre as regiões de Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/07/2020
ms.author: kumud
ms.openlocfilehash: fb828d239266691766f55c1b156831afab7cc5bc
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854691"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede Azure

O Azure monitoriza continuamente a latência (velocidade) das áreas centrais da sua rede utilizando ferramentas de monitorização internas, bem como medições recolhidas pela ThousandEyes , um serviço de monitorização sintética de [terceiros.](https://thousandeyes.com)

## <a name="how-are-the-measurements-collected"></a>Como são recolhidas as medições?

As medições de latência são recolhidas a partir de agentes da ThousandEyes, hospedados em regiões de nuvem Azure em todo o mundo, que enviam continuamente sondas de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência derivam da média das amostras recolhidas para o mês.

## <a name="november-2020-round-trip-latency-figures"></a>Números de latência de ida e volta de novembro de 2020

Os tempos médios mensais de ida e volta entre as regiões de Azure nos últimos 30 dias (terminando em 30 de novembro de 2020) são mostrados abaixo. As seguintes medições são alimentadas por [ThousandEyes](https://thousandeyes.com).

[![Estatísticas de latência inter-região do Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Conheça as [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
