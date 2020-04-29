---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334627"
---
| Recurso | Limite | Nota |
| --- | --- | --- |
| Gateway de Aplicação do Azure |1.000 por subscrição | |
| Configurações IP front-end |2 |1 pública e 1 privada |
| Portas frontais |100<sup>1</sup> | |
| Piscinas de endereços de back-end |100<sup>1</sup> | |
| Servidores de back-end por piscina |1200 | |
| Ouvintes http |200<sup>1</sup> |Limitado a 100 ouvintes ativos que estão a encaminhar o tráfego. Ouvintes ativos = número total de ouvintes - ouvintes não ativos.<br>Se uma configuração predefinida dentro de uma regra de encaminhamento for definida para o tráfego de rota (por exemplo, tem um ouvinte, uma piscina de backend e definições HTTP) então isso também conta como ouvinte.|
| REGRAS de equilíbrio de carga http |100<sup>1</sup> | |
| Definições de HTTP de back-end |100<sup>1</sup> | |
| Instâncias por gateway |V1 SKU - 32<br>V2 SKU - 125 | |
| Certificados SSL |100<sup>1</sup> |1 por ouvinte HTTP |
| Tamanho máximo do certificado SSL |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| Certificados de autenticação |100 | |
| Certificados de raiz fidedignos |100 | |
| Pedir tempo mínimo |1 segundo | |
| Pedir tempo máximo |24 horas | |
| Número de sites |100<sup>1</sup> |1 por ouvinte HTTP |
| Mapas de URL por ouvinte |1 | |
| Regras máximas baseadas em caminhos por mapa de URL|100||
| Configurações de redirecionamento |100<sup>1</sup>| |
| Conexões WebSocket simultâneas |Gateways médios 20k<br> Grandes portas 50k| |
| Comprimento máximo do URL|32KB| |
| Tamanho máximo do cabeçalho para HTTP/2 |4KB| |
| Tamanho máximo do upload de ficheiro, Standard |2GB | |
| Tamanho máximo do carregamento de ficheiros WAF |Portais V1 Medium WAF, 100 MB<br>Portais v1 grandes WAF, 500 MB<br>V2 WAF, 750 MB| |
| Limite de tamanho do corpo waf, sem ficheiros|128 KB||
| Regras máximas de costume waf|100||
| Exclusões máximas de WAF|100||

<sup>1</sup> No caso de SKUs ativadas por WAF, recomendamos que limite o número de recursos a 40 para um desempenho ótimo.
