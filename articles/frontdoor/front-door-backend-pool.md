---
title: Serviço de porta de entrada do Azure - back-ends e conjuntos de back-end | Documentos da Microsoft
description: Este artigo ajuda-o a compreender quais são back-end e conjuntos de back-end para configuração de porta de entrada.
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
ms.openlocfilehash: 228ed5c54a382db7b47d19adacf9e5db398c53ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123696"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Conjuntos de back-ends e back-end no serviço de porta de entrada do Azure
Este artigo explica os conceitos diferentes em relação a como pode mapear a sua implementação de aplicações com a porta de entrada. Também Explicaremos o que os termos diferentes na frente configuração de porta de entrada em torno de back-end de aplicação significa.

## <a name="backend-pool"></a>Conjunto back-end
No Front Door, os conjuntos de back-ends são back-ends equivalentes que podem receber o mesmo tipo de tráfego para a respetiva aplicação. Por outras palavras, são um agrupamento lógico das instâncias da sua aplicação em todo o mundo que podem receber o mesmo tráfego e responder com o comportamento esperado. Estes back-ends, normalmente, são implementadas em diferentes regiões ou na mesma região. Além disso, estas back-ends podem estar no modo de implementação de ativo-ativo ou caso contrário, podem ser definidas como uma configuração de ativo/passivo.

Conjunto back-end também define como os back-ends diferentes devem todos ser avaliados para a integridade dos mesmos através de sondas de estado de funcionamento e do mesmo modo como a balanceamento de carga entre os back-ends deve ocorrer.

### <a name="health-probes"></a>Sondas do estado de funcionamento
Porta de entrada envia pedidos periódicos de sonda HTTP/HTTPS para cada um dos seus back-ends configurado para determinar o estado de funcionamento de cada back-end para carregar e proximidade equilibrar os pedidos de utilizador final. Definições de sonda de estado de funcionamento para um conjunto de back-end definem como podemos consultar o estado de funcionamento para o seu back-ends de aplicação. As seguintes definições estão disponíveis para configuração de balanceamento de carga:

1. **Caminho**: Caminho de URL em que os pedidos de pesquisa serão enviados para todos os o back-ends no conjunto de back-end. Por exemplo, se um dos seus back-ends for `contoso-westus.azurewebsites.net` e o caminho é definido como `/probe/test.aspx`, em seguida, ambientes de porta de entrada, partindo do princípio de protocolo está definido como HTTP, irão enviar os pedidos de sonda de estado de funcionamento para http://contoso-westus.azurewebsites.net/probe/test.aspx. 
2. **Protocolo**: Define se os pedidos de sonda de estado de funcionamento da porta de entrada para o seu back-ends serão enviados através do protocolo HTTP ou HTTPS.
3. **Intervalo (segundos)**: Este campo define a frequência de sondas de estado de funcionamento para o seu back-ends, ou seja, os intervalos em que cada um dos ambientes de porta de entrada enviará uma sonda. Nota - se que está procurando as ativações pós-falha mais rápidas, defina este campo para um valor inferior. No entanto, quanto mais baixo for o valor mais o estado de funcionamento de sonda volume que irão receber o seu back-ends. Para obter uma idéia da sonda volume irá gerar a porta de entrada no seu back-ends, vejamos um exemplo. Vamos dizer, o intervalo está definido para 30 segundos, e existem cerca de 90 ambientes de porta de entrada ou POPs globalmente. Portanto, cada um dos seus back-ends aproximadamente receberá sobre pedidos de sonda de 3 a 5 por segundo.

Leia [sondas de estado de funcionamento](front-door-health-probes.md) para obter detalhes.

### <a name="load-balancing-settings"></a>Definições de balanceamento de carga
As definições de balanceamento de carga para o conjunto de back-end definem como podemos avaliar as sondas de estado de funcionamento para decidir o back-end ser bom ou mau estado de funcionamento e também como precisamos equilibrar o tráfego entre os back-ends diferentes no conjunto de back-end. As seguintes definições estão disponíveis para configuração de balanceamento de carga:

1. **Tamanho de exemplo**: Esta propriedade identifica o número de amostras de sondas de estado de funcionamento que precisamos considerar para avaliação de estado de funcionamento do back-end.
2. **Tamanho da amostra bem-sucedida**: Esta propriedade define que do tamanho especiais do exemplo conforme explicado acima, quantos exemplos precisamos verificar a existência de sucesso chamar o back-end como bom estado de funcionamento. 
</br>Por exemplo, digamos que para a porta de entrada tiver definido a sonda de estado de funcionamento *intervalo* a 30 segundos, *tamanho de exemplo* está definida como "5" e *tamanho da amostra bem-sucedida* está definido como "3". Em seguida, o que significa que esta configuração é que sempre que avaliamos as sondas de estado de funcionamento para o back-end, veremos os últimos cinco exemplos, o que seriam possível expansão últimos segundos 150 (= 5 * 30 s) e se não houver 3 ou mais destas sondas bem-sucedida vamos declarar o back fim do mau estado de funcionamento. Digamos que havia apenas duas pesquisas com êxito e, portanto, podemos marcará o back-end como mau estado de funcionamento. Da próxima vez que executar a avaliação se encontrarmos 3 concluída com êxito nas sondas últimos cinco, em seguida, podemos marcar o back-end como em bom estado novamente.
3. **A sensibilidade de latência (latência adicional)**: O campo de sensibilidade de latência define se pretende que a porta de entrada para enviar o pedido para o back-ends que estão dentro do intervalo de sensibilidade em termos de medição de latência ou reencaminhar o pedido para o back-end mais próximo. Leia [latência, pelo menos, com base em método de encaminhamento](front-door-routing-methods.md#latency) para a porta de entrada obter mais informações.

## <a name="backend"></a>Back-end
Um back-end é equivalente a instância de implementação de uma aplicação numa região. Porta de entrada suporta o Azure, bem como back-ends de não pertencente ao Azure e por isso, a região aqui não é limitado apenas a regiões do Azure, mas também pode ser o seu datacenter no local ou uma instância da aplicação em alguma outra cloud.

Back-ends, no contexto de portas de frente, refere-se para o nome de anfitrião ou IP público da sua aplicação, que pode servir pedidos de cliente. Bem, back-ends não devem ser confundido com o escalão de base de dados ou a camada de armazenamento etc., mas em vez disso, devem ser exibida como o ponto final público do seu back-end de aplicação.

Ao adicionar um back-end num conjunto de back-end da sua porta de entrada, terá de preencher os seguintes detalhes:

1. **Tipo de anfitrião de back-end**: O tipo de recurso que pretende adicionar. Porta de entrada suporta a deteção automática do seu back-ends de aplicação se partir do serviço de aplicações, serviço cloud ou armazenamento. Se pretender que um recurso diferente no Azure ou até mesmo um backend não pertencente ao Azure, selecione "Anfitrião personalizado". Tenha em atenção - durante a configuração, que as APIs não validam se o back-end é acessível a partir de ambientes de porta de entrada, em vez disso, que certifique-se de que o seu back-end pode ser contatado por porta de entrada. 
2. **Nome de anfitrião de subscrição e o back-end**: Se não tiver selecionado o tipo de "Anfitrião personalizado" para o anfitrião de back-end, em seguida, terá de definir o âmbito para baixo e selecione o seu back-end ao escolher a subscrição adequada e o nome de anfitrião correspondente do back-end a partir da interface de utilizador.
3. **Cabeçalho de anfitrião de back-end**: O valor de cabeçalho de anfitrião enviado para o back-end para cada solicitação. Leia [cabeçalho de anfitrião de back-end](#hostheader) para obter detalhes.
4. **Prioridade**: Pode atribuir prioridades ao seu back-ends diferentes quando pretender utilizar um back-end de serviço principal para todo o tráfego e fornecer cópias de segurança no caso de principal ou os back-ends de cópia de segurança não estão disponíveis. Leia mais sobre [prioridade](front-door-routing-methods.md#priority).
5. **Peso**: Pode atribuir pesos ao seu back-ends diferentes quando deseja distribuir o tráfego por um conjunto de back-ends, uniformemente ou, de acordo com os coeficientes de peso. Leia mais sobre [pesos](front-door-routing-methods.md#weighted).


### <a name = "hostheader"></a>Cabeçalho de anfitrião de back-end

Pedidos reencaminhados pela porta de entrada para um back-end tem um campo de cabeçalho de anfitrião que o back-end utiliza para recuperar o recurso de destino. O valor para este campo normalmente provém o URI de back-end e tem o anfitrião e a porta. Por exemplo, um pedido efetuado `www.contoso.com` terão o cabeçalho de anfitrião `www.contoso.com`. Se estiver a configurar o back-end através do portal do Azure e, em seguida, o valor predefinido, que fica preenchido para este campo é o nome de anfitrião do back-end. Por exemplo, se for do seu back-end `contoso-westus.azurewebsites.net`, no portal do Azure será o valor preenchidos automaticamente para o cabeçalho de anfitrião de back-end `contoso-westus.azurewebsites.net`. 
</br>No entanto, se estiver a utilizar modelos do Resource Manager ou outro mecanismo e não define este campo explicitamente desde início envia o nome de anfitrião de entrada como o valor de cabeçalho de anfitrião. Por exemplo, se o pedido foi feito `www.contoso.com`, e é o back-end `contoso-westus.azurewebsites.net` com o back-end anfitrião campo de cabeçalho como vazio, em seguida, desde início irá definir o cabeçalho de anfitrião como `www.contoso.com`.

A maioria de aplicação de back-ends (por exemplo, aplicações Web, armazenamento de BLOBs e serviços Cloud) requerem o cabeçalho de anfitrião coincida com o domínio de back-end. No entanto, o anfitrião de front-end que encaminha para o back-end irá ter um nome de anfitrião diferente como www\.contoso.azurefd.net. Se precisar do back-end que estiver a configurar o cabeçalho de anfitrião de acordo com o nome de anfitrião do back-end, deve garantir que o "cabeçalho de anfitrião de back-end" também tem o nome de anfitrião do back-end.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurar o cabeçalho de anfitrião de back-end para o back-end
O campo de "Cabeçalho de anfitrião de back-end" pode ser configurado para um back-end na secção de conjunto de back-end.

1. Abra o recurso de porta de entrada e clique no conjunto de back-end que tenha o back-end para ser configurado.

2. Adicione um back-end, se não tiver adicionado qualquer ou editar um já existente. O campo de "Cabeçalho de anfitrião de back-end" pode ser definido como um valor personalizado ou deixado em branco, o que significa que o nome de anfitrião para a solicitação de entrada será utilizada como o valor de cabeçalho de anfitrião.



## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).