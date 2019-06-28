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
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333388"
---
| Resource | Limite de máximo/predefinido |
| --- | --- |
| Recursos de serviço de porta de entrada do Azure por subscrição | 100 |
| Anfitriões de front-end, que inclui os domínios personalizados por recurso | 100 |
| Regras de encaminhamento por recurso | 100 |
| Conjuntos de back-end por recurso | 50 |
| Back-ends por conjunto de back-end | 100 |
| Padrões de caminho a corresponder para uma regra de encaminhamento | 25 |
| Regras de firewall de aplicação web personalizado por política | 10 |
| Política de firewall de aplicações Web por recurso | 100 |
| Condições de correspondência do Web application firewall por regra personalizada | 10 |
| Intervalos de endereços do IP do Web application firewall por corresponde à condição | 600 |
| Valores de correspondência do Web application firewall cadeia por uma condição de correspondência | 10 |
| Tamanho de valor de correspondência de cadeia de caracteres firewall aplicação Web | 256 |
| Comprimento do nome de parâmetro de corpo de mensagem do firewall de aplicações Web | 256 |
| Comprimento do nome de cabeçalho HTTP do firewall de aplicações Web | 256 |
| Comprimento do nome de cookie do Web application firewall | 256 |
| Web application firewall HTTP pedido tamanho do corpo inspecioná-lo | 128 KB |
| Comprimento de corpo de resposta personalizada de firewall de aplicação de Web | 2 KB |

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
| **Carregar** |  Não existe nenhum limite, desde que cada carregamento CTE for inferior a 2 GB. | O tamanho não pode ser maior do que 2 GB. |

### <a name="other-limits"></a>Outros limites
- Tamanho máximo do URL - 8.192 bytes - Especifica o comprimento máximo do URL não processado (esquema + nome de anfitrião + porta + caminho + cadeia do URL de consulta) - tamanho de cadeia de caracteres de consulta máxima - 4,096 bytes - Especifica o comprimento máximo da cadeia de caracteres de consulta, em bytes.