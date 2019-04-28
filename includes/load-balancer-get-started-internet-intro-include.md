---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517002"
---
Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece elevada disponibilidade, ao distribuir o tráfego de entrada entre instâncias de serviço com bom estado de funcionamento nos serviços cloud ou máquinas virtuais num conjunto de balanceador de carga. O Balanceador de Carga do Azure pode também apresentar esses serviços em várias portas, vários endereços IP ou ambos.

Pode configurar um balanceador de carga para:

* Balancear a carga do tráfego de entrada da Internet para máquinas virtuais (VMs). Referimos um balanceador de carga neste cenário como um [Balanceador de carga com acesso à Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Efetue o balanceamento de carga do tráfego entre VMs numa rede virtual (VNet), entre VMs nos serviços cloud ou entre computadores e VMs no local numa rede virtual entre locais. Referimos um balanceador de carga neste cenário como um [balanceador de carga interno (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Reencaminhe tráfego externo para uma instância específica da VM.