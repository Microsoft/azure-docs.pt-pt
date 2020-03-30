---
title: Amostras de CLI azure para balanceador de carga
titleSuffix: Azure Load Balancer
description: Exemplos da CLI do Azure
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: f35007bc3c47aeec9bcdd8a418983b95f6f20ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225426"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Amostras de CLI azure para balanceador de carga

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|-|-|
| [Tráfego de equilíbrio de carga para VMs para alta disponibilidade](./scripts/load-balancer-linux-cli-sample-nlb.md) | Cria várias máquinas virtuais numa configuração altamente disponível e equilibrada em carga. |
| [Balancear carga de VMs por zonas de disponibilidade](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Cria três VMs em diferentes zonas de disponibilidade dentro de uma região e o Standard Load Balancer com um endereço IP frontal redundante. Esta configuração de equilíbrio de carga ajuda a proteger as suas aplicações e dados de uma falha improvável ou perda de um centro de dados inteiro. |
|[Balanceamento de carga de VMs dentro de uma zona de disponibilidade específica](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Cria três VMs, um Balancer de Carga Padrão com IP frontal zonal que ajuda a alinhar a trajetória de dados e os recursos numa única zona para uma determinada região.|
| [Balanço de carga vários websites em VMs](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Cria dois VMs com múltiplas configurações IP, unidos a um Conjunto de Disponibilidade Azure, acessível através de um Equilíbrio de Carga Azure. |
| | |

