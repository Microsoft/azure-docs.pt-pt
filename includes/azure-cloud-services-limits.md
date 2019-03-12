---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553986"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| [Funções Web ou de trabalho por implementação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Pontos finais de entrada de instância](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) por implementação |25 |25 |
| [Pontos finais de entrada](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) por implementação |25 |25 |
| [Pontos finais internos](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) por implementação |25 |25 |
| [Hospedado certificados de serviço](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implementação |199 |199 |

<sup>1</sup>cada serviço de Cloud do Azure com funções web ou de trabalho pode ter duas implementações, uma para produção e uma para teste. Este limite refere-se ao número de funções distintas, ou seja, a configuração. Este limite não referem-se ao número de instâncias por função, ou seja, dimensionamento.

