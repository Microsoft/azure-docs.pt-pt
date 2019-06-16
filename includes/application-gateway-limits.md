---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 592e1973344b231693077f8286a41dfd67a8d188
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66689105"
---
| Resource | Limite de máximo/predefinido | Nota |
| --- | --- | --- |
| Gateway de Aplicação do Azure |1000 por subscrição | |
| Configurações de IP Front-end |2 |1 pública e 1 privada |
| Portas de front-end |100<sup>1</sup> | |
| Conjuntos de endereços de back-end |100<sup>1</sup> | |
| Servidores de back-end por conjunto |1,200 | |
| Serviços de escuta HTTP |100<sup>1</sup> | |
| Regras de balanceamento de carga HTTP |100<sup>1</sup> | |
| Definições de HTTP de back-end |100<sup>1</sup> | |
| Instâncias por gateway |32 | |
| Certificados SSL |100<sup>1</sup> |1 por serviços de escuta HTTP |
| Tamanho máximo do certificado SSL |V1 SKU - 10 KB<br>V2 SKU - 25 KB| |
| Certificados de autenticação |100 | |
| Certificados de raiz fidedigna |100 | |
| Mínimo de tempo limite do pedido |1 segundo | |
| Máximo de tempo limite do pedido |24 horas | |
| Número de sites |100<sup>1</sup> |1 por serviços de escuta HTTP |
| Mapas de URL por serviço de escuta |1 | |
| Mapeiam as regras com base no caminho máximas, por URL|100||
| Configurações de redirecionamento |100<sup>1</sup>| |
| Ligações simultâneas do WebSocket |Gateways médios 20k<br> Gateways grandes 50k| |
| Comprimento máximo do URL|8,000||
| Tamanho máximo do ficheiro de carregamento, Standard |2 GB | |
| WAF de tamanho de carregamento de ficheiro máximo |Médias gateways do WAF, 100 MB<br>Grandes gateways do WAF, 500 MB| |
| Limite de tamanho de corpo do WAF, sem ficheiros|128 KB||
|Máximas regras personalizadas de WAF|100||

<sup>1</sup> em caso de SKUs de WAF ativada, recomendamos que limite o número de recursos para 40 para um desempenho ideal.
