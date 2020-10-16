---
title: Azure Front Door - arquitetura de encaminhamento / Microsoft Docs
description: Este artigo ajuda-o a entender o aspeto global da arquitetura da Porta da Frente.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449188"
---
# <a name="routing-architecture-overview"></a>Visão geral da arquitetura de encaminhamento

Quando a Azure Front Door receber os pedidos do seu cliente, fará uma de duas coisas. Ou as responde se ativar o caching ou reencaminha-los para o backend de aplicação apropriado como um representante inverso.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Selecionando o ambiente da porta da frente para o encaminhamento de tráfego (Anycast)

O tráfego encaminhado para os ambientes da Porta Frontal Azure utiliza o tráfego [Anycast](https://en.wikipedia.org/wiki/Anycast) para o DNS (Sistema de Nome de Domínio) e HTTP (Hypertext Transfer Protocol), que permite que os pedidos do utilizador cheguem ao ambiente mais próximo no menor número de lúpulo de rede. Esta arquitetura oferece melhores tempos de ida e volta para os utilizadores finais, maximizando os benefícios da Split TCP. A Porta da Frente organiza os seus ambientes em "anéis" primários e recuantes. O anel exterior tem ambientes mais próximos dos utilizadores, oferecendo latências mais baixas.  O anel interno tem ambientes que podem lidar com a falha para o ambiente do anel exterior, caso aconteçam problemas. O anel exterior é o alvo preferido para todo o tráfego e o anel interno é lidar com o excesso de tráfego do anel exterior. Cada anfitrião frontal ou domínio servido pela Porta frontal é atribuído a um VIP primário (endereços de Protocolo de Internet Virtual), que é anunciado por ambientes tanto no anel interno como no exterior. Um VIP de recuo só é anunciado por ambientes no anel interno. 

Esta arquitetura garante que os pedidos dos seus utilizadores finais cheguem sempre ao ambiente mais próximo da Porta Frontal. Mesmo que o ambiente preferido da Porta Frontal não seja saudável, todo o tráfego se move automaticamente para o ambiente mais próximo.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Ligação ao ambiente da porta da frente (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) é uma técnica para reduzir as latências e problemas de TCP, quebrando uma ligação que incorreria num tempo de ida e volta elevado em pedaços menores. Com ambientes front door mais próximos dos utilizadores finais, as ligações TCP terminam dentro do ambiente da porta da frente. Uma ligação TCP que tenha um grande tempo de ida e volta (RTT) para o backend da aplicação é dividida em duas ligações separadas. A "ligação curta" entre o utilizador final e o ambiente da porta frontal significa que a ligação é estabelecida em três curtas viagens de ida e volta em vez de três longas viagens de ida e volta, o que resulta na poupança de latência. A "longa ligação" entre o ambiente da porta frontal e o backend pode ser pré-estabelecida e depois reutilizada em outros pedidos de utilizadores finais, economizando tempo de conectividade. O efeito do Split TCP é multiplicado ao estabelecer uma ligação SSL/TLS (Transport Layer Security), uma vez que há mais viagens de ida e volta para garantir uma ligação.

## <a name="processing-request-to-match-a-routing-rule"></a>Pedido de processamento para corresponder a uma regra de encaminhamento
Depois de estabelecer uma ligação e completar um aperto de mão TLS, o primeiro passo após um pedido pousar num ambiente da Porta frontal é compará-lo com a regra de encaminhamento. A correspondência é determinada por configurações na Porta frontal a que a regra de encaminhamento específico corresponde ao pedido. Leia sobre como a Porta frontal combina [para](front-door-route-matching.md) saber mais.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificação de backends disponíveis na piscina de backend para a regra de encaminhamento
Uma vez que a Porta Frontal tenha combinado uma regra de encaminhamento para um pedido de entrada, o próximo passo é obter o estado da sonda de saúde para o pool de backend associado à regra de encaminhamento se não houver caching. Leia sobre como a Porta Frontal monitoriza a saúde de backend usando [sondas de saúde](front-door-health-probes.md) para saber mais.

## <a name="forwarding-the-request-to-your-application-backend"></a>Encaminhar o pedido para o backend do seu pedido
Finalmente, assumindo que o caching não está configurado, o pedido do utilizador é reencaminhado para o backend "melhor" com base na configuração do [seu método de encaminhamento.](front-door-routing-methods.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
