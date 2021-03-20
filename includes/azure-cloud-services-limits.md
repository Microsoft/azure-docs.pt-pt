---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85838906"
---
| Recurso | Limite |
| --- | --- |
| [Funções web ou de trabalhador por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Pontos finais de entrada de exemplo](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) por implantação |25 |
| [Pontos finais de entrada](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) por implantação |25 |
| [Pontos finais internos](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) por implantação |25 |
| [Certificados de serviço hospedados](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |

<sup>1</sup> Cada Serviço Azure Cloud com funções web ou trabalhadores pode ter duas implementações, uma para produção e outra para encenação. Este limite refere-se ao número de funções distintas, isto é, a configuração. Este limite não se refere ao número de casos por papel, isto é, escalonamento.

