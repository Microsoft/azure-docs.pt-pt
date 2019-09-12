---
title: Parâmetros e cabeçalhos comuns
description: Os parâmetros e os cabeçalhos comuns a todas as operações que você pode fazer com relação a Key Vault recursos.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ec8e1df71e6513b13e9c37174a3363471be01d9
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879282"
---
# <a name="common-parameters-and-headers"></a>Parâmetros e cabeçalhos comuns

As informações a seguir são comuns a todas as operações que você pode fazer com relação a Key Vault recursos:

- Substituir `{api-version}` pela versão de API no URI.
- Substituir `{subscription-id}` pelo seu identificador de assinatura no URI
- Substituir `{resource-group-name}` pelo grupo de recursos. Para obter mais informações, consulte usando grupos de recursos para gerenciar seus recursos do Azure.
- Substitua `{vault-name}` pelo nome do cofre de chaves no URI.
- Defina o cabeçalho Content-Type como Application/JSON.
- Defina o cabeçalho de autorização para um token Web JSON obtido do Azure Active Directory (AAD). Para obter mais informações, consulte [Autenticando](authentication-requests-and-responses.md) solicitações de Azure Resource Manager.

## <a name="common-error-response"></a>Resposta de erro comum
O serviço usará códigos de status HTTP para indicar êxito ou falha. Além disso, as falhas contêm uma resposta no seguinte formato:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nome do elemento | Type | Descrição |
|---|---|---|
| code | Cadeia de caracteres | O tipo de erro que ocorreu.|
| message | Cadeia de caracteres | Uma descrição do que causou o erro. |



## <a name="see-also"></a>Consultar Também
 [Referência da API REST do Azure Key Vault](/rest/api/keyvault/)
