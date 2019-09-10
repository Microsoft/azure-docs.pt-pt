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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184622"
---
| Resource | Limite predefinido |
| --- | :--- |
| Máximo de clusters por assinatura | 100 |
| Máximo de nós por cluster | 100 |
| Máximo de pods por nó: [Sistema de rede básico][basic-networking] com Kubenet | 110 |
| Máximo de pods por nó: [Rede avançada][advanced-networking] com a interface de rede de contêiner do Azure | Implantação de CLI do Azure: 30<sup>1</sup><br />Modelo de Azure Resource Manager: 30<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup> Quando você implanta um cluster do AKS (serviço kubernetes do Azure) com o CLI do Azure ou um modelo do Resource Manager, esse valor é configurado até 250 pods por nó. Não é possível configurar o pods máximo por nó depois que você já tiver implantado um cluster AKS ou se você implantar um cluster usando o portal do Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
