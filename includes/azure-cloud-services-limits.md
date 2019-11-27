---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 24e327a0b669df247d44e4bf8f05b693abb49990
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224327"
---
| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| [Funções Web ou de trabalho por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Pontos de extremidade de entrada de instância](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) por implantação |25 |25 |
| [Pontos de extremidade de entrada](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) por implantação |25 |25 |
| [Pontos de extremidade internos](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) por implantação |25 |25 |
| [Certificados de serviço hospedado](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |199 |

<sup>1</sup> Cada serviço de nuvem do Azure com funções Web ou de trabalho pode ter duas implantações, uma para produção e outra para preparo. Esse limite se refere ao número de funções distintas, ou seja, configuração. Esse limite não se refere ao número de instâncias por função, ou seja, dimensionamento.

