---
title: Porta da Frente Azure - arquitetura de encaminhamento Microsoft Docs
description: Este artigo ajuda-o a compreender o aspeto da visão global da arquitetura da Porta da Frente.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: fd1f06bcb92ea97e0e9e9a6eefeac957031575a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471562"
---
# <a name="routing-architecture-overview"></a>Visão geral da arquitetura de encaminhamento

A Porta Da Frente Azure quando recebe os pedidos do seu cliente, então ou os responde (se o cache estiver ativado) ou os encaminha para o backend de aplicação apropriado (como um proxy inverso).

</br>Existem oportunidades para otimizar o tráfego ao encaminhar-se para a Porta da Frente Azure, bem como quando se encaminha para backends.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Selecionando o ambiente porta da frente para o encaminhamento de tráfego (Anycast)

O encaminhamento para os ambientes da Porta Frontal Azure aproveita o [Anycast](https://en.wikipedia.org/wiki/Anycast) para o tráfego dNS (Domain Name System) e HTTP (Hypertext Transfer Protocol), pelo que o tráfego de utilizadores irá para o ambiente mais próximo em termos de topologia de rede (menos lúpulo). Esta arquitetura normalmente oferece melhores tempos de ida e volta para os utilizadores finais (maximizando os benefícios de Split TCP). A Porta da Frente organiza os seus ambientes em "anéis" primários e recuados.  O anel exterior tem ambientes mais próximos dos utilizadores, oferecendo llácências mais baixas.  O anel interno tem ambientes que podem lidar com a falha para o ambiente do anel exterior no caso de um problema acontecer. O anel exterior é o alvo preferido para todo o tráfego, mas o anel interno é necessário para lidar com o tráfego transbordante do anel exterior. Em termos de VIPs (endereços protocolos de Internet Virtual), cada anfitrião frontend, ou domínio servido pela Porta da Frente é atribuído um VIP primário, que é anunciado por ambientes tanto no anel interno como exterior, bem como um VIP de recuo, que só é anunciado por ambientes no anel interno. 

</br>Esta estratégia global garante que os pedidos dos seus utilizadores finais chegam sempre ao ambiente mais próximo da Porta da Frente e que mesmo que o ambiente preferido da Porta da Frente não seja saudável, o tráfego passa automaticamente para o ambiente mais próximo.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Ligação ao ambiente da porta da frente (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) é uma técnica para reduzir as latenciências e problemas de TCP, quebrando uma ligação que incorreria num elevado tempo de ida e volta em pedaços menores.  Ao colocar os ambientes porta da frente mais perto dos utilizadores finais e terminar as ligações TCP dentro do ambiente porta da frente, uma ligação TCP com um grande tempo de ida e volta (RTT) para aplicação backend é dividida em duas ligações TCP. A ligação curta entre o utilizador final e o ambiente porta da frente significa que a ligação é estabelecida em três viagens de ida e volta curtas em vez de três longas viagens de ida e volta, poupando latência.  A longa ligação entre o ambiente porta da frente e o backend pode ser pré-estabelecida e reutilizada em várias chamadas de utilizador final, poupando novamente o tempo de ligação do TCP.  O efeito é multiplicado ao estabelecer uma ligação SSL/TLS (Transport Layer Security), uma vez que há mais viagens de ida e volta para assegurar a ligação.

## <a name="processing-request-to-match-a-routing-rule"></a>Pedido de processamento para corresponder a uma regra de encaminhamento
Depois de estabelecer uma ligação e fazer um aperto de mão SSL, quando um pedido aterra em um ambiente porta da frente, corresponder a uma regra de encaminhamento é o primeiro passo. Este jogo é basicamente determinante a partir de todas as configurações na Porta da Frente, que regra de encaminhamento particular para corresponder ao pedido. Leia sobre como a Porta da Frente combina com a [rota](front-door-route-matching.md) para saber mais.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificação de backends disponíveis na piscina de backend para a regra de encaminhamento
Uma vez que a Porta da Frente tem uma correspondência para uma regra de encaminhamento com base no pedido de entrada e se não houver cache, então o próximo passo é retirar o estado da sonda de saúde para a piscina de backend associada à rota igual. Leia sobre como a Porta frontal monitoriza a saúde usando sondas de [saúde](front-door-health-probes.md) para saber mais.

## <a name="forwarding-the-request-to-your-application-backend"></a>Reencaminhamento do pedido para o seu backend de pedido
Finalmente, assumindo que não há configuração de cache configurada, o pedido do utilizador é encaminhado para o "melhor" backend com base na configuração do método de [encaminhamento da Porta Frontal.](front-door-routing-methods.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
