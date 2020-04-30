---
title: Porta da Frente Azure - métodos de encaminhamento de tráfego / Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687813"
---
# <a name="front-door-routing-methods"></a>Métodos de encaminhamento da porta da frente

A Porta Frontal Azure suporta vários métodos de encaminhamento de tráfego para determinar como encaminhar o seu tráfego HTTP/HTTPS para os vários pontos finais de serviço. Com cada um dos pedidos do seu cliente chegando à Porta da Frente, o método de encaminhamento configurado é aplicado para garantir que os pedidos são encaminhados para a melhor instância de backend. 

Existem quatro conceitos principais para o encaminhamento de tráfego disponível na Porta da Frente:

* ** [Latência:](#latency)** O encaminhamento baseado em latência garante que os pedidos são enviados para os limites de latência mais baixos aceitáveis dentro de um intervalo de sensibilidade. Basicamente, os pedidos dos seus utilizadores são enviados para o conjunto de backends "mais próximo" no que diz respeito à latência da rede.
* ** [Prioridade:](#priority)** Pode atribuir prioridades aos seus diferentes backends quando pretender utilizar um backend de serviço primário para todo o tráfego, e fornecer cópias de segurança caso as costas primárias ou de reserva não estejam disponíveis.
* ** [Ponderado:](#weighted)** Pode atribuir pesos aos seus diferentes backends quando pretender distribuir o tráfego através de um conjunto de backends, quer uniformemente quer de acordo com os coeficientes de peso.
* ** [Afinidade da Sessão](#affinity):** Pode configurar a afinidade da sessão para os seus anfitriões ou domínios frontendes quando pretender que os pedidos subsequentes de um utilizador sejam enviados para o mesmo backend, desde que a sessão do utilizador ainda esteja ativa e a instância de backend ainda reporte saudável com base em sondas de saúde. 

Todas as configurações do Front Door incluem a monitorização do estado de funcionamento dos back-ends e a ativação pós-falha global, instantânea e automatizada. Para mais informações, consulte a [Monitorização do Backend da Porta Frontal](front-door-health-probes.md). A porta da frente pode ser configurada para trabalhar com base num único método de encaminhamento e, dependendo das necessidades da sua aplicação, pode utilizar vários ou todos estes métodos de encaminhamento em combinação para construir uma topologia de encaminhamento ideal.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>O encaminhamento de tráfego baseado em mais baixas

A implementação de backends em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta de muitas aplicações, encaminhando o tráfego para o local que é 'mais próximo' dos seus utilizadores finais. O método de encaminhamento de tráfego padrão para a configuração da Porta Frontal reencaminha os pedidos dos utilizadores finais para o backend mais próximo do ambiente porta da frente que recebeu o pedido. Combinada com a arquitetura Anycast da Porta Frontal Azure, esta abordagem garante que cada um dos seus utilizadores finais obtenha o máximo desempenho personalizado com base na sua localização.

O backend 'mais próximo' não é necessariamente mais próximo, medido pela distância geográfica. Em vez disso, a Porta da Frente determina os backends mais próximos medindo a latência da rede. Leia mais sobre [a arquitetura de encaminhamento da Porta Da Frente.](front-door-routing-architecture.md) 

Abaixo está o fluxo global de decisão:

| Backends disponíveis | Prioridade | Sinal de latência (baseado em sonda de saúde) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| Em primeiro lugar, selecione todos os backends que estão ativados e devolvidos saudáveis (200 OK) para a sonda de saúde. Digamos, há seis backends A, B, C, D, E e F, e entre eles C não é saudável e E é incapacitado. Então, a lista de backends disponíveis é A, B, D e F.  | Em seguida, são selecionados os backends prioritários entre os disponíveis. Digamos, o backend A, B e D têm prioridade 1 e o backend F tem uma prioridade de 2. Então, os backends selecionados serão A, B e D.| Selecione as extremidades traseiras com intervalo de latência (menos latência & sensibilidade à latência em ms especificados). Digamos, se A é de 15 ms, B é 30 ms e D está a 60 ms de distância do ambiente da Porta da Frente onde o pedido aterrou, e a sensibilidade à latência é de 30 ms, então a piscina de latência mais baixa é composta por Backend A e B, porque D está além de 30 ms de distância do backend mais próximo que é A. | Por último, a Porta da Frente irá contornar o tráfego entre a piscina final selecionada de backends na relação de pesos especificados. Digamos, se o backend A tem um peso de 5 e o backend B tem um peso de 8, então o tráfego será distribuído no rácio de 5:8 entre os backends A e B. |

>[!NOTE]
> Por padrão, a propriedade de sensibilidade à latência está definida para 0 ms, ou seja, reencaminhar sempre o pedido para o backend mais rápido disponível.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Encaminhamento de tráfego baseado em prioridade

Muitas vezes, uma organização quer fornecer fiabilidade aos seus serviços, implantando um ou mais serviços de backup no caso de o seu serviço primário diminuir. Em toda a indústria, esta topologia também é referida como topologia de implantação ativa/standby ou ativa/passiva. O método de encaminhamento de tráfego 'Priority' permite aos clientes da Azure implementar facilmente este padrão de failover.

A porta da frente padrão contém uma lista de prioridades iguais de backends. Por padrão, a Porta da Frente envia tráfego apenas para os backends prioritários (valor mais baixo para prioridade) ou seja, o conjunto primário de backends. Se os fundos primários não estiverem disponíveis, a Porta frontal encaminha o tráfego para o conjunto secundário de backends (segundo valor mais baixo para prioridade). Se os fundos primários e secundários não estiverem disponíveis, o tráfego vai para o terceiro, e assim por diante. A disponibilidade do backend baseia-se no estado configurado (ativado ou incapacitado) e no estado de saúde em curso, determinado pelas sondas de saúde.

### <a name="configuring-priority-for-backends"></a>Configurar prioridade para backends

Cada um dos backends na sua piscina de backend dentro da configuração porta da frente tem uma propriedade chamada 'Priority', que pode ser um número entre 1 e 5. Com a Porta frontal Azure, configura a prioridade do backend usando explicitamente esta propriedade para cada backend. Esta propriedade tem um valor entre 1 e 5. Valores mais baixos representam uma prioridade maior. Backends podem partilhar valores prioritários.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método ponderado de encaminhamento de tráfego
O método de encaminhamento de tráfego 'ponderado' permite-lhe distribuir o tráfego uniformemente ou utilizar uma ponderação pré-definida.

No método de encaminhamento de tráfego ponderado, atribui um peso a cada extremidade traseira na configuração da Porta Frontal da sua piscina de backend. A ponderação é um número inteiro entre 1 e 1000. Este parâmetro utiliza um peso padrão de '50'.

Entre a lista de backends disponíveis dentro da sensibilidade de latência aceite (conforme especificado), o tráfego é distribuído num mecanismo de rodada-robin na relação de pesos especificados. Se a sensibilidade à latência for fixada para 0 milissegundos, então esta propriedade não faz efeito a menos que existam duas costas com a mesma latência de rede. 

O método ponderado permite alguns cenários úteis:

* **Atualização gradual da aplicação:** Alocar uma percentagem de tráfego para uma nova faceta e aumentar gradualmente o tráfego ao longo do tempo para o colocar a par com outros backends.
* **Migração de candidaturas para Azure**: Crie um pool de backend com fundos azure e externos. Ajuste o peso das costas para preferir as novas costas. Pode gradualmente configurar isto começando com a desativação das novas costas, atribuindo-lhes os pesos mais baixos, aumentando-o lentamente para níveis onde mais tráfego. Em seguida, finalmente desativando os backends menos preferidos e retirando-os da piscina.  
* **Cloud-bursting para capacidade adicional**: Expanda rapidamente uma implantação no local para a nuvem colocando-a atrás da Porta da Frente. Quando necessitar de capacidade extra na nuvem, pode adicionar ou ativar mais backends e especificar que parte do tráfego vai para cada backend.

## <a name="session-affinity"></a><a name = "affinity"></a>Afinidade de Sessão
Por predefinição, sem afinidade da sessão, a Front Door adere aos pedidos originários do mesmo cliente para diferentes backends com base na configuração de equilíbrio de carga, particularmente como as lanosas para diferentes backends mudam ou se pedidos diferentes do mesmo utilizador aterram num ambiente diferente da Porta da Frente. No entanto, outros cenários específicos ou algumas aplicações com monitorização de estado preferem que os pedidos subsequentes do mesmo utilizador sejam recebidos no mesmo back-end que processou o pedido inicial. A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo back-end. Ao utilizar cookies geridos pela Porta Frontal, o Azure Front Door pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo backend para processamento, desde que o backend seja saudável e a sessão de utilizador não tenha expirado. 

A afinidade de sessão pode ser ativada ao nível do anfitrião do front-end, ou seja, para cada um dos seus domínios (ou subdomínios) configurados. Depois de ativada, o Front Door adiciona um cookie à sessão do utilizador. A afinidade de sessão baseada em cookies permite ao Front Door identificar diferentes utilizadores, mesmo que estejam por trás do mesmo endereço IP, o que, por sua vez, proporciona uma distribuição do tráfego ainda mais uniforme entre os seus vários back-ends.

A duração do cookie é igual à da sessão do utilizador, pois, atualmente, o Front Door só suportada cookies de sessão. 

> [!NOTE]
> Os proxies públicos podem interferir com a afinidade da sessão. Isto porque o estabelecimento de uma sessão requer que a Porta da Frente adicione um cookie de afinidade de sessão à resposta, o que não pode ser feito se a resposta for cacheable, uma vez que iria perturbar os cookies de outros clientes que solicitam o mesmo recurso. Para se proteger contra isso, a afinidade da sessão **não** será estabelecida se o backend enviar uma resposta cacheable quando esta for tentada. Se a sessão já foi estabelecida, não importa se a resposta do backend é cacheable.
> A afinidade da sessão será estabelecida nas seguintes circunstâncias, **a menos que** a resposta tenha um código de estado HTTP 304:
> - A resposta tem valores ```Cache-Control``` específicos definidos para o cabeçalho que impede o cache, como "privado" ou não-loja".
> - A resposta ```Authorization``` contém um cabeçalho que não expirou.
> - A resposta tem um código de estado HTTP 302.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
