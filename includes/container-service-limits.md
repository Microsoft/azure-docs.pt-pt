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
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554754"
---
| Recurso | Limite predefinido |
| --- | :--- |
| Clusters máximos por subscrição | 100 |
| Número máximo de nós por cluster | 100 |
| Pods máximas por nó: [Rede básica] [ basic-networking] com Kubenet | 110 |
| Pods máximas por nó: [Advanced networking] [ advanced-networking] com Interface de rede de contentor do Azure | Implementação de CLI do Azure: 30<sup>1</sup><br />Modelo de Gestor de recursos do Azure: 30<sup>1</sup><br />Implementação do portal: 30 |

<sup>1</sup>quando implementar um cluster do Azure Kubernetes Service (AKS) com a CLI do Azure ou um modelo do Resource Manager, este valor é configurável até 110 pods por nó. Não é possível configurar pods máximas por nó, depois de já implementar um cluster do AKS, ou se implementar um cluster com o portal do Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
