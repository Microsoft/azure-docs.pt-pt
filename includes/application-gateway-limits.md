---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 6ab6c4c2051ccd2fbb22c383b9ca0af53ceb13d3
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77054950"
---
| Recurso | Limite máximo/padrão | Nota |
| --- | --- | --- |
| Gateway de Aplicação do Azure |1\.000 por assinatura | |
| Configurações de IP de front-end |2 |1 pública e 1 privada |
| Portas de front-end |100<sup>1</sup> | |
| Pools de endereços de back-end |100<sup>1</sup> | |
| Servidores de back-end por pool |1,200 | |
| Ouvintes HTTP |100<sup>1</sup> | |
| Regras de balanceamento de carga HTTP |100<sup>1</sup> | |
| Configurações de HTTP de back-end |100<sup>1</sup> | |
| Instâncias por gateway |V1 SKU - 32<br>V2 SKU - 125 | |
| Certificados SSL |100<sup>1</sup> |1 por ouvinte HTTP |
| Tamanho máximo do certificado SSL |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| Certificados de autenticação |100 | |
| Certificados de raiz de confiança |100 | |
| Tempo limite mínimo da solicitação |1 segundo | |
| Tempo limite máximo da solicitação |24 horas | |
| Número de sites |100<sup>1</sup> |1 por ouvinte HTTP |
| Mapas de URL por ouvinte |1 | |
| Máximo de regras baseadas em caminho por mapa de URL|100||
| Redirecionar configurações |100<sup>1</sup>| |
| Conexões simultâneas de WebSocket |Gateways médios 20 mil<br> 50 mil de gateways grandes| |
| Comprimento máximo da URL|32 KB| |
| Tamanho máximo do cabeçalho para HTTP/2 |4| |
| Tamanho máximo de carregamento de arquivo, padrão |2 GB | |
| Tamanho máximo de upload de arquivo WAF |gateways de WAF médio v1, 100 MB<br>WAF gateways de grande escala v1, 500 MB<br>v2 WAF, 750 MB| |
| Limite de tamanho de corpo de WAF, sem arquivos|128 KB||
| WAF máximo de regras personalizadas|100||
| Máximo de exclusões de WAF|100||

<sup>1</sup> No caso de SKUs ativadas por WAF, recomendamos que limite o número de recursos a 40 para um desempenho ótimo.
