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
ms.openlocfilehash: 167e774b00bcfdb7bc1e26202bd7826f525a6ace
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890407"
---
| Recurso | Limite predefinido |
| --- | --- |
| Recursos de porta de entrada por subscrição | 100 |
| Anfitriões front-end, incluindo domínios personalizados por recurso | 100 |
| Regras de encaminhamento por recurso | 100 |
| Conjuntos de back-end por recurso | 50 |
| Back-ends por conjunto de back-end | 100 |
| Padrões de caminho a corresponder para uma regra de encaminhamento | 25 |
| Regras de firewall de aplicação web personalizado por política | 10 |
| Política de firewall de aplicações Web por recurso | 100 |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para a porta de entrada
- Porta de entrada tem um TCP ligação tempo limite de inatividade de 61 segundos.

#### <a name="front-door-to-application-backend"></a>Porta de entrada para o back-end de aplicação
- Se a resposta é uma resposta em partes, será devolvido um 200 se / quando é recebido o primeiro segmento.
- Depois da solicitação HTTP é encaminhada para o back-end, porta de entrada será Aguarde 30 segundos para o primeiro pacote de back-end, antes de retornar um erro 503 ao cliente.
- Após o primeiro pacote é recebido a partir do back-end, porta da frente aguarda 30 segundos (tempo limite de inatividade) antes de retornar um erro 503 ao cliente.
- Porta de entrada para o tempo limite da sessão TCP back-end é 30 minutos.

### <a name="upload--download-data-limit"></a>Carregar / transferir o limite de dados

|  | Com a codificação (CTE) da transferência | Sem a segmentação de HTTP |
| ---- | ------- | ------- |
| **Transferência** | Não existe nenhum limite no tamanho de download | Não existe nenhum limite no tamanho de download |
| **Carregar** |  Sem limite, desde que cada CTE carregar é inferior a 28.6 MB | O tamanho não pode ser superior a 28.6 MB. |
