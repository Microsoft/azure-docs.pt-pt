---
title: Trocar mensagens com o Azure Service Bus
description: Crie tarefas automatizadas e fluxos de trabalho que enviem e recebam mensagens utilizando o Azure Service Bus em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 10/22/2020
tags: connectors
ms.openlocfilehash: b6276ff940d8b156a671cb5386ce53ede30dd879
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426646"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Troque mensagens na nuvem usando Azure Logic Apps e Azure Service Bus

Com [a Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o conector [Azure Service Bus,](../service-bus-messaging/service-bus-messaging-overview.md) pode criar tarefas automatizadas e fluxos de trabalho que transferem dados, tais como encomendas de vendas e compras, revistas e movimentos de inventário através de aplicações para a sua organização. O conector não só monitoriza, envia e gere mensagens, como também realiza ações com filas, sessões, tópicos, subscrições, e assim por diante, por exemplo:

* Monitorize quando as mensagens chegarem (completas automaticamente) ou são recebidas (peek-lock) em filas, tópicos e subscrições de tópicos.
* Enviar mensagens.
* Criar e eliminar subscrições de tópicos.
* Gerir mensagens em filas e subscrições de tópicos, por exemplo, obter, ser adiado, completo, adiado, abandono e letra morta.
* Renovar bloqueios em mensagens e sessões em filas e subscrições de tópicos.
* Sessões de encerramento em filas e tópicos.

Pode utilizar gatilhos que obtenham respostas da Service Bus e disponibilize a saída para outras ações nas suas aplicações lógicas. Também pode ter outras ações que utilizem a saída das ações da Service Bus. Se é novo em Service Bus and Logic Apps, [reveja o que é Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) e O que é [Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um espaço de nome de ônibus de serviço e entidade de mensagens, como uma fila. Estes itens e a sua aplicação lógica precisam de utilizar a mesma subscrição do Azure. Se não tiver estes itens, aprenda a criar o seu espaço de [nomes de Service Bus e uma fila.](../service-bus-messaging/service-bus-create-namespace-portal.md)

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde utiliza o espaço de nomes do Service Bus e a entidade de mensagens. A sua aplicação lógica e o autocarro de serviço precisam de utilizar a mesma subscrição do Azure. Para iniciar o seu fluxo de trabalho com um gatilho service bus, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação de Service Bus no seu fluxo de trabalho, inicie a sua aplicação lógica com outro gatilho, por exemplo, o [gatilho recorrência](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verificar permissões

Confirme que a sua aplicação lógica tem permissões para aceder ao seu espaço de nomes de Service Bus.

1. No [portal Azure,](https://portal.azure.com)inscreva-se na sua conta Azure.

1. Vá ao seu *espaço de nomes*de Service Bus . Na página do espaço de nomes, em **Definições,** **selecione Políticas de acesso partilhado**. Em **Sinistros,** verifique se tem permissões **de Gestão** para esse espaço de nome.

   ![Gerir permissões para o espaço de nomes do Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Obtenha a cadeia de ligação para o seu espaço de nomes de Service Bus. Precisa desta cadeia quando fornece as informações de ligação na sua aplicação lógica.

   1. No painel **de políticas de acesso partilhado,** selecione **RootManageSharedAccessKey**.

   1. Ao lado da sua cadeia de ligação primária, selecione o botão de cópia. Guarde o fio de ligação para utilização posterior.

      ![Cadeia de conexão de espaço de nome do serviço de cópia do serviço de serviço](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se a sua cadeia de ligação está associada ao seu espaço de nomes de Service Bus ou a uma entidade de mensagens, como uma fila, procure a cadeia de ligação para o `EntityPath` parâmetro. Se encontrar este parâmetro, a cadeia de ligação é para uma entidade específica, e não é a cadeia correta para usar com a sua aplicação lógica.

## <a name="add-service-bus-trigger"></a>Adicionar gatilho de ônibus de serviço

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa do portal, insira `azure service bus` . A partir da lista de gatilhos que aparece, selecione o gatilho que deseja.

   Por exemplo, para ativar a sua aplicação lógica quando um novo item for enviado para uma fila de Autocarros de Serviço, selecione a Quando uma mensagem é recebida num gatilho **de fila (completa automaticamente).**

   ![Selecione o gatilho do ônibus de serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Aqui ficam algumas considerações para quando utilizar um gatilho de ônibus de serviço:

   * Todos os gatilhos do Service Bus são gatilhos *de sondagens longas.* Esta descrição significa que quando o gatilho dispara, o gatilho processa todas as mensagens e, em seguida, aguarda 30 segundos para que mais mensagens apareçam na fila ou subscrição de tópicos. Se não aparecerem mensagens em 30 segundos, o gatilho é ignorado. Caso contrário, o gatilho continua a ler mensagens até que a fila ou a subscrição de tópicos esteja vazia. A próxima sondagem do gatilho baseia-se no intervalo de recorrência especificado nas propriedades do gatilho.

   * Alguns gatilhos, como o Quando uma ou mais mensagens chegam num gatilho **de fila (completa automaticamente),** podem devolver uma ou mais mensagens. Quando estes disparam fogo, eles retornam entre um e o número de mensagens especificadas pela propriedade de **contagem de mensagens máximas** do gatilho.

     > [!NOTE]
     > O gatilho auto-completo completa automaticamente uma mensagem, mas a conclusão só acontece na próxima chamada para o Service Bus. Este comportamento pode afetar o design da sua aplicação lógica. Por exemplo, evite alterar a concordância no gatilho auto-completo porque esta alteração pode resultar em mensagens duplicadas se a sua aplicação lógica entrar num estado acelerado. A alteração do controlo de concordância cria estas condições: os gatilhos acelerados são ignorados com o `WorkflowRunInProgress` código, a operação de conclusão não acontecerá e o próximo gatilho ocorre após o intervalo de votação. Tem de definir a duração do bloqueio do autocarro de serviço para um valor mais longo do que o intervalo de votação. No entanto, apesar desta definição, a mensagem ainda pode não estar completa se a sua aplicação lógica se mantiver num estado acelerado no próximo intervalo de votação.

   * Se [ligar a definição de concordância](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) para um gatilho de ônibus de serviço, o valor padrão para a `maximumWaitingRuns` propriedade é de 10. Com base na definição de duração do bloqueio da entidade Service Bus e na duração de funcionação da sua aplicação lógica, este valor padrão pode ser demasiado grande e pode causar uma exceção "lock lost". Para encontrar o valor ideal para o seu cenário, comece a testar com um valor de 1 ou 2 para a `maximumWaitingRuns` propriedade. Para alterar o valor máximo de execuções de espera, consulte [o limite de execução de execuções de espera change](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs).

1. Se o seu gatilho estiver a ligar-se ao seu espaço de nomes service bus pela primeira vez, siga estes passos quando o Logic App Designer lhe solicitar informações de ligação.

   1. Forneça um nome para a sua ligação e selecione o seu espaço de nomes de Service Bus.

      ![Screenshot que mostra fornecer nome de conexão e selecionar espaço de nome de Service Bus](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Para introduzir manualmente a cadeia de ligação, selecione **instrua manualmente as informações de ligação**. Se não tiver a sua cadeia de ligação, aprenda [a encontrar a sua cadeia de ligação](#permissions-connection-string).

   1. Selecione a sua política de ônibus de serviço e **selecione Criar**.

      ![Screenshot que mostra a política de ônibus de serviço selecionado](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Selecione a entidade de mensagens que deseja, como uma fila ou tópico. Para este exemplo, selecione a sua fila de ônibus de serviço.
   
      ![Screenshot que mostra a seleção da fila do ônibus de serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Forneça as informações necessárias para o seu gatilho selecionado. Para adicionar outras propriedades disponíveis à ação, abra a nova lista **de parâmetros Add** e selecione as propriedades que deseja.

   Para este gatilho, selecione o intervalo de votação e a frequência para verificar a fila.

   ![Screenshot que mostra a definição do intervalo de votação no gatilho do Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Para obter mais informações sobre os gatilhos e propriedades disponíveis, consulte a página de [referência](/connectors/servicebus/)do conector .

1. Continue a construir a sua aplicação lógica adicionando as ações que deseja.

   Por exemplo, pode adicionar uma ação que envia e-mail quando uma nova mensagem chega. Quando o seu gatilho verifica a sua fila e encontra uma nova mensagem, a sua aplicação lógica executa as suas ações selecionadas para a mensagem encontrada.

## <a name="add-service-bus-action"></a>Adicionar ação de ônibus de serviço

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Sob o passo onde pretende adicionar uma ação, selecione **Novo passo**.

   Ou, para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre esses degraus. Selecione o sinal de mais **+** () que aparece e selecione **Adicione uma ação**.

1. Em **Escolha uma ação,** na caixa de pesquisa, insira `azure service bus` . Da lista de ações que aparece, selecione a ação que deseja. 

   Para este exemplo, selecione a ação **da mensagem Enviar.**

   ![Screenshot que mostra a seleção da ação de Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se a sua ação estiver a ligar-se ao seu espaço de nomes service bus pela primeira vez, siga estes passos quando o Logic App Designer lhe solicitar informações de ligação.

   1. Forneça um nome para a sua ligação e selecione o seu espaço de nomes de Service Bus.

      ![Screenshot que mostra fornecer um nome de conexão e selecionar um espaço de nome de Service Bus](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Para introduzir manualmente a cadeia de ligação, selecione **instrua manualmente as informações de ligação**. Se não tiver a sua cadeia de ligação, aprenda [a encontrar a sua cadeia de ligação](#permissions-connection-string).

   1. Selecione a sua política de ônibus de serviço e **selecione Criar**.

      ![Screenshot que mostra a seleção de uma política de ônibus de serviço e selecionando o botão Criar](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Selecione a entidade de mensagens que deseja, como uma fila ou tópico. Para este exemplo, selecione a sua fila de ônibus de serviço.

      ![Screenshot que mostra a seleção de uma fila de ônibus de serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Forneça os detalhes necessários para a sua ação selecionada. Para adicionar outras propriedades disponíveis à ação, abra a nova lista **de parâmetros Add** e selecione as propriedades que deseja.

   Por exemplo, selecione as propriedades **do Tipo de Conteúdo** e **Conteúdo** para que as adicione à ação. Em seguida, especifique o conteúdo da mensagem que pretende enviar.

   ![Screenshot que mostra fornecer o tipo de conteúdo de mensagem e detalhes](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Para obter mais informações sobre as ações disponíveis e as suas propriedades, consulte a página de [referência](/connectors/servicebus/)do conector.

1. Continue a construir a sua aplicação lógica adicionando quaisquer outras ações que queira.

   Por exemplo, pode adicionar uma ação que envia e-mail para confirmar que a sua mensagem foi enviada.

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>Enviar mensagens correlacionadas por ordem

Quando precisa enviar mensagens relacionadas numa ordem específica, pode utilizar o padrão do [ *comboio sequencial* ](/azure/architecture/patterns/sequential-convoy) utilizando o [conector Azure Service Bus](../connectors/connectors-create-api-servicebus.md). As mensagens correlacionadas têm uma propriedade que define a relação entre essas mensagens, como o ID para a [sessão](../service-bus-messaging/message-sessions.md) em Service Bus.

Ao criar uma aplicação lógica, pode selecionar a **entrega de encomenda correlacionada utilizando** o modelo de sessões de autocarro de serviço, que implementa o padrão de comboio sequencial. Para obter mais informações, consulte [Enviar mensagens relacionadas por ordem](../logic-apps/send-related-messages-sequential-convoy.md).

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>Atrasos nas atualizações da sua app lógica a entrar em vigor

Se o intervalo de votação de um gatilho do Service Bus for pequeno, como 10 segundos, as atualizações para a sua aplicação lógica podem não produzir efeitos até 10 minutos. Para resolver este problema, pode aumentar temporariamente o intervalo de votação para um valor maior, como 30 segundos ou 1 minuto, antes de atualizar a sua aplicação lógica. Depois de fazer a atualização, pode redefinir o intervalo de votação para o valor original. 

<a name="connector-reference"></a>

## <a name="connector-reference"></a>Referência do conector

A partir de um autocarro de serviço, o conector Service Bus pode economizar até 1.500 sessões únicas de cada vez para a cache do conector, por [entidade de mensagens Service Bus, como uma subscrição ou tópico](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Se a contagem de sessão exceder este limite, as sessões antigas são removidas da cache. Para mais informações, consulte [as sessões de Mensagem](../service-bus-messaging/message-sessions.md).

Para outros detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição do Conector Swagger, consulte a página de referência do [conector](/connectors/servicebus/). Para mais informações sobre a Azure Service Bus Messaging, veja [o que é o Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
