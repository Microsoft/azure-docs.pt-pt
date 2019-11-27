---
title: incluir ficheiro
description: incluir ficheiro
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0695416c65eed2bbf0a19d5ed1ea0c53a7ece332
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485510"
---
| Recurso | Limite predefinido |
| --- | :--- |
| Máximo de clusters por assinatura | 100 |
| Máximo de nós por cluster com conjuntos de disponibilidade de máquina virtual e SKU de Load Balancer básica  | 100 |
| Máximo de nós por cluster com conjuntos de dimensionamento de máquinas virtuais e [Standard Load BALANCER SKU][standard-load-balancer] | 800 (100 nós por [pool de nós][node-pool]) |
| Pods máxima por nó: [sistema de rede básico][basic-networking] com Kubenet | 110 |
| Pods máxima por nó: [rede avançada][advanced-networking] com a interface de rede de contêiner do Azure | Implementação da CLI do Azure: 30<sup>1</sup><br />Modelo de Azure Resource Manager: 30<sup>1</sup><br />Implementação do portal: 30 |

<sup>1</sup> Quando você implanta um cluster do AKS (serviço kubernetes do Azure) com o CLI do Azure ou um modelo do Resource Manager, esse valor é configurado até 250 pods por nó. Não é possível configurar o pods máximo por nó depois que você já tiver implantado um cluster AKS ou se você implantar um cluster usando o portal do Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
