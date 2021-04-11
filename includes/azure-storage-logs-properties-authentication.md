---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612111"
---
| Propriedade | Descrição |
|:--- |:---|
|**identidade / tipo** | O tipo de autenticação que foi usada para fazer o pedido. Por exemplo: `OAuth` `Kerberos` , , `SAS Key` ou `Account Key``Anonymous` |
|**identidade / tokenHash**|O hash SHA-256 do símbolo de autenticação utilizado no pedido. <br>Quando o tipo de autenticação é `Account Key` , o formato é "key1 \| key2 (hash SHA256 da tecla)". Por exemplo: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>Quando o tipo de autenticação é `SAS Key` , o formato é "key1 \| key2 (sha 256 hash da tecla),SasSignature (sha 256 hash do token SAS)". Por exemplo: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. Quando o tipo de autenticação é `OAuth` , o formato é "SHA 256 hash do símbolo OAuth". Por exemplo: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> Para outros tipos de autenticação, não existe nenhum campo tokenHash. |
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
