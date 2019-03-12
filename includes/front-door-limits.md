---
title: incluir ficheiro
description: incluir ficheiro
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: e3fa5616518675d8475937ec63afdd8e1742e8c6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553842"
---
| Recurso | Limite predefinido |
| --- | --- |
| Recursos de serviço de porta de entrada do Azure por subscrição | 100 |
| Anfitriões de front-end, que inclui os domínios personalizados por recurso | 100 |
| Regras de encaminhamento por recurso | 100 |
| Conjuntos de back-end por recurso | 50 |
| Back-ends por conjunto de back-end | 100 |
| Padrões de caminho a corresponder para uma regra de encaminhamento | 25 |
| Regras de firewall de aplicação web personalizado por política | 10 |
| Política de firewall de aplicações Web por recurso | 100 |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para a porta de entrada
- Porta de entrada tem um TCP ligação tempo limite de inatividade de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Porta de entrada para o back-end da aplicação
- Se a resposta for uma resposta em partes, um 200 é retornado se ou quando é recebido o primeiro segmento.
- Depois do pedido HTTP é reencaminhado para o back-end, porta da frente aguarda 30 segundos para o primeiro pacote de back-end. Em seguida, ele retorna um erro 503 ao cliente.
- Depois do primeiro pacote recebido de back-end, porta da frente aguarda 30 segundos de um tempo limite de inatividade. Em seguida, ele retorna um erro 503 ao cliente.
- Porta de entrada para o back-end TCP tempo limite é de 30 minutos.

### <a name="upload-and-download-data-limit"></a>Carregar e transferir o limite de dados

|  | Com a codificação (CTE) da transferência | Sem a segmentação de HTTP |
| ---- | ------- | ------- |
| **Transferência** | Não existe nenhum limite no tamanho de download. | Não existe nenhum limite no tamanho de download. |
| **Carregar** |  Não existe nenhum limite, desde que cada carregamento CTE for inferior a 28.6 MB. | O tamanho não pode ser maior do que 28.6. MB. |
