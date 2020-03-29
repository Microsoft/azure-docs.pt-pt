---
title: Suporte HTTP/2 no Azure CDN [ Microsoft Docs
description: Saiba mais sobre o suporte http/2 e CDN.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67849977"
---
# <a name="http2-support-in-azure-cdn"></a>SUPORTE HTTP/2 em Azure CDN

HTTP/2 é uma grande revisão para HTTP/1.1\. Proporciona um desempenho web mais rápido, tempo de resposta reduzido e melhor experiência do utilizador, mantendo ao mesmo tempo os métodos familiares de HTTP, códigos de estado e semântica. Embora http/2 seja projetado para trabalhar com HTTP e HTTPS, muitos navegadores web de clientes apenas suportam HTTP/2 sobre TLS.

### <a name="http2-benefits"></a>HTTP/2 Benefícios

Os benefícios de HTTP/2 incluem:

*   **Multiplexing e concurrency**

    Utilizando http 1.1, fazer múltiplos pedidos de recursos requer múltiplas ligações TCP, e cada ligação tem a sobrecarga de desempenho associada a ele. HTTP/2 permite que sejam solicitados vários recursos numa única ligação TCP.

*   **Compressão do cabeçalho**

    Ao comprimir os cabeçalhos HTTP para os recursos servidos, o tempo no fio é reduzido significativamente.

*   **Dependências de fluxo**

    As dependências de fluxo permitem ao cliente indicar ao servidor quais os recursos prioritários.


## <a name="http2-browser-support"></a>SUPORTE HTTP/2 Browser

Todos os principais navegadores implementaram suporte HTTP/2 nas suas versões atuais. Os navegadores não suportados recuam automaticamente para HTTP/1.1.

|Browser|Versão Mínima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Ópera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Habilitar o suporte http/2 no Azure CDN

Atualmente, o suporte HTTP/2 está ativo para todos os perfis De CDN Azure. Não são necessárias mais medidas por parte dos clientes.

## <a name="next-steps"></a>Passos Seguintes

Para ver os benefícios do HTTP/2 em ação, consulte [esta demonstração da Akamai.](https://http2.akamai.com/demo)

Para saber mais sobre http/2, visite os seguintes recursos:

*   [Página inicial da especificação HTTP/2](https://http2.github.io/)
*   [OFICIAL HTTP/2 FAQ](https://http2.github.io/faq/)
*   [Informação akamai HTTP/2](https://http2.akamai.com/)

Para saber mais sobre as funcionalidades disponíveis da Azure CDN, consulte a visão geral do [Azure CDN](https://azure.microsoft.com/documentation/articles/cdn-overview/).