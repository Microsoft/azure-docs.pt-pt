---
title: Obtenha fichas de acesso ao serviço
description: Descreve como criar fichas para aceder às APIs DO REST ARR
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 001a77734a8cedf08c5523380c1cbd00dce89f40
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681197"
---
# <a name="get-service-access-tokens"></a>Obtenha fichas de acesso ao serviço

O acesso às APIs ARR REST só é concedido para utilizadores autorizados. Para provar a sua autorização, deve enviar um sinal de *acesso* juntamente com pedidos REST. Estes tokens são emitidos pelo *Secure Token Service* (STS) em troca de uma chave de conta. Os tokens têm uma **vida útil de 24 horas** e assim podem ser emitidos aos utilizadores sem lhes dar acesso total ao serviço.

Este artigo descreve como criar tal ficha de acesso.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta ARR,](create-an-account.md)se ainda não tiver uma.

## <a name="token-service-rest-api"></a>Serviço simbólico REST API

Para criar fichas de acesso, o *Serviço De Token Seguro* fornece uma única API REST. O URL para o serviço [https://sts.mixedreality.azure.com](https://sts.mixedreality.azure.com)ARR STS é .

### <a name="get-token-request"></a>Pedido de 'Get Token'

| URI | Método |
|-----------|:-----------|
| /contas/**contaId**/token | GET |

| Cabeçalho | Valor |
|--------|:------|
| Autorização | "Conta do **portadorId**:**accountKey"** |

Substitua o *accountId* e *o accountKey* pelos respetivos dados.

### <a name="get-token-response"></a>Resposta 'Get token'

| Código de estado | Carga útil JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | AccessToken: corda | Êxito |

| Cabeçalho | Objetivo |
|--------|:------|
| MS-CV | Este valor pode ser usado para rastrear a chamada dentro do serviço |

## <a name="getting-a-token-using-powershell"></a>Obter um símbolo usando powerShell

O código PowerShell abaixo demonstra como enviar o pedido de REST necessário para o STS. Em seguida, imprime o símbolo para o pedido PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

O script apenas imprime o símbolo à saída, de onde pode copiar & cola. Para um projeto real, deve automatizar este processo.

## <a name="next-steps"></a>Passos seguintes

* [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)
* [Azure Frontend APIs](../how-tos/frontend-apis.md)
* [API DE GESTÃO DE Sessão](../how-tos/session-rest-api.md)
