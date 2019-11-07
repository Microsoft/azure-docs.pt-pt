---
title: Estatísticas de latência de ida e volta da rede do Azure | Microsoft Docs
description: Saiba mais sobre estatísticas de latência de ida e volta entre regiões do Azure.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587592"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede do Azure

O Azure monitora continuamente a latência (velocidade) das áreas principais de sua rede usando ferramentas de monitoramento internas, bem como as medidas coletadas pelo [ThousandEyes](https://thousandeyes.com), um serviço de monitoramento sintético de terceiros.

## <a name="how-are-the-measurements-collected"></a>Como as medições são coletadas?

As medidas de latência são coletadas de agentes ThousandEyes hospedados em regiões de nuvem do Azure em todo o mundo, que enviam continuamente investigações de rede entre si, em intervalos de 1 minuto. As estatísticas mensais de latência são derivadas da média dos exemplos coletados para o mês.

## <a name="october-2019-latency-figures"></a>Números de latência de 2019 de outubro

Para os 31 dias que terminam em 31 de outubro de 2019, os tempos de latência de viagem de ida e volta mensais min e Max dentro de regiões agregadas são:

- **5 ms** a **72 MS** para viagens de ida e volta dentro de regiões **América do Norte** .
- **3 MS** a **28 MS** para viagens de ida e volta nas regiões da **Europa** .
- **4 MS** a **134 MS** para viagens de ida e volta nas regiões da **Ásia** .

As seguintes medidas de latência entre regiões são alimentadas por [ThousandEyes](https://thousandeyes.com). A unidade de medida na tabela abaixo está em milissegundos (MS).

![Estatísticas de latência entre regiões do Azure](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).