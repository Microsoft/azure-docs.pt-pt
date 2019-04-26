---
title: Parâmetros e cabeçalhos comuns
description: Os parâmetros e cabeçalhos comuns a todas as operações que pode fazer relacionadas com os recursos do Cofre de chaves.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b420999dad6f34e4868013e2d466d8bacb60b2c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306041"
---
# <a name="common-parameters-and-headers"></a>Parâmetros e cabeçalhos comuns

As informações seguintes são comuns a todas as operações que pode fazer relacionadas com os recursos do Cofre de chaves:

- Substitua `{api-version}` com a api-version no URI.
- Substitua `{subscription-id}` com o identificador de subscrição no URI de
- Substitua `{resource-group-name}` com o grupo de recursos. Para obter mais informações, consulte Utilizar grupos de recursos para gerir os recursos do Azure.
- Substitua `{vault-name}` com o nome do Cofre de chaves no URI.
- Defina o cabeçalho de tipo de conteúdo como application/json.
- Defina o cabeçalho de autorização para um JSON Web Token que obtém do Azure Active Directory (AAD). Para obter mais informações, consulte [autenticar o Azure Resource Manager](authentication-requests-and-responses.md) pedidos.

## <a name="common-error-response"></a>Resposta de erro comuns
O serviço irá utilizar códigos de estado HTTP para indicar êxito ou falha. Além disso, as falhas de contenham uma resposta no seguinte formato:

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
| Código | string | O tipo de erro que ocorreu.|
| message | string | Uma descrição sobre o que causou o erro. |



## <a name="see-also"></a>Consultar Também
 [Referência da API REST do Cofre de chaves do Azure](/rest/api/keyvault/)
