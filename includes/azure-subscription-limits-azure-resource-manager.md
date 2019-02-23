---
title: incluir ficheiro
description: incluir ficheiro
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 29fce45da89a20681f725f5abd38f534d300f829
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741250"
---
| Recurso | Limite Predefinido | Limite Máximo |
| --- | --- | --- |
| VMs por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |25.000 <sup>1</sup> por região |25.000 por região |
| Total de núcleos de VM por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por Região | Contactar o suporte |
| Núcleos de VM por série (Dv2, F, etc.) por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por Região | Contactar o suporte |
| [Coadministradores](../articles/billing-add-change-azure-subscription-administrator.md) por subscrição |Ilimitado |Ilimitado |
| [Contas de armazenamento](../articles/storage/common/storage-quickstart-create-account.md) por região por subscrição |200 |200<sup>2</sup> |
| [Grupos de Recursos](../articles/azure-resource-manager/resource-group-overview.md) por subscrição |980 |980 |
| [Conjuntos de Disponibilidade](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por subscrição |2000 por Região |2000 por Região |
| Tamanho do pedido da API do Resource Manager |4 194 304 bytes |4 194 304 bytes |
| Etiquetas por subscrição<sup>3</sup> |ilimitado |ilimitado |
| Cálculos de etiquetas únicas por subscrição<sup>3</sup> | 10,000 | 10,000 |
| [Serviços cloud](../articles/cloud-services/cloud-services-choose-me.md) por subscrição |Não Aplicável<sup>4</sup> |Não Aplicável<sup>4</sup> |
| [Grupos de afinidade](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por subscrição |Não Aplicável<sup>4</sup> |Não Aplicável<sup>4</sup> |
| [Implementações de nível de subscrição](../articles/azure-resource-manager/deploy-to-subscription.md) por localização | 800 | 800 |

<sup>1</sup>Os limites predefinidos variam consoante o Tipo de Categoria da oferta, como Avaliação Gratuita, Pay As You Go e série, como Dv2, F, G, etc.

<sup>2</sup>Isto inclui as contas de armazenamento Standard e Premium. Se necessitar de mais de 200 contas de armazenamento, efetue um pedido através do [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipa do Armazenamento do Azure analisará o seu caso comercial e pode aprovar até 250 contas de armazenamento.

<sup>3</sup>Pode aplicar um número ilimitado de etiquetas por subscrição. O número de etiquetas por recurso ou grupo de recursos está limitado a 15. O Resource Manager devolve apenas uma [lista de nomes e valores de etiquetas únicas](/rest/api/resources/tags) na subscrição quando o número de etiquetas for 10 000 ou inferior. No entanto, pode ainda encontrar um recurso por etiqueta quando o número exceder 10 000.  

<sup>4</sup>Estas funcionalidades já não são necessárias nos Grupos de Recursos do Azure e no Azure Resource Manager.

> [!NOTE]
> É importante realçar que os núcleos de máquina virtual têm um limite total regional, bem como um limite regional por série de tamanho (Dv2, F, etc.) que são impostos em separado.  Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região EUA Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30.  Esta subscrição poderá implementar 30 VMs A1, 30 VMs D1 ou uma combinação de ambas que não excedam um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).  
> <!-- -->
> 
> 

