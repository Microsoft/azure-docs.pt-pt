---
title: Parâmetros e cabeçalhos comuns
description: Os parâmetros e cabeçalhos comuns a todas as operações que você pode fazer relacionado com os recursos do Cofre Chave.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430880"
---
# <a name="common-parameters-and-headers"></a>Parâmetros e cabeçalhos comuns

As seguintes informações são comuns a todas as operações que poderá fazer relacionadas com os recursos do Key Vault:

- Substitua-a `{api-version}` pela versão api no URI.
- Substitua `{subscription-id}` pelo seu identificador de subscrição no URI
- Substitua-o `{resource-group-name}` pelo grupo de recursos. Para mais informações, consulte a Utilização de Grupos de Recursos para gerir os seus recursos Azure.
- Substitua-o `{vault-name}` pelo nome do cofre chave no URI.
- Detete o cabeçalho do Tipo conteúdo para aplicação/json.
- Detete o cabeçalho de Autorização num Token Web JSON que obtenha do Azure Ative Directory (AAD). Para mais informações, consulte os pedidos do Gestor de [Recursos Azure autenticando.](authentication-requests-and-responses.md)

## <a name="common-error-response"></a>Resposta de erro comum
O serviço utilizará códigos de estado HTTP para indicar sucesso ou falha. Além disso, as falhas contêm uma resposta no seguinte formato:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nome do elemento | Tipo | Descrição |
|---|---|---|
| code | string | O tipo de erro que ocorreu.|
| message | string | Uma descrição do que causou o erro. |



## <a name="see-also"></a>Veja também
 [Referência aabobadal de cofre de chaves azure REST API](/rest/api/keyvault/)
