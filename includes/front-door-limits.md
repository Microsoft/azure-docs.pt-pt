---
title: incluir ficheiro
description: incluir ficheiro
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 37ebe2f0c5cbbaca712e69ab4484379ecf0f4830
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237435"
---
| Recurso | Limite máximo/padrão |
| --- | --- |
| Recursos de serviço de porta frontal do Azure por assinatura | 100 |
| Hosts front-end, que incluem domínios personalizados por recurso | 100 |
| Regras de roteamento por recurso | 100 |
| Pools de back-ends por recurso | 50 |
| Back-ends por pool de back-end | 100 |
| Padrões de caminho para corresponder a uma regra de roteamento | 25 |
| Regras personalizadas de firewall do aplicativo Web por política | 10 |
| Política de firewall do aplicativo Web por assinatura | 100 |
| Condições de correspondência do firewall do aplicativo Web por regra personalizada | 10 |
| Intervalos de endereços IP de firewall do aplicativo Web por condição de correspondência | 600 |
| Valores de correspondência de cadeia de caracteres de firewall do aplicativo Web por condição de correspondência | 10 |
| Comprimento do valor de correspondência da cadeia de caracteres do firewall do aplicativo Web | 256 |
| Comprimento do nome do parâmetro de corpo da POSTAgem do firewall do aplicativo Web | 256 |
| Comprimento do nome do cabeçalho HTTP do firewall do aplicativo Web | 256 |
| Comprimento do nome do cookie do firewall do aplicativo Web | 256 |
| Tamanho do corpo da solicitação HTTP do firewall do aplicativo Web inspecionado | 128 KB |
| Comprimento do corpo da resposta personalizada do firewall do aplicativo Web | 2 KB |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para porta frontal
- A porta frontal tem um tempo limite de conexão TCP ocioso de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Porta frontal para back-end de aplicativo
- Se a resposta for uma resposta em bloco, um 200 será retornado se ou quando a primeira parte for recebida.
- Depois que a solicitação HTTP é encaminhada para o back-end, a porta da frente aguarda 30 segundos para o primeiro pacote do back-end. Em seguida, ele retorna um erro 503 para o cliente.
- Depois que o primeiro pacote é recebido do back-end, a porta da frente aguarda 30 segundos em um tempo limite de ociosidade. Em seguida, ele retorna um erro 503 para o cliente.
- A porta da frente para o tempo limite da sessão TCP de back-end é de 30 minutos.

### <a name="upload-and-download-data-limit"></a>Carregar e baixar o limite de dados

|  | Com a CTE (codificação de transferência em bloco) | Sem agrupamento HTTP |
| ---- | ------- | ------- |
| **Transferência** | Não há limite para o tamanho do download. | Não há limite para o tamanho do download. |
| **Carregar** |  Não há limite, desde que cada upload de CTE seja menor que 2 GB. | O tamanho não pode ser maior que 2 GB. |

### <a name="other-limits"></a>Outros limites
- Tamanho máximo da URL-8.192 bytes-especifica o comprimento máximo da URL bruta (esquema + nome do host + porta + caminho + cadeia de consulta da URL)
- Tamanho máximo da cadeia de caracteres de consulta-4.096 bytes-especifica o comprimento máximo da cadeia de caracteres de consulta, em bytes.
