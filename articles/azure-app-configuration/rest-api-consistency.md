---
title: Azure App Configuration REST API - consistência
description: Páginas de referência para garantir a consistência em tempo real utilizando a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932630"
---
# <a name="real-time-consistency"></a>Consistência em tempo real

Devido à natureza de alguns sistemas distribuídos, a coerência em tempo real entre pedidos é difícil de aplicar implicitamente. Uma solução é permitir o suporte protocolar sob a forma de tokens de sincronização múltipla. As fichas de sincronização são opcionais.

## <a name="initial-request"></a>Pedido inicial

Para garantir a consistência em tempo real entre diferentes instâncias e pedidos de clientes, utilize pedidos opcionais `Sync-Token` e cabeçalhos de resposta.

Sintaxe:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parâmetro|Descrição|
|--|--|
| `<id>` | ID token (opaco) |
| `<value>` | Valor simbólico (opaco). Permite a linha codificada base64. |
| `<sn>` | Número de sequência simbólica (versão). Mais alto significa uma versão mais recente do mesmo símbolo. Permite uma maior concordância e caching do cliente. O cliente pode optar por usar apenas a última versão do token, porque as versões token são inclusivas. Este parâmetro não é necessário para pedidos. |

## <a name="response"></a>Resposta

O serviço fornece um `Sync-Token` cabeçalho com cada resposta.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Pedidos subsequentes

Qualquer pedido subsequente é garantida em tempo real uma resposta consistente em relação ao fornecido `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Se omitir o `Sync-Token` cabeçalho do pedido, é possível que o serviço responda com dados em cache durante um curto período de tempo (até alguns segundos), antes de se instalar internamente. Este comportamento pode causar leituras inconsistentes se as alterações ocorrerem imediatamente antes da leitura.

## <a name="multiple-sync-tokens"></a>Vários fichas de sincronização

O servidor pode responder com várias fichas de sincronização para um único pedido. Para manter a consistência em tempo real para o próximo pedido, o cliente deve responder com todos os tokens de sincronização recebidos. Os valores múltiplos dos cabeçalhos devem ser separados por vírgula.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
