---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919319"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| [Funções Web ou de trabalho por implementação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Pontos finais de entrada de instância](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) por implementação |25 |25 |
| [Pontos finais de entrada](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) por implementação |25 |25 |
| [Pontos finais internos](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) por implementação |25 |25 |
| [Hospedado certificados de serviço](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implementação |199 |199 |

<sup>1</sup>cada serviço de Cloud do Azure com funções web ou de trabalho pode ter duas implementações, uma para produção e uma para teste. Este limite refere-se ao número de funções distintas, ou seja, a configuração. Este limite não referem-se ao número de instâncias por função, ou seja, dimensionamento.

