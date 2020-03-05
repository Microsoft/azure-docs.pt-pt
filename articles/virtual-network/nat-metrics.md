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
ms.date: 03/03/2020
ms.author: allensu
ms.openlocfilehash: 6bffa114b325e8b98f439e7ff2ce21480407a643
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274915"
---
# <a name="azure-virtual-network-nat-metrics"></a>Métricas NAT da Rede Virtual Azure

Os recursos de gateway da Rede Virtual Azure NAT fornecem métricas multidimensionais. Pode utilizar estas métricas para observar a operação e para [resolução de problemas.](nat-metrics.md)  Os alertas podem ser configurados para questões críticas como a exaustão do SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Rede Virtual NAT para saída para a Internet">
</p>

*Figura: Rede Virtual NAT para saída para a Internet*

>[!NOTE] 
>A Rede Virtual NAT está disponível como pré-visualização pública neste momento. Atualmente só está disponível num conjunto limitado de [regiões.](nat-overview.md#region-availability) Esta pré-visualização é fornecida sem um acordo de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter mais informações.

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

- Conheça a [Rede Virtual NAT](nat-overview.md)
- Saiba mais sobre o [recurso de gateway NAT](nat-gateway-resource.md)
- Conheça o [Monitor Azure](../azure-monitor/overview.md)
