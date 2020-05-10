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
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005822"
---
# <a name="common-parameters-and-headers"></a>Parâmetros e cabeçalhos comuns

As seguintes informações são comuns a todas as operações que poderá fazer relacionadas com os recursos do Key Vault:

- O `Host` cabeçalho HTTP deve estar sempre presente e deve especificar o nome de anfitrião do cofre. Exemplo: `Host: contoso.vault.azure.net`. Note que a maioria `Host` das tecnologias de cliente povoam o cabeçalho do URI. Por exemplo, `GET https://contoso.vault.azure.net/secrets/mysecret{...}` definirá o `Host` como `contoso.vault.azure.net`. Isto significa que se aceder ao Key `GET https://10.0.0.23/secrets/mysecret{...}`Vault usando `Host` um endereço IP cru como, o valor `Host` automático do cabeçalho estará errado e terá de garantir manualmente que o cabeçalho contém o nome de anfitrião do cofre.
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
