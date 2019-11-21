---
title: Azure CLI Samples for Load Balancer
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
# <a name="azure-cli-samples-for-load-balancer"></a>Azure CLI Samples for Load Balancer

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|-|-|
| [Load balance traffic to VMs for high availability](./scripts/load-balancer-linux-cli-sample-nlb.md) | Creates several virtual machines in a highly available and load balanced configuration. |
| [Balancear carga de VMs por zonas de disponibilidade](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Creates three VMs in different availability zones within a region and Standard Load Balancer with a zone-redundant frontend IP address. This load balancer configuration helps to protect your apps and data from an unlikely failure or loss of an entire datacenter. |
|[Balanceamento de carga de VMs dentro de uma zona de disponibilidade específica](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Creates three VMs, a Standard Load Balancer with zonal frontend IP that helps align data path and resources in a single zone for a given region.|
| [Load balance multiple websites on VMs](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Creates two VMs with multiple IP configurations, joined to an Azure Availability Set, accessible through an Azure Load Balancer. |
| | |

