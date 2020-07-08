---
title: Amostras de CLI Azure para balanceador de carga
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74225426"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Amostras de CLI Azure para balanceador de carga

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|-|-|
| [Carregar o tráfego de equilíbrio para VMs para uma elevada disponibilidade](./scripts/load-balancer-linux-cli-sample-nlb.md) | Cria várias máquinas virtuais numa configuração altamente disponível e equilibrada de carga. |
| [Balancear carga de VMs por zonas de disponibilidade](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Cria três VMs em diferentes zonas de disponibilidade dentro de uma região e o Balanceador de Carga Padrão com um endereço IP frontal redundante de zona. Esta configuração do balanceador de carga ajuda a proteger as suas apps e dados de uma falha ou perda improvável de um centro de dados inteiro. |
|[Balanceamento de carga de VMs dentro de uma zona de disponibilidade específica](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Cria três VMs, um Balanceador de Carga Padrão com IP frontal zonal que ajuda a alinhar o caminho de dados e os recursos numa única zona para uma determinada região.|
| [Balancei vários websites em VMs](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Cria dois VMs com múltiplas configurações IP, unidos a um Conjunto de Disponibilidade Azure, acessível através de um Balançador de Carga Azure. |
| | |

