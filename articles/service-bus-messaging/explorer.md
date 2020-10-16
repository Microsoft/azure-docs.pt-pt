---
title: Use o Azure Service Bus Explorer para realizar operações de dados no Service Bus (Preview)
description: Este artigo fornece informações sobre como usar o Azure Service Bus Explorer baseado no portal para aceder aos dados do Azure Service Bus.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: e5e97c6860c2cc01048f4f7caf9f40f9e07592d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295605"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Utilize o Service Bus Explorer para realizar operações de dados no Service Bus (Preview)

## <a name="overview"></a>Descrição geral

A Azure Service Bus permite que as aplicações de clientes remetentes e recetores dissolvam a sua lógica de negócio com o uso da semântica familiar ponto-a-ponto (Fila) e subscreva a semântica de subscrição de tópicos.

As operações realizadas num espaço de nomes de autocarros da Azure Service são de dois tipos 
   * Operações de Gestão - Criar, Atualizar, Eliminar o Espaço de Nomes de Autocarros de Serviço, Filas, Tópicos e Subscrições.
   * Operações de Dados - Enviar e Receber Mensagens de Filas, Tópicos e Assinaturas.

O Azure Service Bus Explorer expande a funcionalidade do portal para além das operações de gestão para apoiar as operações de dados (Enviar, Receber, Espreitar) nas Filas, Tópicos e Assinaturas (e suas subentuidades de cartas mortas) - desde o próprio portal Azure.

> [!NOTE]
> Este artigo destaca a funcionalidade do Azure Service Bus Explorer que vive no portal Azure.
>
> A ferramenta de explorador de autocarros Azure Service ***não*** é propriedade da comunidade da ferramenta OSS [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a ferramenta Service Bus Explorer, terá de providenciar um espaço de nomes para o Azure Service Bus. 

Uma vez que o espaço de nome do Service Bus é a provisionado, você precisa criar uma fila para enviar e receber mensagem de ou um tópico com uma Subscrição para testar a funcionalidade.

Para saber mais sobre como criar filas, tópicos e subscrições, consulte os links abaixo
   * [Quickstart - Criar filas](service-bus-quickstart-portal.md)
   * [Quickstart - Criar Tópicos](service-bus-quickstart-topics-subscriptions-portal.md)


## <a name="using-the-service-bus-explorer"></a>Utilização do Explorador de Autocarros de Serviço

Para utilizar o explorador de autocarros Azure Service, tem de navegar para o espaço de nomes do Service Bus no qual pretende realizar operações de envio, espreitar e receber operações.

Se procura realizar operações contra uma fila, escolha **'Filas'** no menu de navegação. Se procura realizar operações contra um tópico (e são subscrições relacionadas), escolha **Tópicos**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="Escolha de entidade":::

Depois de escolher as **'Filas'** ou **'Tópicos',** escolha a fila ou tópico específico.

Selecione o **'Service Bus Explorer (pré-visualização)'** a partir do menu de navegação à esquerda

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Escolha de entidade":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Enviar uma mensagem para uma fila ou tópico

Para enviar uma mensagem para uma **fila** ou **tópico,** clique no ***separador Enviar*** no Service Bus Explorer.

Para compor uma mensagem aqui - 

1. Escolha o **Tipo de Conteúdo** para ser 'Texto/Planície', 'Aplicação/Xml' ou 'Aplicação/Json'.
2. Adicionar a mensagem **Conteúdo**. Certifique-se de que corresponde ao conjunto **De Tipo de Conteúdo** anteriormente.
3. Desaver as **Propriedades Avançadas** (opcional) - estas incluem ID de correlação, ID de mensagem, etiqueta, respostaTo, tempo para viver (TTL) e tempo de enqueue programado (para mensagens agendadas).
4. Desatadas as **Propriedades Personalizadas** - podem ser quaisquer propriedades do utilizador definidas contra uma chave de dicionário.

Uma vez composta a mensagem, acerte enviar.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Escolha de entidade":::

Quando a operação de envio estiver concluída com sucesso, 

* Se enviar para a fila, o contador de métricas **de Mensagens Ativas** irá incrementar.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Escolha de entidade":::

* Se enviar para o tópico, o contador de métricas **de Mensagens Ativas** incrementará na Subscrição para onde a mensagem foi encaminhada.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="Escolha de entidade":::

### <a name="receiving-a-message-from-a-queue"></a>Receber uma mensagem de uma fila

A função de receção no Service Bus Explorer permite receber uma única mensagem de cada vez. A operação de receção é realizada utilizando o modo **ReceberAndDelete.**

> [!IMPORTANT]
> Tenha em atenção que a operação Receber realizada pelo explorador de autocarros de serviço é uma ***receção destrutiva,*** ou seja, a mensagem é removida da fila quando é exibida na ferramenta Service Bus Explorer.
>
> Para navegar nas mensagens sem as retirar da fila, considere utilizar a funcionalidade ***Peek.***
>

Para receber uma mensagem de uma fila (ou da sua subqueue de carta morta) 

1. Clique no ***separador Receber*** no Service Bus Explorer.
2. Verifique as métricas para ver se existem **Mensagens Ativas** ou **Mensagens com letras mortas** para receber.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Escolha de entidade":::

3. Escolha entre a ***fila*** ou o subqueue ***da Carta Morta.***

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="Escolha de entidade":::

4. Clique no botão ***Receber,*** seguido de ***Sim*** para confirmar a operação 'Receber e Apagar'.


Quando a operação de receção for bem sucedida, os detalhes da mensagem serão apresentados na grelha como abaixo. Pode selecionar a mensagem da grelha para mostrar os seus dados.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Escolha de entidade":::


### <a name="peeking-a-message-from-a-queue"></a>Espreitando uma mensagem de uma fila

Com a funcionalidade de espreitar, pode utilizar o Service Bus Explorer para visualizar as 32 melhores mensagens numa fila ou na fila da carta morta.

1. Para espreitar a mensagem numa fila, clique no ***separador Peek*** no Service Bus Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="Escolha de entidade":::

2. Verifique as métricas para ver se existem **Mensagens Ativas** ou **Mensagens com letras mortas** para espreitar.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Escolha de entidade":::

3. Em seguida, escolha entre a ***fila*** ou o subqueue ***da Carta Morta.***

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="Escolha de entidade":::

4. Clique no botão ***Peek.*** 

Uma vez concluída a operação de espreitar, até 32 mensagens aparecerão na grelha como abaixo. Para ver os detalhes de uma determinada mensagem, selecione-a a partir da grelha. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="Escolha de entidade":::

> [!NOTE]
>
> Uma vez que espreitar não é uma operação destrutiva, a mensagem **não será** removida da fila.
>

### <a name="receiving-a-message-from-a-subscription"></a>Receber uma mensagem de uma Assinatura

Tal como acontece com uma fila, a operação ***Receber*** pode ser executada contra uma subscrição (ou a sua entidade de carta morta). No entanto, uma vez que uma Subscrição vive no contexto do Tópico, a operação de receção é realizada navegando para o Service Bus Explorer para um determinado tópico.

> [!IMPORTANT]
> Tenha em atenção que a operação Receber realizada pelo explorador de autocarros de serviço é uma ***receção destrutiva,*** ou seja, a mensagem é removida da fila quando é exibida na ferramenta Service Bus Explorer.
>
> Para navegar nas mensagens sem as retirar da fila, considere utilizar a funcionalidade ***Peek.***
>

1. Clique no separador ***Receber*** e selecione a ***Subscrição*** específica do seletor de entrega.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="Escolha de entidade":::

2. Escolha entre a ***subscrição*** ou a sub-entidade ***DeadLetter.***

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="Escolha de entidade":::

3. Clique no botão ***Receber,*** seguido de ***Sim*** para confirmar a operação 'Receber e Apagar'.

Quando a operação de receção for bem sucedida, a mensagem recebida será exibida na grelha como abaixo. Para ver os detalhes da mensagem, clique na mensagem.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Escolha de entidade":::

### <a name="peeking-a-message-from-a-subscription"></a>Espreitar uma mensagem de uma assinatura

Para simplesmente navegar nas mensagens de uma Subscrição ou da sua sub-entidade deadletter, a funcionalidade ***Peek*** também pode ser utilizada na Subscrição.

1. Clique no ***separador Peek*** e selecione a ***Subscrição*** específica a partir do seletor de dropdown.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="Escolha de entidade":::

2. Escolha entre a ***Subscrição*** ou a ***sub-entidade DeadLetter.***

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="Escolha de entidade":::

3. Clique no botão ***Peek.***

Uma vez concluída a operação de espreitar, até 32 mensagens aparecerão na grelha como abaixo. Para ver os detalhes de uma determinada mensagem, selecione-a a partir da grelha. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="Escolha de entidade":::

> [!NOTE]
>
> Uma vez que espreitar não é uma operação destrutiva, a mensagem **não será** removida da fila.
>

## <a name="next-steps"></a>Passos Seguintes

   * Saiba mais sobre as filas e [tópicos de](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) [autocarros](service-bus-queues-topics-subscriptions.md#queues) de serviço
   * Saiba mais sobre a criação [de filas de autocarros de serviço através do portal Azure](service-bus-quickstart-portal.md)
   * Saiba mais sobre a criação [de tópicos e subscrições de autocarros de serviço através do portal Azure](service-bus-quickstart-topics-subscriptions-portal.md)