---
title: Exibição de Tráfego no Gerenciador de tráfego do Azure
description: Introdução ao Gerenciador de tráfego Exibição de Tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 39d6090b14a16f505413154df2a78d42b3fb3f3c
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333706"
---
# <a name="traffic-manager-traffic-view"></a>Exibição de Tráfego do Gerenciador de tráfego

O Gerenciador de tráfego fornece roteamento no nível do DNS para que os usuários finais sejam direcionados para pontos de extremidade íntegros com base no método de roteamento especificado quando você criou o perfil. O Exibição de Tráfego fornece o Gerenciador de tráfego com uma exibição de suas bases de usuário (em um nível de granularidade do resolvedor de DNS) e seu padrão de tráfego. Quando você habilita Exibição de Tráfego, essas informações são processadas para fornecer ideias acionáveis. 

Usando Exibição de Tráfego, você pode:
- Entenda onde as bases de usuário estão localizadas (até uma granularidade de nível de resolvedor de DNS local).
- Exiba o volume de tráfego (observado como consultas de DNS manipuladas pelo Gerenciador de tráfego do Azure) provenientes dessas regiões.
- Obtenha informações sobre o que é a latência representativa que esses usuários enfrentou.
- Aprofunde-se nos padrões de tráfego específicos de cada uma dessas bases de usuários para regiões do Azure em que você tem pontos de extremidade. 

Por exemplo, você pode usar Exibição de Tráfego para entender quais regiões têm um grande número de tráfego, mas sofrem com latências mais altas. Em seguida, você pode usar essas informações para planejar sua expansão de superfície para novas regiões do Azure para que esses usuários possam ter uma experiência de latência mais baixa.

## <a name="how-traffic-view-works"></a>Como Exibição de Tráfego funciona

Exibição de Tráfego funciona fazendo com que o Gerenciador de tráfego examine as consultas recebidas nos últimos sete dias em relação a um perfil que tem esse recurso habilitado. Das informações de consultas de entrada, Exibição de Tráfego extrai o IP de origem do resolvedor de DNS que é usado como uma representação do local dos usuários. Eles são agrupados em uma granularidade de nível de resolvedor de DNS para criar regiões de base de usuário usando as informações geográficas dos endereços IP mantidos pelo Gerenciador de tráfego. O Gerenciador de tráfego examina as regiões do Azure para as quais a consulta foi roteada e constrói um mapa de fluxo de tráfego para os usuários dessas regiões.  
Na próxima etapa, o Gerenciador de tráfego correlaciona a região base de usuário ao mapeamento de região do Azure com as tabelas de latência de inteligência de rede que ele mantém para diferentes redes de usuário final entenderem a latência média de usuários dessas regiões quando Conectando-se às regiões do Azure. Todos esses cálculos são combinados em um nível de IP do resolvedor DNS por local antes de serem apresentados a você. Você pode consumir as informações de várias maneiras.

A frequência da atualização de dados da exibição de tráfego depende de várias variáveis de serviço internas. No entanto, os dados geralmente são atualizados uma vez a cada 24 horas.

>[!NOTE]
>A latência descrita em Exibição de Tráfego é uma latência representativa entre o usuário final e as regiões do Azure às quais eles se conectaram e não é a latência de pesquisa de DNS. Exibição de Tráfego faz uma estimativa de melhor esforço da latência entre o resolvedor DNS local e a região do Azure à qual a consulta foi roteada, se não houver dados suficientes disponíveis, a latência retornada será nula. 

## <a name="visual-overview"></a>Visão geral Visual

Ao navegar até a seção **exibição de tráfego** na página do Gerenciador de tráfego, você verá um mapa geográfico com uma sobreposição de exibição de tráfego insights. O mapa fornece informações sobre a base de usuários e os pontos de extremidade para seu perfil do Gerenciador de tráfego.

### <a name="user-base-information"></a>Informações básicas do usuário

Para os resolvedores DNS locais para os quais as informações de localização estão disponíveis, elas são mostradas no mapa. A cor do resolvedor de DNS denota a latência média de experiência dos usuários finais que usaram esse resolvedor de DNS para suas consultas do Gerenciador de tráfego.

Se você passar o mouse sobre um local do resolvedor DNS no mapa, ele mostrará:
- o endereço IP do resolvedor de DNS
- o volume de tráfego de consulta DNS visto pelo Gerenciador de tráfego a partir dele
- os pontos de extremidade para os quais o tráfego do resolvedor de DNS foi roteado, como uma linha entre o ponto final e o resolvedor de DNS 
- a latência média desse local para o ponto de extremidade, representada como a cor da linha que os conecta

### <a name="endpoint-information"></a>Informações do ponto de extremidade

As regiões do Azure nas quais os pontos de extremidade residem são mostradas como pontos azuis no mapa. Se o ponto de extremidade for externo e não tiver uma região do Azure mapeada para ele, ele será mostrado na parte superior do mapa. Clique em qualquer ponto de extremidade para ver os diferentes locais (com base no resolvedor de DNS usado) de onde o tráfego foi direcionado para esse ponto de extremidade. As conexões são mostradas como uma linha entre o ponto de extremidade e o local do resolvedor de DNS e são coloridas de acordo com a latência representativa entre esse par. Além disso, você pode ver o nome do ponto de extremidade, a região do Azure em que ele é executado e o volume total de solicitações que foram direcionadas a ele por esse perfil do Gerenciador de tráfego.


## <a name="tabular-listing-and-raw-data-download"></a>Downloads de listagem de tabela e dados brutos

Você pode exibir os dados de Exibição de Tráfego em um formato de tabela no portal do Azure. Há uma entrada para cada par de IP/ponto de extremidade do resolvedor de DNS que mostra o endereço IP do resolvedor de DNS, o nome e a localização geográfica da região do Azure na qual o ponto de extremidade está localizado (se disponível), o volume de solicitações associadas a esse resolvedor de DNS para esse ponto de extremidade e a latência representativa associada aos usuários finais que usam esse DNS (quando disponível). Você também pode baixar os dados de Exibição de Tráfego como um arquivo CSV que pode ser usado como parte de um fluxo de trabalho de análise de sua escolha.

## <a name="billing"></a>Faturação

Ao usar Exibição de Tráfego, você será cobrado com base no número de pontos de dados usados para criar as informações apresentadas. Atualmente, o único tipo de ponto de dados usado é as consultas recebidas em seu perfil do Gerenciador de tráfego. Para obter mais detalhes sobre os preços, visite a [página de preços do Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>FAQs

* [O que Exibição de Tráfego faz?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Como posso aproveitar o uso de Exibição de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Como o Exibição de Tráfego é diferente das métricas do Gerenciador de tráfego disponíveis por meio do Azure monitor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Exibição de Tráfego usar informações de sub-rede do cliente EDNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Quantos dias de dados Exibição de Tráfego usar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Como Exibição de Tráfego lidar com pontos de extremidade externos?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Preciso Habilitar Exibição de Tráfego para cada perfil em minha assinatura?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Como desativo Exibição de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Como funciona a cobrança Exibição de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Passos Seguintes

- Saiba [como funciona o Gerenciador de tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de tráfego
- Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-create-profile.md)

