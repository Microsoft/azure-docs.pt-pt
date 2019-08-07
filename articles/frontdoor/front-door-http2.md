---
title: Serviço de porta frontal do Azure-suporte a HTTP2 | Microsoft Docs
description: Este artigo ajuda você a aprender sobre o suporte a HTTP/2 no serviço de porta frontal do Azure
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
ms.openlocfilehash: c3c1721454c0b3c96071c685a764f34d4fa540b9
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775257"
---
# <a name="http2-support-in-azure-front-door-service"></a>Suporte a HTTP/2 no serviço de porta frontal do Azure

Atualmente, o suporte a HTTP/2 está ativo para todas as configurações de porta frontal. Nenhuma ação adicional é necessária dos clientes.

HTTP/2 é uma revisão principal para HTTP/1.1. Ele fornece um desempenho na Web mais rápido, menor tempo de resposta e melhor experiência do usuário, mantendo os métodos HTTP, códigos de status e semânticas conhecidos. Embora o HTTP/2 seja projetado para funcionar com HTTP e HTTPS, muitos navegadores da Web do cliente dão suporte apenas a HTTP/2 sobre TLS (segurança da camada de transporte).

### <a name="http2-benefits"></a>Benefícios do HTTP/2

Os benefícios do HTTP/2 incluem:

*   **Multiplexação e simultaneidade**

    Usando HTTP 1,1, fazer várias solicitações de recursos requer várias conexões TCP e cada conexão tem sobrecarga de desempenho associada a ela. O HTTP/2 permite que vários recursos sejam solicitados em uma única conexão TCP.

*   **Compactação de cabeçalho**

    Ao compactar os cabeçalhos HTTP para recursos atendidos, o tempo na transmissão é reduzido significativamente.

*   **Dependências de fluxo**

    As dependências de fluxo permitem que o cliente indique ao servidor quais recursos têm prioridade.


## <a name="http2-browser-support"></a>Suporte a navegadores HTTP/2

Todos os principais navegadores implementaram o suporte a HTTP/2 em suas versões atuais. Os navegadores sem suporte são automaticamente fallback para HTTP/1.1.

|Browser|Versão mínima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Próximos Passos

Para saber mais sobre HTTP/2, visite os seguintes recursos:

- [Home Page de especificação HTTP/2](https://http2.github.io/)
- [FAQ oficial do HTTP/2](https://http2.github.io/faq/)
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
