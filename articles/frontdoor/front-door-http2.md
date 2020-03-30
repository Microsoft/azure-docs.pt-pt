---
title: Porta da Frente Azure - SUPORTE HTTP2 Microsoft Docs
description: Este artigo ajuda-o a aprender sobre o suporte http/2 na Porta da Frente Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 415a89cbccd6b43d64ba1a553d44ac003df5806a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471630"
---
# <a name="http2-support-in-azure-front-door"></a>Suporte HTTP/2 na Porta da Frente Azure

Atualmente, o suporte HTTP/2 está ativo para todas as configurações da Porta Frontal. Não são necessárias mais medidas por parte dos clientes.

HTTP/2 é uma grande revisão para HTTP/1.1. Proporciona um desempenho web mais rápido, tempo de resposta reduzido e melhor experiência do utilizador, mantendo ao mesmo tempo os métodos familiares de HTTP, códigos de estado e semântica. Embora http/2 seja projetado para trabalhar com HTTP e HTTPS, muitos navegadores web de clientes apenas suportam HTTP/2 sobre Transport Layer Security (TLS).

### <a name="http2-benefits"></a>BENEFÍCIOS HTTP/2

Os benefícios de HTTP/2 incluem:

*   **Multiplexing e concurrency**

    Utilizando http 1.1, fazer múltiplos pedidos de recursos requer múltiplas ligações TCP, e cada ligação tem a sobrecarga de desempenho associada a ele. HTTP/2 permite que sejam solicitados vários recursos numa única ligação TCP.

*   **Compressão do cabeçalho**

    Ao comprimir os cabeçalhos HTTP para os recursos servidos, o tempo no fio é reduzido significativamente.

*   **Dependências de fluxo**

    As dependências de fluxo permitem ao cliente indicar ao servidor quais os recursos prioritários.


## <a name="http2-browser-support"></a>Suporte ao navegador HTTP/2

Todos os principais navegadores implementaram suporte HTTP/2 nas suas versões atuais. Os navegadores não suportados recuam automaticamente para HTTP/1.1.

|Browser|Versão Mínima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Ópera| 32|
|Safari| 9|

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre http/2, visite os seguintes recursos:

- [Página inicial da especificação HTTP/2](https://http2.github.io/)
- [OFICIAL HTTP/2 FAQ](https://http2.github.io/faq/)
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
