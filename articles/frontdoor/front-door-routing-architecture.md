---
title: Serviço de porta de entrada do Azure - arquitetura de roteamento | Documentos da Microsoft
description: Este artigo ajuda-o a compreender o aspecto de vista global da arquitetura da porta de entrada.
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
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736292"
---
# <a name="routing-architecture-overview"></a>Descrição geral da arquitetura de roteamento

O serviço de porta de entrada do Azure ao receber o cliente solicitar, em seguida, qualquer uma das respostas-las de (se estiver ativada a colocação em cache) ou reencaminha-os para o back-end do aplicativo apropriado (como proxy inverso).

</br>Existem oportunidades para otimizar o tráfego quando o encaminhamento de porta da frente para Azure, bem como ao encaminhamento para o back-ends.

## <a name = "anycast"></a>Selecionar o ambiente de porta de entrada para o tráfego de encaminhamento (Anycast)

Encaminhamento para a porta da frente do Azure tira partido de ambientes [Anycast](https://en.wikipedia.org/wiki/Anycast) para o DNS (Domain Name System) e o tráfego HTTP (Hypertext Transfer Protocol), então, o tráfego de utilizador passará para o ambiente mais próximo em termos de topologia de rede (obter saltos). Esta arquitetura costuma oferece o melhor os tempos de ida e volta para os utilizadores finais (maximizar os benefícios da divisão de TCP). Porta de entrada organiza os seus ambientes em "anéis" principais e de contingência.  O anel exterior tem ambientes mais próximos dos utilizadores, oferecendo latências mais baixas.  O anel interior tem ambientes que podem processar a ativação pós-falha para o ambiente de anel exterior, no caso de um problema ocorre. O anel exterior é o alvo preferencial para todo o tráfego, mas o anel interior é necessário para processar a capacidade excedida de tráfego do anel exterior. Em termos de VIP (endereços de protocolo de Internet Virtual), cada anfitrião de front-end ou domínio servido por porta de entrada é atribuído um VIP primário, o que for anunciado por ambientes no anel interno e externo, bem como um VIP de fallback, que só é anunciado por ambientes no anel interior. 

</br>Esta estratégia geral garante que os pedidos dos seus utilizadores finais contactar sempre o ambiente de porta de entrada mais próximo e que, mesmo que o ambiente de porta de entrada preferencial é mau estado de funcionamento, em seguida, o tráfego automaticamente passa para o ambiente mais próximo seguinte.

## <a name = "splittcp"></a>Ligar ao ambiente de porta de entrada (divisão de TCP)

[Divisão TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) é uma técnica para reduzir latências e problemas TCP ao dividir uma ligação que serão cobrados um tempo de ida e volta elevado em partes mais pequenas.  Colocando os ambientes de porta de entrada mais próximo aos utilizadores finais e a terminar ligações de TCP dentro do ambiente de porta de entrada, uma conexão TCP com um grande tempo de ida e volta (RTT) ao back-end do aplicativo é dividido em duas conexões TCP. A ligação curta entre o utilizador final e o ambiente de porta de entrada significa que a ligação é estabelecida de ida e volta em vez de três viagens longas, poupando latência em três curta.  A ligação de tempo entre o ambiente de porta de entrada e o back-end pode ser estabelecida previamente e reutilizada em várias chamadas de utilizador final, novamente a guardar o tempo de ligação de TCP.  O efeito é multiplicado ao estabelecer uma ligação de SSL/TLS (Transport Layer Security) porque há mais ida e volta para proteger a ligação.

## <a name="processing-request-to-match-a-routing-rule"></a>Processar o pedido para corresponder a uma regra de encaminhamento
Depois de estabelecer uma ligação e fazer um SSL handshake, quando um pedido pousar num ambiente de porta de entrada, que correspondem uma regra de roteamento é a primeira etapa. Esta correspondência basicamente é determinar das todas as configurações na frente porta, o encaminhamento específico de regra para satisfazer o pedido para. Saiba mais sobre como a porta da frente [correspondência de rotas](front-door-route-matching.md) para saber mais.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificação de back-ends disponível no agrupamento de back-end para a regra de encaminhamento
Depois de porta de entrada tem uma correspondência para uma regra de encaminhamento com base na solicitação de entrada e se houver sem colocação em cache, em seguida, a próxima etapa é obter o estado de sonda de estado de funcionamento para o agrupamento de back-end associado a rota correspondente. Saiba mais sobre como porta de entrada monitoriza o estado de funcionamento do back-end utilizando [sondas de estado de funcionamento](front-door-health-probes.md) para saber mais.

## <a name="forwarding-the-request-to-your-application-backend"></a>Reencaminhar o pedido para o back-end de aplicação
Por fim, partindo do princípio de que existe sem colocação em cache configurado, o pedido de utilizador é reencaminhado para o "melhor" back-end com base no seu [método de encaminhamento de porta de entrada](front-door-routing-methods.md) configuração.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
