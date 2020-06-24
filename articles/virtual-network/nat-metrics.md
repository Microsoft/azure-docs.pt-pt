---
title: Métricas e alertas para Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Compreenda as métricas e alertas do Azure Monitor disponíveis para o NAT de Rede Virtual.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 75e20a4fb91b73bc353d347f0b34f9be8f7a8a58
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84709951"
---
# <a name="azure-virtual-network-nat-metrics"></a>Métricas NAT de rede virtual Azure

Os recursos de gateway da Rede Virtual Azure fornecem métricas multidimensionais. Pode utilizar estas métricas para observar a operação e para [a resolução de problemas](troubleshoot-nat.md).  Os alertas podem ser configurados para questões críticas como a exaustão do SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Rede Virtual NAT para saída para a Internet">
</p>

*Figura: Rede Virtual NAT para saída para a Internet*

## <a name="metrics"></a>Métricas

Os recursos de gateway NAT fornecem as seguintes métricas multidimensionais no Azure Monitor:

| Metric | Descrição | Agregação recomendada | Dimensões |
|---|---|---|---|
| Bytes | Bytes processados de entrada e saída | Soma | Direção (Em; Out), Protocolo (6 TCP; 17 UDP) |
| Pacotes | Pacotes processados de entrada e saída | Soma | Direção (Em; Out), Protocolo (6 TCP; 17 UDP) |
| Pacotes abandonados | Pacotes deixados pela porta de entrada da NAT | Soma | / |
| Contagem de ligação SNAT | Transições estatais por intervalo | Soma | Estado de Ligação, Protocolo (6 TCP; 17 UDP) |
| Contagem total de ligação SNAT | Ligações SNAT ativas atuais (~ portas SNAT em uso) | Soma | Protocolo (6 TCP; 17 UDP) |


## <a name="alerts"></a>Alertas

Os alertas para as métricas podem ser configurados no Monitor Azure para cada uma das [métricas anteriores](#metrics).

## <a name="limitations"></a>Limitações

A Saúde dos Recursos não é apoiada.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Rede Virtual NAT](nat-overview.md)
* Saiba mais sobre [o recurso de gateway NAT](nat-gateway-resource.md)
* Saiba mais sobre [o Azure Monitor](../azure-monitor/overview.md)
* Saiba mais sobre [a resolução de problemas dos recursos de gateway DA NAT.](troubleshoot-nat.md)
* [Diga-nos o que construir a seguir para o NAT de Rede Virtual no UserVoice](https://aka.ms/natuservoice).


