---
title: Exemplos CLI do Azure para o Balanceador de carga
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225426"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Exemplos CLI do Azure para o Balanceador de carga

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|-|-|
| [Balancear a carga do tráfego para as VMs para alta disponibilidade](./scripts/load-balancer-linux-cli-sample-nlb.md) | Cria várias máquinas virtuais numa elevada disponibilidade e a configuração de balanceamento de carga. |
| [Balancear carga de VMs por zonas de disponibilidade](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Cria três VMs em zonas de disponibilidade diferentes dentro de uma região e o Balanceador de carga Standard com um endereço IP de front-end com redundância de zona. Esta configuração de Balanceador de carga ajuda a proteger as aplicações e dados de uma falha pouco provável ou perda de todo o datacenter. |
|[Balanceamento de carga de VMs dentro de uma zona de disponibilidade específica](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Cria três VMs, um balanceador de carga Standard com o IP de front-end zonal que ajuda a alinhar o caminho de dados e recursos numa única zona para uma determinada região.|
| [Balancear a carga de vários sites em VMs](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Cria duas VMs com várias configurações de IP, associadas ao Azure conjunto de disponibilidade, acessível através de um balanceador de carga do Azure. |
| | |

