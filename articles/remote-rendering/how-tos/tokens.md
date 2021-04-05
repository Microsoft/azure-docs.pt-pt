---
title: Obter tokens de acesso ao serviço
description: Descreve como criar fichas para aceder às APIs de REPOUSO ARR
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97830705"
---
# <a name="get-service-access-tokens"></a>Obter tokens de acesso ao serviço

O acesso às APIs de REPOUSO ARR só é concedido aos utilizadores autorizados. Para provar a sua autorização, tem de enviar um *token de acesso* juntamente com os pedidos de REST. Estes tokens são emitidos pelo *Serviço DeKen Seguro* (STS) em troca de uma chave de conta. Os tokens têm uma **vida útil de 24 horas** e assim podem ser emitidos aos utilizadores sem lhes dar acesso total ao serviço.

Este artigo descreve como criar tal símbolo de acesso.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta ARR,](create-an-account.md)se ainda não tiver uma.

## <a name="token-service-rest-api"></a>Serviço Token REST API

Para criar fichas de acesso, o *Serviço Secure Token* fornece uma única API REST. O URL para o serviço STS depende do domínio da conta da conta de renderização remota. Está na forma https://sts .. domínio da conta], por exemplo. `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>Pedido de 'Obter Ficha'

| URI | Método |
|-----------|:-----------|
| /contas/**accountId**/token | GET |

| Cabeçalho | Valor |
|--------|:------|
| Autorização | "Conta **portadorId**:**accountKey"** |

Substitua *a contaId* e *contaKey* pelos seus respetivos dados.

### <a name="get-token-response"></a>Resposta 'Get token'

| Código de estado | Carga JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | AccessToken: string | Com êxito |

| Cabeçalho | Objetivo |
|--------|:------|
| MS-CV | Este valor pode ser usado para rastrear a chamada dentro do serviço |

## <a name="getting-a-token-using-powershell"></a>Obter um símbolo usando PowerShell

O código PowerShell abaixo demonstra como enviar o pedido de REPOUSO necessário para o STS. Em seguida, imprime o símbolo à solicitação powerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

O script apenas imprime o símbolo à saída, de onde pode copiá-lo & colá-lo. Para um projeto de verdade, você deve automatizar este processo.

## <a name="next-steps"></a>Passos seguintes

* [Scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md)
* [Azure Frontend APIs](../how-tos/frontend-apis.md)
* [Gestão de sessão REST API](../how-tos/session-rest-api.md)
