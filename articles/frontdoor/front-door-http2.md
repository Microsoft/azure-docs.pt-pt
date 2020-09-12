---
title: Azure Front Door - suporte HTTP2 / Microsoft Docs
description: Este artigo ajuda-o a aprender sobre o suporte HTTP/2 na Porta frontal do Azure
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 7cdcef80ef2433d34b32589d2539089fb9801eb1
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399434"
---
# <a name="http2-support-in-azure-front-door"></a>Suporte HTTP/2 na Porta frontal Azure

Atualmente, o suporte HTTP/2 está ativo para todas as configurações da Porta Frontal Azure. Não são necessárias mais medidas por parte dos clientes.

HTTP/2 é uma grande revisão para HTTP/1.1. Proporciona um desempenho web mais rápido, tempo de resposta reduzido e melhor experiência do utilizador, mantendo os métodos HTTP familiares, códigos de estado e semântica. Embora HTTP/2 seja projetado para trabalhar com HTTP e HTTPS, muitos navegadores de clientes apenas suportam HTTP/2 sobre a Segurança da Camada de Transporte (TLS).

> [!NOTE]
> O suporte ao protocolo HTTP/2 está disponível apenas para pedidos de clientes para porta frontal. A comunicação da Porta frontal para as extremidades traseiras na piscina traseira acontece em HTTP/1.1. 

### <a name="http2-benefits"></a>Benefícios HTTP/2

Os benefícios de HTTP/2 incluem:

*   **Multiplexing e concurrency**

    A utilização do HTTP 1.1, fazer múltiplos pedidos de recursos requer várias ligações TCP, e cada ligação tem uma sobrecarga de desempenho associada a ela. HTTP/2 permite que vários recursos sejam solicitados numa única ligação TCP.

*   **Compressão do cabeçalho**

    Ao comprimir os cabeçalhos HTTP para recursos servidos, o tempo no fio é reduzido significativamente.

*   **Dependências de fluxo**

    As dependências de fluxo permitem ao cliente indicar ao servidor quais os recursos prioritários.


## <a name="http2-browser-support"></a>SUPORTE AO NAVEGADOR HTTP/2

Todos os principais navegadores implementaram suporte HTTP/2 nas suas versões atuais. Os navegadores não suportados recuam automaticamente para HTTP/1.1.

|Browser|Versão Mínima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Ópera| 32|
|Safari| 9|

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre HTTP/2, visite os seguintes recursos:

- [Página inicial de especificações HTTP/2](https://http2.github.io/)
- [OFICIAL HTTP/2 FAQ](https://http2.github.io/faq/)
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
