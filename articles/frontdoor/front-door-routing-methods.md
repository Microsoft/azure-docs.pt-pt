---
title: Azure Front Door - métodos de encaminhamento de tráfego / Microsoft Docs
description: Este artigo ajuda-o a entender os diferentes métodos de encaminhamento de tráfego usados pela Porta da Frente
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
ms.openlocfilehash: b7dd00d28ecfe844094677e0ae19f4fd359d97d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687813"
---
# <a name="front-door-routing-methods"></a>Métodos de encaminhamento da porta da frente

A Azure Front Door suporta vários métodos de encaminhamento de tráfego para determinar como encaminhar o tráfego HTTP/HTTPS para os vários pontos finais de serviço. Com cada um dos pedidos do seu cliente a chegar à Porta da Frente, o método de encaminhamento configurado é aplicado para garantir que os pedidos são encaminhados para a melhor instância de backend. 

Existem quatro conceitos principais para o encaminhamento de tráfego disponível na Porta da Frente:

* ** [Latência:](#latency)** O encaminhamento baseado em latência garante que os pedidos são enviados para os apoios de latência mais baixos aceitáveis dentro de um intervalo de sensibilidade. Basicamente, os seus pedidos de utilizador são enviados para o conjunto "mais próximo" de backends no que diz respeito à latência da rede.
* ** [Prioridade:](#priority)** Pode atribuir prioridades aos seus diferentes backends quando pretender utilizar um backend de serviço primário para todo o tráfego e fornecer cópias de segurança caso as ressaltos primárias ou as cópias de segurança não estejam disponíveis.
* ** [Ponderado:](#weighted)** Pode atribuir pesos aos seus diferentes backends quando pretender distribuir o tráfego por um conjunto de backends, quer uniformemente quer de acordo com coeficientes de peso.
* ** [Afinidade da Sessão:](#affinity)** Pode configurar a afinidade da sessão para os anfitriões ou domínios de frontend quando pretender que os pedidos subsequentes de um utilizador sejam enviados para o mesmo backend enquanto a sessão do utilizador ainda estiver ativa e a instância de backend ainda reporte saudável com base em sondas de saúde. 

Todas as configurações do Front Door incluem a monitorização do estado de funcionamento dos back-ends e a ativação pós-falha global, instantânea e automatizada. Para obter mais informações, consulte [o Monitor de Backend front door](front-door-health-probes.md). A porta da frente pode ser configurada para funcionar com base num único método de encaminhamento e, dependendo das necessidades da sua aplicação, pode utilizar vários ou todos estes métodos de encaminhamento em combinação para construir uma topologia de encaminhamento ideal.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Encaminhamento de tráfego baseado em latências mais baixas

A implementação de backends em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta de muitas aplicações, encaminhando o tráfego para o local que está "mais próximo" dos seus utilizadores finais. O método de encaminhamento de tráfego predefinido para a configuração da porta frontal remete os pedidos dos seus utilizadores finais para o backend mais próximo do ambiente da Porta frontal que recebeu o pedido. Combinada com a arquitetura Anycast da Azure Front Door, esta abordagem garante que cada um dos seus utilizadores finais obtenha o máximo desempenho personalizado com base na sua localização.

O backend 'mais próximo' não é necessariamente o mais próximo medido pela distância geográfica. Em vez disso, a Porta Frontal determina os backends mais próximos medindo a latência da rede. Leia mais sobre [a arquitetura de encaminhamento da Porta da Frente.](front-door-routing-architecture.md) 

Segue-se o fluxo global de decisão:

| Backends disponíveis | Prioridade | Sinal de latência (baseado em sonda de saúde) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| Em primeiro lugar, selecione todos os backends que estão ativados e devolvidos saudáveis (200 OK) para a sonda de saúde. Digamos, há seis backends A, B, C, D, E e F, e entre eles C não é saudável e E é incapacitado. Então, a lista de backends disponíveis é A, B, D e F.  | Em seguida, os backends prioritários entre os disponíveis são selecionados. Digamos, backend A, B e D têm prioridade 1 e o backend F tem uma prioridade de 2. Então, os backends selecionados serão A, B e D.| Selecione os backends com intervalo de latência (menos latência & sensibilidade à latência em ms especificado). Digamos, se A tem 15 ms, B é 30 ms e D está a 60 ms do ambiente da Porta frontal onde o pedido aterrou, e a sensibilidade à latência é de 30 ms, então a piscina de latência mais baixa é composta por backend A e B, porque D está além de 30 ms de distância do backend mais próximo que é A. | Por último, a Porta Frontal rodeará o tráfego entre o conjunto final selecionado de backends na relação de pesos especificados. Digamos, se o backend A tem um peso de 5 e o backend B tem um peso de 8, então o tráfego será distribuído na proporção de 5:8 entre os backends A e B. |

>[!NOTE]
> Por padrão, a propriedade de sensibilidade à latência está definida para 0 ms, ou seja, sempre encaminhar o pedido para o backend mais rápido disponível.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Encaminhamento de tráfego baseado em prioridades

Muitas vezes, uma organização quer fornecer fiabilidade para os seus serviços, implantando um ou mais serviços de backup no caso de o seu serviço primário diminuir. Em toda a indústria, esta topologia também é referida como topologia de implantação ativa/standby ou ativa/passiva. O método de encaminhamento de tráfego "Prioritário" permite aos clientes da Azure implementar facilmente este padrão de failover.

A porta frontal padrão contém uma lista de prioridades iguais de backends. Por predefinição, a Porta Frontal envia o tráfego apenas para os backends prioritários (valor mais baixo para prioridade) ou seja, o conjunto primário de backends. Se os backends primários não estiverem disponíveis, a Porta frontal encaminha o tráfego para o conjunto secundário de backends (segundo valor mais baixo para prioridade). Se os backends primário e secundário não estiverem disponíveis, o tráfego vai para o terceiro, e assim por diante. A disponibilidade do backend baseia-se no estado configurado (ativado ou incapacitado) e no estado de saúde de backend em curso, conforme determinado pelas sondas de saúde.

### <a name="configuring-priority-for-backends"></a>Configurar prioridade para backends

Cada um dos backends na sua piscina de backend dentro da configuração da Porta frontal tem uma propriedade chamada 'Prioridade', que pode ser um número entre 1 e 5. Com a Porta Frontal Azure, configura a prioridade de backend explicitamente usando esta propriedade para cada backend. Esta propriedade é um valor entre 1 e 5. Valores mais baixos representam uma prioridade maior. Os backends podem partilhar valores prioritários.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método de encaminhamento de tráfego ponderado
O método de encaminhamento de tráfego 'ponderado' permite-lhe distribuir o tráfego uniformemente ou utilizar uma ponderação pré-definida.

No método de encaminhamento de tráfego ponderado, atribui-se um peso a cada backend na configuração da porta frontal da sua piscina de backend. A ponderação é um número inteiro entre 1 e 1000. Este parâmetro utiliza um peso predefinido de '50'.

Entre a lista de backends disponíveis dentro da sensibilidade de latência aceite (conforme especificado), o tráfego é distribuído num mecanismo de robin redondo na relação de pesos especificados. Se a sensibilidade à latência estiver definida em 0 milissegundos, então esta propriedade não produz efeito a menos que existam dois backends com a mesma latência da rede. 

O método ponderado permite alguns cenários úteis:

* **Atualização gradual**da aplicação : Atribua uma percentagem do tráfego à rota para um novo backend, e aumente gradualmente o tráfego ao longo do tempo para o colocar em pé de igualdade com outros backends.
* **Migração de aplicações para Azure**: Crie uma piscina de backend com backends Azure e externos. Ajuste o peso dos backends para preferir os novos backends. Você pode gradualmente configurar isto começando com ter os novos backends desativados, em seguida, atribuindo-lhes os pesos mais baixos, aumentando-o lentamente para níveis onde eles tomam a maioria do tráfego. Em seguida, finalmente desativando os backends menos preferidos e retirando-os da piscina.  
* **Rebentamento de nuvens para capacidade adicional**: Expanda rapidamente uma implantação no local para a nuvem colocando-a atrás da Porta da Frente. Quando precisar de uma capacidade extra na nuvem, pode adicionar ou ativar mais backends e especificar qual a parte do tráfego que vai para cada backend.

## <a name="session-affinity"></a><a name = "affinity"></a>Afinidade de Sessão
Por padrão, sem afinidade de sessão, a Porta Frontal encaminha pedidos originários do mesmo cliente para diferentes backends baseados na configuração de equilíbrio de carga, particularmente como as latências para diferentes backends mudam ou se pedidos diferentes do mesmo utilizador aterram em um ambiente de porta frontal diferente. No entanto, outros cenários específicos ou algumas aplicações com monitorização de estado preferem que os pedidos subsequentes do mesmo utilizador sejam recebidos no mesmo back-end que processou o pedido inicial. A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo back-end. Ao utilizar cookies geridos front door, a Porta Frontal Azure pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo backend para processamento, desde que o backend seja saudável e a sessão do utilizador não tenha expirado. 

A afinidade de sessão pode ser ativada ao nível do anfitrião do front-end, ou seja, para cada um dos seus domínios (ou subdomínios) configurados. Depois de ativada, o Front Door adiciona um cookie à sessão do utilizador. A afinidade de sessão baseada em cookies permite ao Front Door identificar diferentes utilizadores, mesmo que estejam por trás do mesmo endereço IP, o que, por sua vez, proporciona uma distribuição do tráfego ainda mais uniforme entre os seus vários back-ends.

A duração do cookie é igual à da sessão do utilizador, pois, atualmente, o Front Door só suportada cookies de sessão. 

> [!NOTE]
> Os proxies públicos podem interferir com a afinidade da sessão. Isto porque o estabelecimento de uma sessão requer que a Porta frontal adicione um cookie de afinidade de sessão à resposta, o que não pode ser feito se a resposta for cacheable, uma vez que iria perturbar os cookies de outros clientes que solicitam o mesmo recurso. Para proteger contra isso, **a** afinidade da sessão não será estabelecida se o backend enviar uma resposta cacheable quando esta é tentada. Se a sessão já foi estabelecida, não importa se a resposta do backend é cacheable.
> A afinidade da sessão será estabelecida nas seguintes circunstâncias, **a menos que** a resposta tenha um código de estado HTTP 304:
> - A resposta tem valores específicos definidos para o ```Cache-Control``` cabeçalho que impede o caching, como "privado" ou não-loja".
> - A resposta contém um ```Authorization``` cabeçalho que não expirou.
> - A resposta tem um código de estado HTTP 302.

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
