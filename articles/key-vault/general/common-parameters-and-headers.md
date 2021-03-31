---
title: Parâmetros e cabeçalhos comuns
description: Os parâmetros e cabeçalhos comuns a todas as operações que possa fazer relacionadas com os recursos do Cofre-Chave.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83005822"
---
# <a name="common-parameters-and-headers"></a>Parâmetros e cabeçalhos comuns

As seguintes informações são comuns a todas as operações que possa fazer relacionadas com os recursos do Cofre-Chave:

- O `Host` cabeçalho HTTP deve estar sempre presente e deve especificar o nome de hospedeiro do cofre. Exemplo: `Host: contoso.vault.azure.net`. Note que a maioria das tecnologias do cliente povoam o `Host` cabeçalho do URI. Por exemplo, `GET https://contoso.vault.azure.net/secrets/mysecret{...}` definirá o `Host` como `contoso.vault.azure.net` . Isto significa que, se aceder ao Key Vault utilizando um endereço IP bruto `GET https://10.0.0.23/secrets/mysecret{...}` como, o valor automático do cabeçalho estará errado e terá de `Host` se assegurar manualmente de que o `Host` cabeçalho contém o nome de anfitrião do cofre.
- `{api-version}`Substitua-a pela versão api no URI.
- `{subscription-id}`Substitua-o pelo seu identificador de subscrição no URI
- `{resource-group-name}`Substitua-o pelo grupo de recursos. Para obter mais informações, consulte a Utilização de Grupos de Recursos para gerir os seus recursos Azure.
- `{vault-name}`Substitua-o pelo nome do cofre chave no URI.
- Desa esta qual é a cabeçalho do tipo de conteúdo para a aplicação/json.
- Desacorda o cabeçalho de autorização a um Token Web JSON que obtenha do Azure Ative Directory (AAD). Para mais informações, consulte os pedidos [do Autenticado Gestor de Recursos Azure.](authentication-requests-and-responses.md)

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

|Nome do elemento | Tipo | Description |
|---|---|---|
| code | string | O tipo de erro que ocorreu.|
| message | string | Uma descrição do que causou o erro. |



## <a name="see-also"></a>Consulte também
 [Referência Azure Key Vault REST API](/rest/api/keyvault/)
