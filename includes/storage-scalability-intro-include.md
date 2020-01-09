---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477151"
---
Esta referência detalha a escalabilidade e metas de desempenho para o armazenamento do Azure. As metas de escalabilidade e desempenho listadas aqui são destinos de ponta, mas são atingíveis. Em todos os casos, a taxa de solicitação e a largura de banda obtidas pela sua conta de armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho que seu aplicativo executa.

Certifique-se de testar seu serviço para determinar se seu desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e garanta que o tráfego seja bem distribuído entre partições.

Quando seu aplicativo atinge o limite do que uma partição pode manipular para sua carga de trabalho, o armazenamento do Azure começa a retornar o código de erro 503 (servidor ocupado) ou as respostas de código de erro 500 (tempo limite de operação). Se ocorrerem erros 503, considere modificar seu aplicativo para usar uma política de retirada exponencial para novas tentativas. A retirada exponencial permite que a carga na partição diminua e reduza os picos de tráfego para essa partição.
