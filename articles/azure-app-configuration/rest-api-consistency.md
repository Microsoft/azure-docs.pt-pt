---
title: Configuração de aplicativoS Azure REST API - Consistência
description: Páginas de referência para garantir a consistência em tempo real usando a API de Configuração de Aplicação Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424540"
---
# <a name="real-time-consistency"></a>Consistência em tempo real

Devido à natureza de alguns sistemas distribuídos, a coerência em tempo real entre pedidos é difícil de aplicar implicitamente. A solução é permitir o suporte ao protocolo sob a forma de **tokens de sincronização múltiplas.** As fichas de sincronização são opcionais.

## <a name="initial-request"></a>Pedido inicial

Para garantir a consistência em tempo real entre diferentes instâncias e pedidos de clientes, utilize `Sync-Token` cabeçalhos de pedido/resposta opcionais.

Sintaxe:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parâmetro|Descrição|
|--|--|
| `<id>` | ID token (opaco) |
| `<value>` | Valor simbólico (opaco). Permite a cadeia codificada base64 |
| `<sn>` | Número de sequência simbólica (versão). Mais alto significa versão mais recente do mesmo símbolo. Permite uma maior concordância e caching do cliente. O cliente pode optar por utilizar apenas a última versão do Token, uma vez que as versões simbólicas são inclusivas. Não é necessário para pedidos. |

## <a name="response"></a>Resposta

O serviço fornece um `Sync-Token` cabeçalho com cada resposta.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Pedidos subsequentes

Qualquer pedido subsequente é garantida em **tempo real** uma resposta consistente em relação ao fornecido `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Se o `Sync-Token` cabeçalho for omitido do pedido, então é possível que o serviço responda com dados em cache durante um curto período de tempo (até alguns segundos), antes de se instalar internamente. Este comportamento pode causar leituras inconsistentes se as alterações ocorrerem imediatamente antes da leitura.

## <a name="multiple-sync-tokens"></a>Vários fichas de sincronização

O servidor MAY responde com vários fichas de sincronização para um único pedido. Para manter a consistência **em tempo real** para o próximo pedido, o cliente DEVE responder com todos os tokens de sincronização recebidos. Por RFC, vários valores do cabeçalho devem ser separados por vírgula.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
