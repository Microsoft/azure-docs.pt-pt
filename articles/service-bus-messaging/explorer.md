---
title: Utilize o Azure Service Bus Explorer para realizar operações de dados no Bus de Serviço (Pré-visualização)
description: Este artigo fornece informações sobre como usar o Azure Service Bus Explorer baseado no portal para aceder aos dados do Azure Service Bus.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: aschhab
ms.openlocfilehash: 8f53b5e41c67ca1990edd011677b25f3a9a601d1
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776770"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Use service bus explorer para realizar operações de dados no Ônibus de serviço (Pré-visualização)

## <a name="overview"></a>Descrição geral

O Azure Service Bus permite que as aplicações de clientes de remetente e recetor estoque da sua lógica de negócio com a utilização de semânticas familiares de ponto a ponto (Fila) e de subscrição de publicações (Tópico-Subscrição).

As operações realizadas num espaço de nome de autocarro de serviço Azure são de dois tipos 
   * Operações de Gestão - Criar, Atualizar, Eliminar o espaço de nome do autocarro de serviço, filas, tópicos e subscrições.
   * Operações de Dados - Enviar e receber Mensagens de Filas, Tópicos e Subscrições.

O Azure Service Bus Explorer expande a funcionalidade do portal para além das operações de gestão para apoiar operações de dados (Enviar, Receber, Espreitar) nas filas, tópicos e subscrições (e suas subentidades de letra morta) - a partir do próprio portal Azure.

> [!NOTE]
> Este artigo destaca a funcionalidade do Azure Service Bus Explorer que vive no portal Azure.
>
> A ferramenta de explorador de ônibus de serviço Azure ***não*** é a ferramenta comunitária OSS [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer)
>

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a ferramenta Service Bus Explorer, terá de fornecer um espaço de nome de ônibus de serviço Azure. 

Uma vez que o espaço de nome do Ônibus de serviço é provisionado, você precisa criar uma fila para enviar e receber mensagem de ou um Tópico com uma Subscrição para testar a funcionalidade.

Para saber mais sobre como criar filas, tópicos e subscrições, consulte os links abaixo
   * [Quickstart - Criar filas](service-bus-quickstart-portal.md)
   * [Quickstart - Criar Tópicos](service-bus-quickstart-topics-subscriptions-portal.md)


## <a name="using-the-service-bus-explorer"></a>Usando o Explorador de Ônibus de Serviço

Para utilizar o explorador de ônibus de serviço Azure, você precisa navegar para o espaço de nome service Bus no qual você deseja executar enviar, espreitar e receber operações.

Se procura realizar operações contra uma fila, escolha **'Filas'** do menu de navegação. Se procura realizar operações contra um Tópico (e são subscrições relacionadas), escolha **Tópicos**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="Selecione entidade":::

Depois de escolher as **'Filas'** ou **'Tópicos',** escolha a fila ou tópico específico.

Selecione o **'Service Bus Explorer (pré-visualização)'** do menu de navegação à esquerda

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Menu de navegação deixada sb explorer":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Envio de uma mensagem para uma fila ou tópico

Para enviar uma mensagem para uma **fila** ou um **Tópico,** clique no separador ***Enviar*** no Explorer de Autocarrode Serviço.

Para compor uma mensagem aqui - 

1. Escolha o Tipo de **Conteúdo** para ser 'Texto/Planície', 'Aplicação/Xml' ou 'Application/Json'.
2. Adicione o **conteúdo**da mensagem . Certifique-se de que corresponde ao conjunto de **Tipo de Conteúdo** mais cedo.
3. Definir as **Propriedades Avançadas** (opcional) - estas incluem Id de Correlação, ID de Mensagem, Etiqueta, RespostaTo, Tempo para Viver (TTL) e Hora de Enfila Agendada (para Mensagens Agendadas).
4. Despfique as **Propriedades Personalizadas** - pode ser qualquer propriedade do utilizador definida contra uma chave de dicionário.

Uma vez que a mensagem tenha sido composta, acerte o envio.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Mensagem de composição":::

Quando a operação de envio for concluída com sucesso, 

* Se enviar para a fila, o contador de métricas **de Mensagens Ativas** irá aumentar.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* Se enviar para o Tópico, o contador de métricas **de Mensagens Ativas** irá aumentar na Subscrição para onde a mensagem foi encaminhada.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Recebendo uma mensagem de uma fila

A função de receção no Service Bus Explorer permite receber uma única mensagem de cada vez. A operação de receção é executada utilizando o modo **ReceiveAndDelete.**

> [!IMPORTANT]
> Por favor, note que a operação Receive realizada pelo explorador de ônibus de serviço é uma ***receção destrutiva***, ou seja, a mensagem é removida da fila quando é exibida na ferramenta Service Bus Explorer.
>
> Para navegar em mensagens sem removê-las da fila, considere utilizar a funcionalidade ***Peek.***
>

Para receber uma mensagem de uma fila (ou sua subfila de letras mortas) 

1. Clique no separador ***Receber*** no Explorador de Autocarros de Serviço.
2. Verifique as métricas para ver se existem **Mensagens Ativas** ou **Mensagens** com Letras Mortas para receber.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Escolha entre a ***fila*** ou a subfila ***da Deadletter.***

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="FilaOrDeadletter":::

4. Clique no botão ***Receber,*** seguido de ***Sim*** para confirmar a operação 'Receber e Excluir'.


Quando a operação de receção for bem sucedida, os detalhes da mensagem serão apresentados na grelha como abaixo. Pode selecionar a mensagem da grelha para mostrar os seus dados.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Receber MensagemFromqueue":::


### <a name="peeking-a-message-from-a-queue"></a>Espreitando uma mensagem de uma fila

Com a funcionalidade de espreitar, pode utilizar o Service Bus Explorer para ver as 32 mensagens mais bem vistas numa fila ou na fila da carta morta.

1. Para espreitar a mensagem numa fila, clique no separador ***Peek*** no Service Bus Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Verifique as métricas para ver se existem **Mensagens Ativas** ou **Mensagens** com Letras Mortas para espreitar.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Em seguida, escolha entre a ***fila*** ou a subfila ***da Carta Morta.***

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="FilaOrDeadletter":::

4. Clique no botão ***Peek.*** 

Uma vez concluída a operação de espreitar, até 32 mensagens aparecerão na grelha como abaixo. Para ver os detalhes de uma determinada mensagem, selecione-a a partir da grelha. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromqueue":::

> [!NOTE]
>
> Uma vez que espreitar não é uma operação destrutiva, a mensagem **não será** removida da fila.
>

### <a name="receiving-a-message-from-a-subscription"></a>Receber uma mensagem de uma Subscrição

Tal como com uma fila, a operação ***Receive*** pode ser realizada contra uma subscrição (ou a sua entidade de carta morta). No entanto, uma vez que uma Subscrição vive no contexto do Tópico, a operação de receção é realizada navegando para o Service Bus Explorer para um determinado Tópico.

> [!IMPORTANT]
> Por favor, note que a operação Receive realizada pelo explorador de ônibus de serviço é uma ***receção destrutiva***, ou seja, a mensagem é removida da fila quando é exibida na ferramenta Service Bus Explorer.
>
> Para navegar em mensagens sem removê-las da fila, considere utilizar a funcionalidade ***Peek.***
>

1. Clique no separador ***Receber*** e selecione a ***subscrição*** específica do seletor dropdown.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceberTabSelecionado":::

2. Escolha entre a ***Subscrição*** ou a sub-entidade ***DeadLetter.***

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriçãoOrDeadletter":::

3. Clique no botão ***Receber,*** seguido de ***Sim*** para confirmar a operação 'Receber e Excluir'.

Quando a operação recebida for bem sucedida, a mensagem recebida será exibida na grelha como abaixo. Para ver os detalhes da mensagem, clique na mensagem.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Receber MensagemFromqueue":::

### <a name="peeking-a-message-from-a-subscription"></a>Espreite uma mensagem de uma subscrição

Para simplesmente navegar nas mensagens numa Subscrição ou na sua sub-entidade de carta morta, a funcionalidade ***Peek*** também pode ser utilizada na Subscrição.

1. Clique no separador ***Peek*** e selecione a ***subscrição*** específica do seletor dropdown.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelecionado":::

2. Escolha entre a ***Subscrição*** ou a subentidade ***DeadLetter.***

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriçãoOrDeadletter":::

3. Clique no botão ***Peek.***

Uma vez concluída a operação de espreitar, até 32 mensagens aparecerão na grelha como abaixo. Para ver os detalhes de uma determinada mensagem, selecione-a a partir da grelha. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscri":::

> [!NOTE]
>
> Uma vez que espreitar não é uma operação destrutiva, a mensagem **não será** removida da fila.
>

## <a name="next-steps"></a>Passos Seguintes

   * Saiba mais sobre filas de [ônibus](service-bus-queues-topics-subscriptions.md#queues) de serviço e [tópicos](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * Saiba mais sobre a criação de filas de [ônibus de serviço através do portal Azure](service-bus-quickstart-portal.md)
   * Saiba mais sobre a criação de [Tópicos e Assinaturas de Ônibus de Serviço através do portal Azure](service-bus-quickstart-topics-subscriptions-portal.md)