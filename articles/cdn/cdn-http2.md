---
title: Suporte HTTP/2 em Azure CDN Microsoft Docs
description: A Azure Content Delivery Network suporta HTTP/2, que tem benefícios em HTTP/1, tais como multiplexing & concurrency, compressão do cabeçalho e dependências de fluxo.
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
ms.openlocfilehash: d4ed04185da251d9042f6b54a192d0e49ef20ede
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778549"
---
# <a name="http2-support-in-azure-cdn"></a>Suporte HTTP/2 em Azure CDN

HTTP/2 é uma grande revisão para HTTP/1.1\. Proporciona um desempenho web mais rápido, tempo de resposta reduzido e melhor experiência do utilizador, mantendo os métodos HTTP familiares, códigos de estado e semântica. Embora HTTP/2 seja projetado para trabalhar com HTTP e HTTPS, muitos navegadores de clientes apenas suportam HTTP/2 sobre TLS.

### <a name="http2-benefits"></a>HTTP/2 Benefícios

Os benefícios de HTTP/2 incluem:

*   **Multiplexing e concurrency**

    A utilização do HTTP 1.1, fazer múltiplos pedidos de recursos requer várias ligações TCP, e cada ligação tem uma sobrecarga de desempenho associada a ela. HTTP/2 permite que vários recursos sejam solicitados numa única ligação TCP.

*   **Compressão do cabeçalho**

    Ao comprimir os cabeçalhos HTTP para recursos servidos, o tempo no fio é reduzido significativamente.

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

## <a name="enabling-http2-support-in-azure-cdn"></a>Habilitação http/2 suporte em Azure CDN

Atualmente, o suporte HTTP/2 está ativo para todos os perfis Azure CDN. Não são necessárias mais medidas por parte dos clientes.

## <a name="next-steps"></a>Passos Seguintes

Para ver os benefícios de HTTP/2 em ação, consulte [esta demonstração da Akamai.](https://http2.akamai.com/demo)

Para saber mais sobre HTTP/2, visite os seguintes recursos:

*   [Página inicial de especificações HTTP/2](https://http2.github.io/)
*   [OFICIAL HTTP/2 FAQ](https://http2.github.io/faq/)
*   [Informações da Akamai HTTP/2](https://http2.akamai.com/)

Para saber mais sobre as funcionalidades disponíveis da Azure CDN, consulte a Visão Geral do [CDN Azure](./cdn-overview.md).