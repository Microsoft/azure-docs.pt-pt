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
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335061"
---
| Recurso | Limite |
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
