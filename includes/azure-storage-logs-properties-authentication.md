---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 61576de4a57d55ea9d1ea209c52df556f0069617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750421"
---
| Propriedade | Descrição |
|:--- |:---|
|**identidade / tipo** | O tipo de autenticação que foi usada para fazer o pedido. Por exemplo: `OAuth` `Kerberos` , , `SAS Key` ou `Account Key``Anonymous` |
|**identidade / tokenHash**|Este campo está reservado apenas para uso interno. |
|**autorização /ação** | A ação que é atribuída ao pedido. |
|**autorização / funAssignmentId** | A identificação da atribuição de papéis. Por exemplo: `4e2521b7-13be-4363-aeda-111111111111`.|
|**autorização / funçãoDefinitionId** | A definição de papel ID. Por exemplo: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principais / id** | A identificação do diretor de segurança. Por exemplo: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principais / tipo** | O tipo de chefe de segurança. Por exemplo: `ServicePrincipal`. |
|**solicitador / appID** | O ID de pedido de autorização aberta (OAuth) que é usado como solicitador. <br> Por exemplo: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**solicitador /público** | O público do pedido. Por exemplo: `https://storage.azure.com`. |
|**solicitador /objectId** | A identificação do objeto OAuth do solicitador. Em caso de autenticação kerberos, representa o identificador de objetos do utilizador autenticado Kerberos. Por exemplo: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**solicitador /inquilinoId** | A identidade de identidade do inquilino da OAuth. Por exemplo: `72f988bf-86f1-41af-91ab-222222222222`.|
|**solicitador / tokenIssuer** | O emitente simbólico da OAuth. Por exemplo: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**solicitador /upn** | O Nome Principal do Utilizador (UPN) do solicitador. Por exemplo: `someone@contoso.com`. |
|**solicitador / nome de utilizador** | Este campo está reservado apenas para uso interno.|
