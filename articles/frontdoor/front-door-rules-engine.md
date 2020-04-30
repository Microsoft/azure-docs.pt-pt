---
title: Porta da frente azure [ Microsoft Docs
description: Este artigo apresenta uma descrição geral do Azure Front Door. Descubra se é a escolha certa para equilibrar o tráfego de utilizadores para a sua aplicação.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515553"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>O que é o Motor de Regras para a Porta Da Frente Azure? 

Regras O Motor permite-lhe personalizar a forma como os pedidos http são tratados na borda e fornece mais controlo sobre o comportamento da sua aplicação web. O motor de regras para a porta frontal azure compreende várias funcionalidades-chave, incluindo:

- Encaminhamento baseado em cabeçalhos – pedidos de rota com base nos padrões no conteúdo de cabeçalhos de pedido, cookies e cordas de consulta.
- Encaminhamento baseado em parâmetros – aproveite uma série de condições de jogo, incluindo pós-args, cordas de consulta, cookies e métodos de pedido, para encaminhar pedidos com base em parâmetros de pedido HTTP. 
- Configurações de rotas sobrepõe-se: 
    - Utilize capacidades de redirecionamento para devolver redirecionamentos 301/302/307/308 ao cliente para redirecionar para novos nomes, caminhos e protocolos de hospedagem. 
    - Utilize capacidades de encaminhamento para reescrever o caminho URL do pedido sem fazer um redirecionamento tradicional e reencaminhar o pedido para o backend apropriado na sua piscina de backend configurada. 
    - Personalize a sua configuração de cache e altere dinamicamente uma rota de encaminhamento para cache com base nas condições de jogo. 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="architecture"></a>Arquitetura 

Regras do motor lida com pedidos na borda. Depois de configurar o Motor de Regras, quando um pedido atinge o seu ponto final da porta frontal, o WAF será executado primeiro, seguido da configuração do Motor de Regras associada ao seu Frontend/Domínio. Quando uma configuração do Motor de Regras é executada, significa que a regra de encaminhamento dos pais já é compatível. Se todas as ações de cada uma das regras dentro da configuração do Motor de Regras são executadas está sujeita a todas as condições de jogo dentro dessa regra sendo satisfeitas. Se um pedido corresponder a nenhuma das condições da configuração do motor de regra, então a regra de encaminhamento predefinido é executada. 

Por exemplo, na configuração abaixo, um Motor de Regras é configurado para anexar um cabeçalho de resposta que altera a idade máxima do controlo de cache se a condição de correspondência for satisfeita. 

![ação cabeçalho resposta](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Noutro exemplo, vemos que o Rules Engine está configurado para enviar um utilizador para uma versão móvel do site se a condição de correspondência, tipo de dispositivo, for verdadeira. 

![sobreposição da configuração da rota](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Em ambos os exemplos, quando nenhuma das condições de jogo é satisfeita, a regra especificada da Rota é o que é executado. 

## <a name="terminology"></a>Terminologia 

Com o Motor de Regras AFD, pode criar uma série de configurações de Rules Engine, cada uma composta por um conjunto de regras. O seguinte descreve alguma terminologia útil que encontrará ao configurar o seu Motor de Regras. 

- *Regras Configuração do motor*: Um conjunto de regras que são aplicadas à regra da rota única. Cada configuração está limitada a 5 regras. Pode criar até 10 configurações. 
- *Regra do motor*: Regra composta por até 10 condições de correspondência e 5 ações.
- *Condição do jogo*: Existem inúmeras condições de correspondência que podem ser utilizadas para analisar os seus pedidos de entrada. Uma regra pode conter até 10 condições de correspondência. As condições de jogo são avaliadas com um operador **e** um operador. Uma lista completa das condições de jogo pode ser encontrada [aqui.](front-door-rules-engine-match-conditions.md) 
- *Ação*: As ações ditam o que acontece aos seus pedidos de entrada - ações de cabeçalho de pedido/resposta, reencaminhamento, redirecionamentos e reescritas estão todos disponíveis hoje. Uma regra pode conter até 5 ações; no entanto, uma regra só pode conter uma substituição de configuração de rota de 1 rota.  Uma lista completa de ações pode ser encontrada [aqui.](front-door-rules-engine-actions.md)


## <a name="next-steps"></a>Passos seguintes

- Aprenda a configurar a sua primeira [configuração do Motor de Regras](front-door-tutorial-rules-engine.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
