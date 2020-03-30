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
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335090"
---
| Recurso | Limite |
| --- | --- |
| Recursos da Porta Da Frente Azure por subscrição | 100 |
| Anfitriões front-end, que inclui domínios personalizados por recurso | 500 |
| Regras de encaminhamento por recurso | 500 |
| Piscinas de back-end por recurso | 50 |
| Back ends por piscina de back-end | 100 |
| Padrões de caminho para combinar com uma regra de encaminhamento | 25 |
| URLs em uma única chamada de purga cache | 100 |
| Regras personalizadas de firewall de aplicação web por política | 100 |
| Política de firewall de aplicações web por subscrição | 100 |
| Condições de correspondência de firewall de aplicação web por regra personalizada | 10 |
| Intervalos de endereçoip de firewall de aplicação web por condição de jogo | 600 |
| Valores de correspondência de firewall de firewall de aplicação web por condição de jogo | 10 |
| Comprimento de valor de correspondência de linha de firewall de aplicação web | 256 |
| Firewall de aplicação web POST comprimento de nome do parâmetro do corpo | 256 |
| Comprimento do nome do cabeçalho http da firewall da aplicação web | 256 |
| Comprimento do nome do cookie de firewall da aplicação web | 256 |
| Firewall de aplicação web HTTP solicitar tamanho do corpo inspecionado | 128 KB |
| Comprimento do corpo personalizado de resposta da firewall da aplicação web | 2 KB |

### <a name="timeout-values"></a>Valores de timeout
#### <a name="client-to-front-door"></a>Cliente para porta da frente
* A Porta da Frente tem um tempo limite de ligação TCP inativo de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Porta da Frente para aplicação back-end
* Se a resposta for uma resposta em pedaços, um 200 é devolvido se ou quando o primeiro pedaço for recebido.
* Depois de o pedido HTTP ser encaminhado para a parte de trás, a Porta da Frente aguarda 30 segundos pelo primeiro pacote a partir da parte de trás. Depois devolve um erro 503 ao cliente. Este valor é configurável através do campo sendRecvTimeoutSeconds na API.
    * Para cenários de cache, este tempo não é configurável e por isso, se um pedido for cache e demorar mais de 30 segundos para o primeiro pacote da Porta da Frente ou do backend, então um erro de 504 é devolvido ao cliente. 
* Depois de o primeiro pacote ser recebido a partir da parte de trás, a Porta da Frente espera por 30 segundos num intervalo de tempo inativo. Depois devolve um erro 503 ao cliente. Este valor de tempo não é configurável.
* Porta da frente para o intervalo de sessão tCP de trás para trás é de 90 segundos.

### <a name="upload-and-download-data-limit"></a>Limite de dados de upload e descarregamento

|  | Com codificação de transferência sabotada (TEC) | Sem http chunking |
| ---- | ------- | ------- |
| **Transferir** | Não há limite para o tamanho do download. | Não há limite para o tamanho do download. |
| **Carregar** |    Não há limite, desde que cada carregamento de CTE seja inferior a 2 GB. | O tamanho não pode ser maior que 2 GB. |

### <a name="other-limits"></a>Outros limites
* Tamanho máximo do URL - 8.192 bytes - Especifica o comprimento máximo do URL bruto (esquema + nome de anfitrião + porta + caminho + cadeia de consulta do URL)
* Tamanho máximo da corda de consulta - 4.096 bytes - Especifica o comprimento máximo da corda de consulta, em bytes.
* Tamanho máximo do cabeçalho de resposta HTTP da URL da sonda de saúde - 4.096 bytes - Especificou o comprimento máximo de todos os cabeçalhos de resposta das sondas de saúde. 
