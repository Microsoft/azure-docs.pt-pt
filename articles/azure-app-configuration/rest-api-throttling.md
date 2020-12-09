---
title: Azure App Configuration REST API - Estrangulamento
description: Páginas de referência para compreender o estrangulamento ao utilizar a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b4472fdcacfe5b62c0cded089224c6d41e32f47d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932443"
---
# <a name="throttling"></a>Limitação

As lojas de configuração têm limites para os pedidos que podem servir. Quaisquer pedidos que excedam uma quota atribuída a uma loja de configuração receberão uma resposta HTTP 429 (Demasiados Pedidos).

O estrangulamento é dividido em diferentes políticas de quotas:

- **Total de Pedidos** - número total de pedidos
- **Largura total da largura de banda** - dados de saída em bytes
- **Armazenamento** - tamanho total de armazenamento dos dados dos utilizadores em bytes

## <a name="handling-throttled-responses"></a>Manipulação de respostas aceleradas

Quando o limite de taxa para uma determinada quota tiver sido atingido, o servidor responderá a pedidos adicionais desse tipo com um código de estado _429._ A resposta _429_ conterá um cabeçalho _de retry-after-ms,_ proporcionando ao cliente um tempo de espera sugerido (em milissegundos) para permitir que a quota de pedido se reabasteça.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

No exemplo acima, o cliente excedeu a sua quota permitida e é aconselhado a abrandar e esperar 10 milissegundos antes de tentar quaisquer outros pedidos. Os clientes também devem considerar o retrocesso progressivo.

## <a name="other-retry"></a>Outras redações

O serviço pode identificar situações que não sejam estrangulamentos que necessitem de um revessível do cliente (ex: 503 Serviço Indisponível). Em todos estes casos, o `retry-after-ms` cabeçalho de resposta será fornecido. Para aumentar a robustez, o cliente é aconselhado a seguir o intervalo sugerido e a realizar uma nova corrida.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
