---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 361fb18574e90fc46e45bff8914c51ee1afa2f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89411641"
---
| Recurso | Limite |
| --- | --- |
| Recursos da Porta Frontal Azure por subscrição | 100 |
| Anfitriões frontais, que inclui domínios personalizados por recurso | 500 |
| Regras de encaminhamento por recurso | 500 |
| Piscinas traseiras por recurso | 50 |
| Extremidades traseiras por piscina traseira | 100 |
| Padrões de caminho para combinar com uma regra de encaminhamento | 25 |
| URLs em uma única chamada de purga de cache | 100 |
| Regras de firewall de aplicação web personalizadas por política | 100 |
| Política de firewall de aplicação web por subscrição | 100 |
| Condições de correspondência de firewall de aplicação web por regra personalizada | 10 |
| Gamas de endereços IP de firewall de aplicação web por condição de correspondência | 600 |
| Valores de correspondência de cadeia de firewall de aplicação web por condição de correspondência | 10 |
| Comprimento do valor do valor do valor do valor do jogo de combinação de firewall de aplicação web | 256 |
| Web application firewall POST comprimento do nome do parâmetro do corpo | 256 |
| Firewall de aplicação web HTTP comprimento do nome do cabeçalho | 256 |
| Comprimento do nome do cookie de firewall de aplicação web | 256 |
| Firewall de aplicação web HTTP solicitar tamanho do corpo inspecionado | 128 KB |
| Web application firewall comprimento do corpo de resposta personalizada | 2 KB |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para Porta da Frente
* A Porta frontal tem um tempo de ligação TCP ocioso de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Porta da frente para aplicação back-end
* Se a resposta for uma resposta em pedaços, um 200 é devolvido se ou quando o primeiro pedaço é recebido.
* Depois de o pedido HTTP ser reencaminhado para a parte de trás, a Porta frontal aguarda 30 segundos para o primeiro pacote a partir da parte de trás. Depois devolve um erro de 503 ao cliente. Este valor é configurável através do campo enviarRecvTimeoutSeconds na API.
    * Para cenários de cache, este tempo limite não é configurável e assim, se um pedido é em cache e leva mais de 30 segundos para o primeiro pacote da Porta frontal ou do backend, então um erro de 504 é devolvido ao cliente. 
* Depois de o primeiro pacote ser recebido da parte de trás, a Porta frontal espera 30 segundos num intervalo de tempo inativo. Depois devolve um erro de 503 ao cliente. Este valor de tempo limite não é configurável.
* A porta da frente para o tempo de 20 ª sessão da TCP é de 90 segundos.

### <a name="upload-and-download-data-limit"></a>Carregar e transferir limite de dados

|  | Com codificação de transferências em pedaços (CTE) | Sem a HTTP a bater |
| ---- | ------- | ------- |
| **Transferência** | Não há limite para o tamanho do download. | Não há limite para o tamanho do download. |
| **Carregar** |    Não há limite enquanto cada carregamento CTE for inferior a 2 GB. | O tamanho não pode ser maior que 2 GB. |

### <a name="other-limits"></a>Outros limites
* Tamanho máximo de URL - 8.192 bytes - Especifica o comprimento máximo do URL bruto (esquema + nome de anfitrião + porta + caminho + cadeia de consulta do URL)
* Tamanho máximo da corda de consulta - 4.096 bytes - Especifica o comprimento máximo da cadeia de consulta, em bytes.
* Tamanho máximo do cabeçalho de resposta HTTP do URL da sonda de saúde - 4.096 bytes - Especificou o comprimento máximo de todos os cabeçalhos de resposta das sondas de saúde. 
