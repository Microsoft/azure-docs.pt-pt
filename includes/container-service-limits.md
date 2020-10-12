---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80335061"
---
| Recurso | Limite |
| --- | :--- |
| Aglomerados máximos por subscrição | 100 |
| Os nódes máximos por cluster com conjuntos de disponibilidade de máquina virtual e SKU de balanceador de carga básico  | 100 |
| Os nódes máximos por cluster com conjuntos de balança de máquina virtual e [sKU de balançador de carga padrão][standard-load-balancer] | 1000 (100 nós por [piscina de nó)][node-pool] |
| Cápsulas máximas por nó: [Rede básica][basic-networking] com Kubenet | 110 |
| Cápsulas máximas por nó: [Rede avançada][advanced-networking] com interface de rede de contentores Azure | Implementação da CLI do Azure: 30<sup>1</sup><br />Modelo de gestor de recursos Azure: 30<sup>1</sup><br />Implementação do portal: 30 |

<sup>1</sup> Quando implementa um cluster Azure Kubernetes Service (AKS) com o modelo Azure CLI ou um Gestor de Recursos, este valor é configurado até 250 cápsulas por nó. Não é possível configurar cápsulas máximas por nó depois de já ter implantado um cluster AKS, ou se implementar um cluster utilizando o portal Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
