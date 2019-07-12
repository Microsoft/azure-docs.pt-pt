---
title: Suporte de HTTP/2 na CDN do Azure | Documentos da Microsoft
description: Saiba mais sobre o suporte de HTTP/2 e da CDN.
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
ms.author: rli
ms.openlocfilehash: c59ecceee35587f1b7d844f160ac0f61c5288e0e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593753"
---
# <a name="http2-support-in-azure-cdn"></a>Suporte de HTTP/2 na CDN do Azure

HTTP/2 é uma revisão principal ao HTTP/1.1\. Ele fornece mais rápida experiência de utilizador melhorada, tempo de resposta reduzidos e desempenho da web, enquanto mantém o familiares métodos HTTP, códigos de estado e a semântica. Embora HTTP/2 foi concebida para funcionar com HTTP e HTTPS, muitos browsers do cliente só suportam HTTP/2 por TLS.

### <a name="http2-benefits"></a>Benefícios HTTP/2

Os benefícios de HTTP/2 incluem:

*   **Multiplexação e simultaneidade**

    Utilizar HTTP 1.1, fazer várias solicitações de recursos requer várias ligações de TCP e cada conexão tem sobrecarga de desempenho associada à mesma. HTTP/2 permite que vários recursos solicitados numa única ligação de TCP.

*   **Compressão de cabeçalho**

    Comprimir para os cabeçalhos HTTP para recursos servidos, tempo na conexão é significativamente reduzido.

*   **Dependências do Stream**

    Dependências de Stream permitem que o cliente indicar ao servidor que recursos tem prioridade.


## <a name="http2-browser-support"></a>Suporte de Browser HTTP/2

Todos os principais navegadores implementaram o suporte de HTTP/2 nas suas versões atuais. Não suportam browsers automaticamente fallback para HTTP/1.1.

|Browser|Versão mínima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Ativar o suporte de HTTP/2 na CDN do Azure

Atualmente, o suporte de HTTP/2 está ativo para todos os perfis de CDN do Azure. Nenhuma ação adicional é necessária de clientes.

## <a name="next-steps"></a>Próximos Passos

Para ver os benefícios de HTTP/2 em ação, veja [esta demonstração da Akamai](https://http2.akamai.com/demo).

Para saber mais sobre o HTTP/2, visite os seguintes recursos:

*   [Home page de especificação de HTTP/2](https://http2.github.io/)
*   [HTTP/2 oficial FAQ](https://http2.github.io/faq/)
*   [Informações do Akamai HTTP/2](https://http2.akamai.com/)

Para saber mais sobre as funcionalidades disponíveis do CDN do Azure, veja a [descrição geral da CDN do Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).