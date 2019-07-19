---
title: Suporte a HTTP/2 na CDN do Azure | Microsoft Docs
description: Saiba mais sobre o suporte a HTTP/2 e CDN.
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
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849977"
---
# <a name="http2-support-in-azure-cdn"></a>Suporte a HTTP/2 na CDN do Azure

HTTP/2 é uma revisão principal para HTTP/1.1 \. Ele fornece um desempenho na Web mais rápido, menor tempo de resposta e melhor experiência do usuário, mantendo os métodos HTTP, códigos de status e semânticas conhecidos. Embora o HTTP/2 seja projetado para funcionar com HTTP e HTTPS, muitos navegadores da Web do cliente dão suporte apenas a HTTP/2 por TLS.

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

## <a name="enabling-http2-support-in-azure-cdn"></a>Habilitando o suporte a HTTP/2 na CDN do Azure

Atualmente, o suporte a HTTP/2 está ativo para todos os perfis da CDN do Azure. Nenhuma ação adicional é necessária dos clientes.

## <a name="next-steps"></a>Próximos Passos

Para ver os benefícios do HTTP/2 em ação, consulte [esta demonstração do Akamai](https://http2.akamai.com/demo).

Para saber mais sobre HTTP/2, visite os seguintes recursos:

*   [Home Page de especificação HTTP/2](https://http2.github.io/)
*   [FAQ oficial do HTTP/2](https://http2.github.io/faq/)
*   [Informações do Akamai HTTP/2](https://http2.akamai.com/)

Para saber mais sobre os recursos disponíveis da CDN do Azure, confira [visão geral da CDN do Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).