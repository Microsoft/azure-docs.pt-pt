---
title: Métodos de encaminhamento de tráfego do serviço de porta de entrada do Azure - | Documentos da Microsoft
description: Este artigo ajuda-o a compreender os métodos de encaminhamento de tráfego diferentes utilizados por porta de entrada
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
ms.openlocfilehash: bd1278db43ba31ed78f13a826a330e16c3bc8d57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736229"
---
# <a name="front-door-routing-methods"></a>Métodos de encaminhamento de porta de entrada

Serviço de porta de entrada do Azure suporta vários métodos de encaminhamento de tráfego de mensagens em fila para determinar como encaminhar o tráfego HTTP/HTTPS para vários pontos de extremidade do serviço. Com cada um dos seus pedidos de cliente atingir a porta de entrada, o método de encaminhamento configurado é aplicado para garantir que os pedidos são reencaminhados para a instância de back-end melhor. 

Existem quatro principais conceitos para encaminhamento de tráfego disponíveis na porta de entrada:

* **[Latency](#latency):** O encaminhamento com base na latência garante que são enviados os pedidos para os back-ends de latência mais baixos aceitáveis dentro de um intervalo de sensibilidade. Basicamente, os pedidos de utilizador são enviados para o "mais próximo" conjunto de back-ends com relação à latência de rede.
* **[Prioridade](#priority):** Pode atribuir prioridades ao seu back-ends diferentes quando pretender utilizar um back-end de serviço principal para todo o tráfego e fornecer cópias de segurança no caso de principal ou os back-ends de cópia de segurança não estão disponíveis.
* **[Weighted](#weighted):** Pode atribuir pesos ao seu back-ends diferentes quando deseja distribuir o tráfego por um conjunto de back-ends, uniformemente ou, de acordo com os coeficientes de peso.
* **Afinidade de sessão:** Pode configurar a sessão de afinidade para os anfitriões de front-end ou domínios, quando deseja que os pedidos subsequentes de um utilizador sejam enviados para o mesmo back-end, desde que a sessão do utilizador ainda está ativa e a instância de back-end ainda relatórios bom estado de funcionamento com base em sondas de estado de funcionamento. 

Todas as configurações do Front Door incluem a monitorização do estado de funcionamento dos back-ends e a ativação pós-falha global, instantânea e automatizada. Para obter mais informações, consulte [monitorização de back-end de porta de entrada](front-door-health-probes.md). A porta de entrada pode ser configurado para o trabalho com base num único método de encaminhamento e dependendo do seu aplicativo precisa pode utilizar vários ou todos esses métodos de encaminhamento em conjunto para criar uma topologia de encaminhamento ideal.

## <a name = "latency"></a>Latências mais baixas com base em Encaminhamento de tráfego

Implementar back-ends em duas ou mais localizações em todo o mundo pode melhorar a capacidade de resposta de vários aplicativos ao encaminhar o tráfego para a localização ' mais próxima "aos seus utilizadores finais. O método de encaminhamento de tráfego predefinido para a sua configuração de porta de entrada reencaminha os pedidos dos seus utilizadores finais para o back-end mais próximo do ambiente de porta de entrada que recebeu o pedido. Combinado com a arquitetura do Anycast desde início do serviço do Azure, esta abordagem garante que cada um dos seus utilizadores finais get máximo de desempenho personalizado com base na respetiva localização.

O back-end "mais próximo" não é necessariamente mais próximo, medido pela distância geográfica. Em vez disso, a porta de entrada determina os back-ends mais próximos ao medir a latência de rede. Leia mais sobre [arquitetura de roteamento da porta da frente](front-door-routing-architecture.md). 

Segue-se o fluxo de decisão geral:

| Back-ends disponíveis | Prioridade | Sinal de latência (baseado em sonda de estado de funcionamento) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| Em primeiro lugar, selecione o back-ends todos os que estão ativadas e devolvidos bom estado de funcionamento (200 OK) para a sonda de estado de funcionamento. Por exemplo, existem seis back-ends A, B, C, D, E e F e entre eles C é mau estado de funcionamento e a E está desabilitado. Portanto, lista de back-ends disponível é A, B, D e F.  | Em seguida, são selecionados os back-ends de prioridade entre as disponíveis. Digamos, um back-end, B e D têm prioridade 1 e back-end F tem uma prioridade 2. Portanto, back-ends selecionado será A, B e D.| Selecione os back-ends com o intervalo de latência (menos latência & sensibilidade de latência em ms especificado). Por exemplo, se A é 15 ms, o B é 30 ms e D é 60 ms para fora do ambiente de porta de entrada onde foi parar o pedido e sensibilidade de latência é 30 ms, em seguida, é composta de agrupamento mais baixo de latência de back-end A e B, uma vez que D é para além dos 30 ms longe o mais próximo back-end que está A. | Por último, porta de entrada será round robin o tráfego entre o conjunto final selecionado de back-ends no rácio de pesos especificado. Por exemplo, se tiver de back-end um uma ponderação de 5 e o back-end B tem uma importância de 8, em seguida, o tráfego será distribuído na proporção de 5:8 entre o back-ends A e B. |

>[!NOTE]
> Por predefinição, a propriedade de sensibilidade de latência é definida como 0 ms, ou seja, sempre reencaminhar o pedido para o mais rápido disponível back-end.


## <a name = "priority"></a>Com base na prioridade encaminhamento de tráfego

Muitas vezes, uma organização deseja fornecer confiabilidade para seus serviços ao implementar um ou mais serviços de cópia de segurança no caso de seus principais do serviço ficar inativo. No setor, essa topologia é também referida como ativo/inativo ou topologia de implementação do ativo/passivo. O método de encaminhamento de tráfego de 'Priority' permite que os clientes do Azure facilmente implementar este padrão de ativação pós-falha.

O padrão de porta de entrada contém uma lista de prioridade igual de back-ends. Por padrão, porta da frente envia o tráfego apenas para os back-ends de prioridade mais alta (valor mais baixo para prioridade) ou seja, o principal conjunto de back-ends. Se o back-ends primários não estiverem disponíveis, a porta da frente encaminha o tráfego para o secundário conjunto de back-ends (valor em segundo lugar mais baixa para prioridade). Se ambos os os primários e secundários back-ends não estiverem disponíveis, o tráfego passa para o terceiro e assim por diante. Disponibilidade de back-end baseia-se o estado configurado (ativada ou desativada) e o estado de funcionamento do back-end em curso, conforme determinado pelo Estado de funcionamento sondas.

### <a name="configuring-priority-for-backends"></a>Configurar a prioridade para o back-ends

Cada um os back-ends no seu conjunto de back-end dentro da configuração de porta de entrada tem uma propriedade chamada 'Priority', que pode ser um número entre 1 e 5. Com o serviço de porta de entrada do Azure, configurar a prioridade de back-end explicitamente usando esta propriedade para cada back-end. Esta propriedade é um valor entre 1 e 5. Valores mais baixos representam uma prioridade mais alta. Back-ends podem partilhar os valores de prioridade.

## <a name = "weighted"></a>Método de encaminhamento de tráfego ponderado
O método de encaminhamento de tráfego de mensagens em fila "Ponderado" permite-lhe para distribuir o tráfego de forma uniforme ou para utilizar um peso predefinido.

O método de encaminhamento de tráfego ponderado, vai atribuir uma ponderação para cada back-end na configuração da porta da frente do seu conjunto de back-end. A ponderação é um número inteiro entre 1 e 1000. Este parâmetro utiliza um peso padrão de "50".

Entre a lista de back-ends disponíveis dentro da sensibilidade de latência aceites (conforme especificado), o tráfego é distribuído num mecanismo de round robin a proporção de pesos especificado. Se a sensibilidade de latência é definida como 0 milissegundos, em seguida, esta propriedade só têm efeito a menos que haja dois back-ends com a mesma latência de rede. 

O método ponderado permite alguns cenários úteis:

* **Atualização da aplicação gradual**: Alocar uma percentagem de tráfego para encaminhar para um novo back-end e aumentar gradualmente o tráfego ao longo do tempo para colocá-lo semelhante com outros back-ends.
* **Migração de aplicativos para o Azure**: Crie um conjunto de back-end com o back-ends do Azure e externos. Ajuste o peso dos back-ends para preferir os novo back-ends. Pode gradualmente configurar isto começando com o novo back-ends desativados, em seguida, atribuí-las os pesos mais baixos, lentamente a aumentar a níveis em que levarão a maioria do tráfego. Em seguida, por fim a desativar os back-ends menos preferenciais e removê-los a partir do agrupamento.  
* **Segurança da cloud para uma maior capacidade**: Expanda rapidamente uma implementação no local para a nuvem, colocando-o por trás da porta de entrada. Quando precisar de capacidade extra na cloud, pode adicionar ou ativar o back-ends mais e especifique a que parte do tráfego vai para cada back-end.

## <a name = "affinity"></a>Afinidade de sessão
Por padrão, sem afinidade de sessão, a porta da frente encaminha pedidos provenientes do mesmo cliente para o back-ends diferentes com base na carga balanceamento configuração particularmente como alterar as latências para back-ends diferentes ou se pedidos diferentes no mesmo utilizador francesas num ambiente de porta de entrada diferente. No entanto, outros cenários específicos ou algumas aplicações com monitorização de estado preferem que os pedidos subsequentes do mesmo utilizador sejam recebidos no mesmo back-end que processou o pedido inicial. A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo back-end. Ao utilizar cookies de porta da frente gerido, o serviço de porta de entrada do Azure pode direcionar tráfego subsequente de uma sessão de utilizador para o mesmo back-end para processamento, desde que o back-end está em bom estado e a sessão do utilizador ainda não tiver expirado. 

A afinidade de sessão pode ser ativada ao nível do anfitrião do front-end, ou seja, para cada um dos seus domínios (ou subdomínios) configurados. Depois de ativada, o Front Door adiciona um cookie à sessão do utilizador. A afinidade de sessão baseada em cookies permite ao Front Door identificar diferentes utilizadores, mesmo que estejam por trás do mesmo endereço IP, o que, por sua vez, proporciona uma distribuição do tráfego ainda mais uniforme entre os seus vários back-ends.

A duração do cookie é igual à da sessão do utilizador, pois, atualmente, o Front Door só suportada cookies de sessão. 

> [!NOTE]
> Proxies públicos podem interferir com a afinidade de sessão. Isto acontece porque o estabelecimento de uma sessão requer a porta de entrada adicionar um cookie de afinidade de sessão para a resposta, o que não pode ser efetuada se a resposta é colocáveis em cache, ele poderia interromper os cookies de outros clientes solicitar o mesmo recurso. Para proteger contra isto, afinidade de sessão serão **não** ser estabelecido se o back-end envia uma resposta em cache quando isso é tentado. Se já tiver sido estabelecida a sessão, não importa se a resposta do back-end é colocáveis em cache.
> Afinidade de sessão, estará nas seguintes circunstâncias **, a menos que** a resposta tem um código de estado 304 de HTTP:
> - A resposta tem valores específicos definidos para o ```Cache-Control``` cabeçalho que impedem a colocação em cache, como "privado" ou não-store ".
> - A resposta contém uma ```Authorization``` cabeçalho que não tenha expirado.
> - A resposta tem um código de estado HTTP 302.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
