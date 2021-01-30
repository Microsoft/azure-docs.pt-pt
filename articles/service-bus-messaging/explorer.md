---
title: Use o Azure Service Bus Explorer para realizar operações de dados no Service Bus (Preview)
description: Este artigo fornece informações sobre como usar o Azure Service Bus Explorer baseado no portal para aceder aos dados do Azure Service Bus.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 06bc2ffffc6b467aa730e16599099bc95117dac9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092222"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Utilize o Service Bus Explorer para realizar operações de dados no Service Bus (Preview)

## <a name="overview"></a>Descrição Geral

A Azure Service Bus permite que as aplicações de clientes remetentes e recetores dissolvam a sua lógica de negócio com o uso da semântica familiar ponto-a-ponto (Fila) e subscreva a semântica de subscrição de tópicos.

As operações realizadas num espaço de nomes de autocarros da Azure Service são de dois tipos 
   * Operações de Gestão - Criar, Atualizar, Eliminar o Espaço de Nomes de Autocarros de Serviço, Filas, Tópicos e Subscrições.
   * Operações de Dados - Enviar e Receber Mensagens de Filas, Tópicos e Assinaturas.

O Azure Service Bus Explorer expande a funcionalidade do portal para além das operações de gestão para apoiar as operações de dados (Enviar, Receber, Espreitar) nas Filas, Tópicos e Assinaturas (e suas subentuidades de cartas mortas) - desde o próprio portal Azure.

> [!NOTE]
> Este artigo destaca a funcionalidade do Azure Service Bus Explorer que vive no portal Azure.
>
> A ferramenta de explorador de autocarros Azure Service é ***não** _ a ferramenta oss da comunidade [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a ferramenta Service Bus Explorer, terá de fazer as seguintes tarefas: 

- Provisionar um espaço de nomes para o autocarro da Azure Service.
- Crie uma fila para enviar e receber mensagem de ou um tópico com uma subscrição para testar a funcionalidade. Para aprender a criar filas, tópicos e subscrições, consulte os seguintes artigos: 
    - [Quickstart - Criar filas](service-bus-quickstart-portal.md)
    - [Quickstart - Criar Tópicos](service-bus-quickstart-topics-subscriptions-portal.md)
- Certifique-se de que é membro de uma destas funções no espaço de nomes: 
    - [Proprietário de dados de ônibus de serviço](/azure/role-based-access-control/built-in-roles#azure-service-bus-data-owner) 
    - [Contribuinte](/azure/role-based-access-control/built-in-roles#contributor) 
    - [Proprietário](/azure/role-based-access-control/built-in-roles#owner)


## <a name="using-the-service-bus-explorer"></a>Utilização do Explorador de Autocarros de Serviço

Para utilizar o explorador de autocarros Azure Service, tem de navegar para o espaço de nomes do Service Bus no qual pretende realizar operações de envio, espreitar e receber operações.

Se procura realizar operações contra uma fila, escolha _ *'Filas'** do menu de navegação. Se procura realizar operações contra um tópico (e são subscrições relacionadas), escolha **Tópicos**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="Escolha de entidade":::

Depois de escolher as **'Filas'** ou **'Tópicos',** escolha a fila ou tópico específico.

Selecione o **'Service Bus Explorer (pré-visualização)'** a partir do menu de navegação à esquerda

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Menu de navegação esquerda SB Explorer":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Enviar uma mensagem para uma fila ou tópico

Para enviar uma mensagem para uma **fila** ou **tópico,** clique no separador **_Enviar_* _ no Service Bus Explorer.

Para compor uma mensagem aqui - 

1. Escolha o _ *Tipo de Conteúdo** para ser 'Texto/Planície', 'Aplicação/Xml' ou 'Aplicação/Json'.
2. Adicionar a mensagem **Conteúdo**. Certifique-se de que corresponde ao conjunto **De Tipo de Conteúdo** anteriormente.
3. Desaver as **Propriedades Avançadas** (opcional) - estas incluem ID de correlação, ID de mensagem, etiqueta, respostaTo, tempo para viver (TTL) e tempo de enqueue programado (para mensagens agendadas).
4. Desatadas as **Propriedades Personalizadas** - podem ser quaisquer propriedades do utilizador definidas contra uma chave de dicionário.

Uma vez composta a mensagem, acerte enviar.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Composição Mensagem":::

Quando a operação de envio estiver concluída com sucesso, 

* Se enviar para a fila, o contador de métricas **de Mensagens Ativas** irá incrementar.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Filas Após ADesempenhada":::

* Se enviar para o tópico, o contador de métricas **de Mensagens Ativas** incrementará na Subscrição para onde a mensagem foi encaminhada.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TópicoAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Receber uma mensagem de uma fila

A função de receção no Service Bus Explorer permite receber uma única mensagem de cada vez. A operação de receção é realizada utilizando o modo **ReceberAndDelete.**

> [!IMPORTANT]
> Tenha em atenção que a operação Receber realizada pelo explorador de autocarros de serviço é uma *_receção destrutiva_* _, ou seja, a mensagem é removida da fila quando é exibida na ferramenta Service Bus Explorer.
>
> Para navegar nas mensagens sem as retirar da fila, considere utilizar a funcionalidade _*_Peek._*_
>

Para receber uma mensagem de uma fila (ou da sua subqueue de carta morta) 

1. Clique no _*_separador Receber_*_ no Service Bus Explorer.
2. Verifique as métricas para ver se existem _ *Mensagens Ativas** ou **Mensagens com letras mortas** para receber.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Filas Após ADesempenhada":::

3. Escolha entre a *_fila_** ou o subqueue _*_da Carta Morta._*_

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrdeadletter":::

4. Clique no botão _*_Receber,_*_ seguido de _*_Sim_*_ para confirmar a operação 'Receber e Apagar'.


Quando a operação de receção for bem sucedida, os detalhes da mensagem serão apresentados na grelha como abaixo. Pode selecionar a mensagem da grelha para mostrar os seus dados.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Screenshot da janela queues no Azure Service Bus Explorer com detalhes de mensagem apresentados para uma mensagem ativa que é selecionada na fila.":::


### <a name="peeking-a-message-from-a-queue"></a>Espreitando uma mensagem de uma fila

Com a funcionalidade de espreitar, pode utilizar o Service Bus Explorer para visualizar as 32 melhores mensagens numa fila ou na fila da carta morta.

1. Para espreitar a mensagem numa fila, clique no _*_separador Peek_*_ no Service Bus Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Verifique as métricas para ver se existem _ *Mensagens Ativas** ou **Mensagens com letras mortas** para espreitar.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Filas Após ADesempenhada":::

3. Em seguida, escolha entre a *_fila_** ou o subqueue _*_deadletter._*_

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrdeadletter":::

4. Clique no botão _*_Peek._*_ 

Uma vez concluída a operação de espreitar, até 32 mensagens aparecerão na grelha como abaixo. Para ver os detalhes de uma determinada mensagem, selecione-a a partir da grelha. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Uma vez que espreitar não é uma operação destrutiva, a mensagem *não* será removida da fila.
>

### <a name="receiving-a-message-from-a-subscription"></a>Receber uma mensagem de uma Assinatura

Tal como acontece com uma fila, a operação **_Receive_* _ pode ser executada contra uma subscrição (ou a sua entidade de carta morta). No entanto, uma vez que uma Subscrição vive no contexto do Tópico, a operação de receção é realizada navegando para o Service Bus Explorer para um determinado tópico.

> [!IMPORTANT]
> Tenha em atenção que a operação Receber realizada pelo explorador de autocarros de serviço é uma _*_receção destrutiva,_*_ ou seja, a mensagem é removida da fila quando é exibida na ferramenta Service Bus Explorer.
>
> Para navegar nas mensagens sem as retirar da fila, considere utilizar a funcionalidade _*_Peek._*_
>

1. Clique no separador _*_Receber_*_ e selecione a _*_Subscrição_*_ específica do seletor de entrega.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceberTabSelected":::

2. Escolha entre a _*_subscrição_*_ ou a sub-entidade _*_DeadLetter._*_

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Clique no botão _*_Receber,_*_ seguido de _*_Sim_*_ para confirmar a operação 'Receber e Apagar'.

Quando a operação de receção for bem sucedida, a mensagem recebida será exibida na grelha como abaixo. Para ver os detalhes da mensagem, clique na mensagem.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Screenshot do separador Receber no Azure Service Bus Explorer com detalhes de mensagem apresentados para uma mensagem ativa que foi recebida.":::

### <a name="peeking-a-message-from-a-subscription"></a>Espreitar uma mensagem de uma assinatura

Para simplesmente navegar nas mensagens de uma Subscrição ou da sua sub-entidade deadletter, a funcionalidade _*_Peek_*_ também pode ser utilizada na Subscrição.

1. Clique no _*_separador Peek_*_ e selecione a _*_Subscrição_*_ específica a partir do seletor de dropdown.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSeleed":::

2. Escolha entre a _*_Subscrição_*_ ou a _*_sub-entidade DeadLetter._*_

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Clique no botão _*_Peek._*_

Uma vez concluída a operação de espreitar, até 32 mensagens aparecerão na grelha como abaixo. Para ver os detalhes de uma determinada mensagem, selecione-a a partir da grelha. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Uma vez que espreitar não é uma operação destrutiva, a mensagem *não* será removida da fila.
>

## <a name="next-steps"></a>Passos Seguintes

   * Saiba mais sobre as filas e [tópicos de](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) [autocarros](service-bus-queues-topics-subscriptions.md#queues) de serviço
   * Saiba mais sobre a criação [de filas de autocarros de serviço através do portal Azure](service-bus-quickstart-portal.md)
   * Saiba mais sobre a criação [de tópicos e subscrições de autocarros de serviço através do portal Azure](service-bus-quickstart-topics-subscriptions-portal.md)
