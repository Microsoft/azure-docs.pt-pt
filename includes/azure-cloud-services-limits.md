---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334892"
---
| Recurso | Limite |
| --- | --- |
| [Funções web ou trabalhador por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Pontos finais](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) de entrada por exemplo por implantação |25 |
| [Pontos finais](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) de entrada por implantação |25 |
| [Pontos finais internos](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) por implantação |25 |
| [Certificados de serviço hospedados](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |

<sup>1</sup> Cada Serviço Azure Cloud com funções web ou operárias pode ter duas implementações, uma para produção e outra para encenação. Este limite refere-se ao número de funções distintas, isto é, configuração. Este limite não se refere ao número de instâncias por função, isto é, escalando.

