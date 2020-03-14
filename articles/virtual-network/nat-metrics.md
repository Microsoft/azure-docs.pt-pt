---
title: Métricas e alertas para a Rede Virtual Azure NAT
titleSuffix: Azure Virtual Network
description: Compreenda as métricas do Monitor Azure e os alertas disponíveis para a Rede Virtual NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 4f1760c32117b34d4d453964473cba3c7d07f725
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79202183"
---
# <a name="azure-virtual-network-nat-metrics"></a>Métricas NAT da Rede Virtual Azure

Os recursos de gateway da Rede Virtual Azure NAT fornecem métricas multidimensionais. Pode utilizar estas métricas para observar a operação e para [resolução de problemas.](nat-metrics.md)  Os alertas podem ser configurados para questões críticas como a exaustão do SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Rede Virtual NAT para saída para a Internet">
</p>

*Figura: Rede Virtual NAT para saída para a Internet*

## <a name="metrics"></a>Métricas

Os recursos de gateway NAT fornecem as seguintes métricas multidimensionais no Monitor Azure:

| Métrica | Descrição | Agregação Recomendada | Dimensões |
|---|---|---|---|
| Bytes | Bytes processados de entrada e saída | Soma | Direção (In; Out), Protocolo (6 TCP; 17 UDP) |
| Pacotes | Pacotes processados de entrada e saída | Soma | Direção (In; Out), Protocolo (6 TCP; 17 UDP) |
| Pacotes caídos | Pacotes deixados pelo portal NAT | Soma | / |
| Contagem de ligação sNAT | Transições estatais por intervalo | Soma | Estado de ligação, protocolo (6 TCP; 17 UDP) |
| Contagem total de ligação SNAT | Ligações Ativas Atuais (~ portas SNAT em uso) | Soma | Protocolo (6 TCP; 17 UDP) |


## <a name="alerts"></a>Alertas

Os alertas para métricas podem ser configurados no Monitor Azure para cada uma das [métricas](#metrics)anteriores .

## <a name="limitations"></a>Limitações

A Saúde dos Recursos não é apoiada.

## <a name="next-steps"></a>Passos seguintes

* Conheça a [Rede Virtual NAT](nat-overview.md)
* Saiba mais sobre o [recurso de gateway NAT](nat-gateway-resource.md)
* Conheça o [Monitor Azure](../azure-monitor/overview.md)
* Saiba mais sobre a resolução de recursos de [gateway na NAT.](troubleshoot-nat.md)
* [Diga-nos o que construir a seguir para a Rede Virtual NAT no UserVoice](https://aka.ms/natuservoice).


