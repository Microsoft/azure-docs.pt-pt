---
title: Estatísticas de latência de ida e volta da rede Azure Microsoft Docs
description: Saiba mais sobre estatísticas de latência de ida e volta entre as regiões de Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 72168a56bfb4e08c7f44c84c773d9f6599cfa607
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91848892"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede Azure

O Azure monitoriza continuamente a latência (velocidade) das áreas centrais da sua rede utilizando ferramentas de monitorização internas, bem como medições recolhidas pela ThousandEyes , um serviço de monitorização sintética de [terceiros.](https://thousandeyes.com)

## <a name="how-are-the-measurements-collected"></a>Como são recolhidas as medições?

As medições de latência são recolhidas a partir de agentes da ThousandEyes, hospedados em regiões de nuvem Azure em todo o mundo, que enviam continuamente sondas de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência derivam da média das amostras recolhidas para o mês.

## <a name="september-2020-round-trip-latency-figures"></a>Números de latência de ida e volta de setembro de 2020

Os tempos médios mensais de ida e volta entre as regiões de Azure nos últimos 30 dias (terminando em 30 de setembro de 2020) são apresentados abaixo. As seguintes medições são alimentadas por [ThousandEyes](https://thousandeyes.com).

[![Estatísticas de latência inter-região do Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Conheça as [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
