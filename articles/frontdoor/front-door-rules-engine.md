---
title: Motor de regras para a arquitetura e terminologia da porta frontal azure
description: Este artigo fornece uma visão geral da função do motor das regras da porta dianteira Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/29/2020
ms.author: duau
ms.openlocfilehash: 8e478cebcf8c5c9365100ade23c3d610c24930ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91569744"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>O que é o Motor das Regras para a Porta frontal Azure? 

Regras O Motor permite-lhe personalizar a forma como os pedidos HTTP são tratados no limite e fornece um comportamento mais controlado à sua aplicação web. Regras O motor para a porta frontal Azure tem várias características-chave, incluindo:

* Aplica HTTPS para garantir que todos os seus utilizadores finais interagem com o seu conteúdo sobre uma ligação segura.
* Implementa cabeçalhos de segurança para prevenir vulnerabilidades baseadas no navegador como HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options e Access-Control-Allow-Origin para cenários de partilha de recursos de origem cruzada (CORS). Os atributos baseados em segurança também podem ser definidos com cookies.
* Pedidos de rota para versões móveis ou desktop da sua aplicação com base nos padrões de conteúdo, cookies ou cadeias de consultas de pedidos.
* Utilize capacidades de redirecionamento para devolver 301, 302, 307 e 308 redirecionamentos para o cliente para direcionar para novos nomes de anfitriões, caminhos ou protocolos.
- Modificar dinamicamente a configuração do caching da sua rota com base nos pedidos de entrada.
- Reescreva o caminho URL do pedido e encaminhe o pedido para o backend apropriado na sua piscina de backend configurada.

## <a name="architecture"></a>Arquitetura 

Regras o motor lida com pedidos no limite. Quando um pedido atinge o ponto final da porta frontal, o WAF é executado primeiro, seguido da configuração do Motor de Regras associada ao seu Frontend/Domain. Se uma configuração do Motor de Regras for executada, significa que a regra de encaminhamento dos pais já é compatível. Para que todas as ações de cada regra sejam executadas, todas as condições de jogo dentro de uma regra têm de ser satisfeitas. Se um pedido não corresponder a nenhuma das condições da configuração do seu Motor de Regra, então a Regra de Encaminhamento padrão é executada. 

Por exemplo, no diagrama seguinte, um Motor de Regras é configurado para anexar um cabeçalho de resposta. O cabeçalho muda a idade máxima do controlo da cache se a condição de jogo for satisfeita. 

![ação de cabeçalho de resposta](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Noutro exemplo, vemos que o Rules Engine está configurado para enviar um utilizador para uma versão móvel do site se a condição de correspondência, tipo de dispositivo, for verdadeira. 

![sobreposição de configuração de rota](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Em ambos os exemplos, quando nenhuma das condições de jogo são cumpridas, a Regra de Rota especificada é o que é executado. 

## <a name="terminology"></a>Terminologia 

Com o MOTOR DE Regras AFD, pode criar uma combinação de configurações do Rules Engine, cada uma composta por um conjunto de regras. O seguinte descreve alguma terminologia útil que encontrará ao configurar o seu Motor de Regras. 

- *Regras Configuração do motor*: Um conjunto de regras que são aplicadas à regra da rota única. Cada configuração é limitada a 25 regras. Pode criar até 10 configurações. 
- *Regras Do Motor Regra :* Uma regra composta por até 10 condições de jogo e 5 ações.
- *Condição de jogo*: Existem muitas condições de jogo que podem ser utilizadas para analisar os seus pedidos de entrada. Uma regra pode conter até 10 condições de jogo. As condições de correspondência são avaliadas com um operador **E.** Uma lista completa das condições de jogo pode ser [consultada aqui.](front-door-rules-engine-match-conditions.md) 
- *Ação*: As ações ditam o que acontece aos seus pedidos de entrada - ações de pedido/resposta, encaminhamento, redirecionamentos e reescritas estão disponíveis hoje. Uma regra pode conter até cinco ações; no entanto, uma regra só pode conter uma sobreposição de configuração de rota.  Uma lista completa de ações pode ser encontrada [aqui.](front-door-rules-engine-actions.md)


## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar a sua primeira [configuração do Motor de Regras](front-door-tutorial-rules-engine.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
