---
title: incluir ficheiro
description: incluir ficheiro
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 1c2dec106ae72ddead7bda54792fa74e38eb6660
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081104"
---
| Recurso                                                                                                           | Limite                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Aglomerados máximos por subscrição                                                                                  | 1000                                                                                                                                                                                                        |
| Os nódes máximos por cluster com conjuntos de disponibilidade de máquina virtual e SKU de balanceador de carga básico                       | 100                                                                                                                                                                                                         |
| Os nódes máximos por cluster com conjuntos de balança de máquina virtual e [sKU de balançador de carga padrão][standard-load-balancer] | 1000 (100 nós por [piscina de nó)][node-pool]                                                                                                                                                                 |
| Cápsulas máximas por nó: [Rede básica][basic-networking] com Kubenet                                           | 110                                                                                                                                                                                                         |
| Cápsulas máximas por nó: [Rede avançada][advanced-networking] com interface de rede de contentores Azure        | Implementação da CLI do Azure: 30<sup>1</sup><br />Modelo de gestor de recursos Azure: 30<sup>1</sup><br />Implementação do portal: 30                                                                                        |
| Pré-visualização do addon AKS da Malha de Serviço Aberto (OSM)                                                                          | Versão do cluster Kubernetes: 1.19+<sup>2</sup><br />Controladores OSM por cluster:<sup>12</sup><br />Cápsulas por controlador OSM: 500<sup>2</sup><br />Contas de serviço Kubernetes geridas pela OSM: 50<sup>2</sup> |

<sup>1</sup> Quando implementa um cluster Azure Kubernetes Service (AKS) com o modelo Azure CLI ou um Gestor de Recursos, este valor é configurado até 250 cápsulas por nó. Não é possível configurar cápsulas máximas por nó depois de já ter implantado um cluster AKS, ou se implementar um cluster utilizando o portal Azure.<br />

<sup>2</sup> O addon OSM para AKS encontra-se em estado de pré-visualização e será submetido a melhorias adicionais antes da disponibilidade geral (GA). Durante a fase de pré-visualização é aconselhável não ultrapassar os limites indicados.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
