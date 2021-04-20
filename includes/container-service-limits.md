---
title: incluir ficheiro
description: incluir ficheiro
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 15e91e6f275c3a6ebe44690441404a38e8f61394
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732556"
---
| Recurso                                                                                                           | Limite                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Aglomerados máximos por subscrição                                                                                  | 1000                                                                                                                                                                                                        |
| Os nódes máximos por cluster com conjuntos de disponibilidade de máquina virtual e SKU de balanceador de carga básico                       | 100                                                                                                                                                                                                         |
| Os nódes máximos por cluster com conjuntos de balança de máquina virtual e [sKU de balançador de carga padrão][standard-load-balancer] | 1000 (em todas as [piscinas de nó)][node-pool]                                            |
| Piscinas máximas de nó por cluster                                                                                     | 100                                                                                  |
| Cápsulas máximas por nó: [Rede básica][basic-networking] com Kubenet                                           | Máximo: 250 <br /> Padrão do Azure CLI: 110 <br /> Padrão do modelo do Gestor de Recursos Azure: 110 <br /> Padrão de implementação do portal Azure: 30          |
| Cápsulas máximas por nó: [Rede avançada][advanced-networking] com interface de rede de contentores Azure        | Máximo: 250 <br /> Predefinição: 30                                                      |
| Pré-visualização do addon AKS da Malha de Serviço Aberto (OSM)                                                                          | Versão do cluster Kubernetes: 1.19+<sup>1</sup><br />Controladores OSM por cluster: 1<sup>1</sup><br />Cápsulas por controlador OSM: 500<sup>1</sup><br />Contas de serviço Kubernetes geridas pela OSM: 50<sup>1</sup> |

<sup>1</sup> O addon OSM para AKS encontra-se em estado de pré-visualização e será submetido a melhorias adicionais antes da disponibilidade geral (GA). Durante a fase de pré-visualização, recomenda-se não ultrapassar os limites mostrados.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
