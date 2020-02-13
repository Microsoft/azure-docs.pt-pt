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
ms.openlocfilehash: ac928d9087ba5db312540b8ec542d7a2a29e2a99
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179147"
---
| Recurso | Limite predefinido |
| --- | :--- |
| Clusters máximos por subscrição | 100 |
| Nómáximo por cluster com conjuntos de disponibilidade de máquinas virtuais e Balancer de carga básica SKU  | 100 |
| Nómáximo por cluster com conjuntos de escala de máquina virtual e [Balancer de carga padrão SKU][standard-load-balancer] | 1000 (100 nós por piscina de [nós)][node-pool] |
| Cápsulas máximas por nó: [Networking básico][basic-networking] com Kubenet | 110 |
| Cápsulas máximas por nó: [Rede avançada][advanced-networking] com interface de rede de contentores Azure | Implementação da CLI do Azure: 30<sup>1</sup><br />Modelo de Gestor de Recursos Azure: 30<sup>1</sup><br />Implementação do portal: 30 |

<sup>1</sup> Quando implementa um cluster de Serviço Azure Kubernetes (AKS) com o Modelo Azure CLI ou um Gestor de Recursos, este valor é configurável até 250 cápsulas por nó. Não é possível configurar cápsulas máximas por nó depois de já ter implantado um cluster AKS, ou se implementar um cluster utilizando o portal Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
