---
title: incluir ficheiro
description: incluir ficheiro
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557111"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| vCPUs por [subscrição](https://azure.microsoft.com/pricing/)<sup>1</sup> |20 |10,000 |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por subscrição |200 |200 |
| [Contas de armazenamento](../articles/storage/common/storage-account-create.md) por subscrição<sup>2</sup> |100 |100 |
| [Serviços cloud](../articles/cloud-services/cloud-services-choose-me.md) por subscrição |20 |200 |
| [Redes locais](/previous-versions/azure/reference/jj157100(v=azure.100)) por subscrição |10 |500 |
| Servidores DNS por subscrição |9 |100 |
| IPs reservados por subscrição |20 |100 |
| [Grupos de afinidade](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) por subscrição |256 |256 |
| Comprimento do nome da subscrição (caracteres) | 64 | 64 |

<sup>1</sup> Casos extra pequenos contam como um vCPU em direção ao limite vCPU, apesar de usar um núcleo de CPU parcial.

<sup>2</sup> O limite da conta de armazenamento inclui contas de armazenamento Standard e Premium.