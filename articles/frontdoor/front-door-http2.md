---
title: Serviço de porta de entrada do Azure - suporte de HTTP2 | Documentos da Microsoft
description: Este artigo ajuda-o a saber mais sobre o suporte de HTTP/2 no serviço de porta de entrada do Azure
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
ms.openlocfilehash: 33e738f31be493d5890fc972ec71e7c6cd733bf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736456"
---
# <a name="http2-support-in-azure-front-door-service"></a>Suporte de HTTP/2 no serviço de porta de entrada do Azure
HTTP/2 é uma revisão principal ao HTTP/1.1. Ele fornece mais rápida experiência de utilizador melhorada, tempo de resposta reduzidos e desempenho da web, enquanto mantém o familiares métodos HTTP, códigos de estado e a semântica. Embora HTTP/2 foi concebida para funcionar com HTTP e HTTPS, muitos browsers do cliente só suportam HTTP/2 sobre Transport Layer Security (TLS).

### <a name="http2-benefits"></a>Benefícios HTTP/2

Os benefícios de HTTP/2 incluem:

*   **Multiplexação e simultaneidade**

    Utilizar HTTP 1.1, fazer várias solicitações de recursos requer várias ligações de TCP e cada conexão tem sobrecarga de desempenho associada à mesma. HTTP/2 permite que vários recursos solicitados numa única ligação de TCP.

*   **Compressão de cabeçalho**

    Comprimir para os cabeçalhos HTTP para recursos servidos, tempo na conexão é significativamente reduzido.

*   **Dependências do Stream**

    Dependências de Stream permitem que o cliente indicar ao servidor que recursos tem prioridade.


## <a name="http2-browser-support"></a>Suporte de browser HTTP/2

Todos os principais navegadores implementaram o suporte de HTTP/2 nas suas versões atuais. Não suportam browsers automaticamente fallback para HTTP/1.1.

|Browser|Versão mínima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Ativar o suporte de HTTP/2 no serviço de porta de entrada do Azure

Atualmente, o suporte de HTTP/2 está ativo para todas as configurações de porta de entrada. Nenhuma ação adicional é necessária de clientes.

## <a name="next-steps"></a>Próximos Passos

Para saber mais sobre o HTTP/2, visite os seguintes recursos:

- [Home page de especificação de HTTP/2](https://http2.github.io/)
- [HTTP/2 oficial FAQ](https://http2.github.io/faq/)
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
