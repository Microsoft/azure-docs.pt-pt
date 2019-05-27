---
title: incluir ficheiro
description: incluir ficheiro
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143005"
---
| Resource | Limite predefinido |
| --- | :--- |
| Clusters máximos por subscrição | 100 |
| Número máximo de nós por cluster | 100 |
| Pods máximas por nó: [Rede básica] [ basic-networking] com Kubenet | 110 |
| Pods máximas por nó: [Advanced networking] [ advanced-networking] com Interface de rede de contentor do Azure | Implementação de CLI do Azure: 30<sup>1</sup><br />Modelo de Gestor de recursos do Azure: 30<sup>1</sup><br />Implementação do portal: 30 |

<sup>1</sup>quando implementar um cluster do Azure Kubernetes Service (AKS) com a CLI do Azure ou um modelo do Resource Manager, este valor é configurável até 250 pods por nó. Não é possível configurar pods máximas por nó, depois de já implementar um cluster do AKS, ou se implementar um cluster com o portal do Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
