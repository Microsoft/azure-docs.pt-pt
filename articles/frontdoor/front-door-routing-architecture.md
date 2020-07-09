---
title: Azure Front Door - arquitetura de encaminhamento / Microsoft Docs
description: Este artigo ajuda-o a entender o aspeto global da arquitetura da Porta da Frente.
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
ms.openlocfilehash: a088e52f742f96a13ba61969c2d7a6697c96b145
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879297"
---
# <a name="routing-architecture-overview"></a>Visão geral da arquitetura de encaminhamento

A Porta frontal Azure quando recebe os pedidos do seu cliente, então responde-lhes (se o caching estiver ativado) ou reencaminha-os para o backend de aplicação apropriado (como um representante inverso).

</br>Existem oportunidades para otimizar o tráfego ao encaminhar para a Porta frontal de Azure, bem como quando o encaminhamento para backends.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Selecionando o ambiente da porta da frente para o encaminhamento de tráfego (Anycast)

O encaminhamento para os ambientes da Porta Frontal Azure alavanca o tráfego [de DNS](https://en.wikipedia.org/wiki/Anycast) (Sistema de Nome de Domínio) e HTTP (Hypertext Transfer Protocol), pelo que o tráfego do utilizador irá para o ambiente mais próximo em termos de topologia de rede (menos lúpulo). Esta arquitetura normalmente oferece melhores tempos de ida e volta para os utilizadores finais (maximizando os benefícios da Split TCP). A Porta da Frente organiza os seus ambientes em "anéis" primários e recuantes.  O anel exterior tem ambientes mais próximos dos utilizadores, oferecendo latências mais baixas.  O anel interno tem ambientes que podem lidar com a falha para o ambiente do anel exterior no caso de um problema acontecer. O anel exterior é o alvo preferido para todo o tráfego, mas o anel interno é necessário para lidar com o excesso de tráfego do anel exterior. Em termos de VIPs (endereços virtual de Protocolo de Internet), cada anfitrião frontal, ou domínio servido pela Porta frontal é atribuído um VIP primário, que é anunciado por ambientes tanto no anel interno como exterior, bem como um V de retorno, que só é anunciado por ambientes no anel interno. 

</br>Esta estratégia global garante que os pedidos dos seus utilizadores finais cheguem sempre ao ambiente mais próximo da Porta Frontal e que mesmo que o ambiente preferido da Porta Frontal não seja saudável, o tráfego move-se automaticamente para o ambiente mais próximo.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Ligação ao ambiente da porta da frente (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) é uma técnica para reduzir as latências e problemas de TCP, quebrando uma ligação que incorreria num tempo de ida e volta elevado em pedaços menores.  Ao colocar os ambientes da Porta Frontal mais perto dos utilizadores finais e ao terminar as ligações TCP dentro do ambiente da porta da frente, uma ligação TCP com um grande tempo de ida e volta (RTT) para o backend da aplicação é dividida em duas ligações TCP. A ligação curta entre o utilizador final e o ambiente da porta frontal significa que a ligação é estabelecida ao longo de três curtas viagens de ida e volta em vez de três longas viagens de ida e volta, poupando latência.  A longa ligação entre o ambiente da porta frontal e o backend pode ser pré-estabelecida e reutilizada através de várias chamadas de utilizador final, poupando novamente o tempo de ligação TCP.  O efeito é multiplicado ao estabelecer uma ligação SSL/TLS (Transport Layer Security), uma vez que há mais viagens de ida e volta para garantir a ligação.

## <a name="processing-request-to-match-a-routing-rule"></a>Pedido de processamento para corresponder a uma regra de encaminhamento
Depois de estabelecer uma ligação e fazer um aperto de mão TLS, quando um pedido aterra num ambiente da Porta da Frente, corresponder a uma regra de encaminhamento é o primeiro passo. Esta partida é basicamente determinante de todas as configurações na Porta da Frente, que regra de encaminhamento particular para corresponder ao pedido. Leia sobre como a Porta frontal combina [para](front-door-route-matching.md) saber mais.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificação de backends disponíveis na piscina de backend para a regra de encaminhamento
Uma vez que a Porta Frontal tem uma correspondência para uma regra de encaminhamento com base no pedido de entrada e se não houver caching, então o passo seguinte é puxar o estado da sonda de saúde para a piscina de backend associada à rota combinada. Leia sobre como a Porta Frontal monitoriza a saúde de backend usando [sondas de saúde](front-door-health-probes.md) para saber mais.

## <a name="forwarding-the-request-to-your-application-backend"></a>Encaminhar o pedido para o backend do seu pedido
Finalmente, assumindo que não existe uma configuração de caching, o pedido do utilizador é reencaminhado para o backend "melhor" com base na configuração [do método de encaminhamento da porta frontal.](front-door-routing-methods.md)

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
