---
title: Troca de mensagens com ônibus de serviço Azure
description: Criar tarefas automatizadas e fluxos de trabalho que enviam e recebem mensagens utilizando o Azure Service Bus em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 50f43283d1113a5beb05b5898514623be37e5de9
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141998"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Troca de mensagens na nuvem usando aplicativos azure logic e ônibus de serviço Azure

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o conector [Azure Service Bus,](../service-bus-messaging/service-bus-messaging-overview.md) pode criar tarefas automatizadas e fluxos de trabalho que transferem dados, tais como encomendas de vendas e compras, revistas e movimentos de inventário em aplicações para a sua organização. O conector não só monitoriza, envia e gere mensagens, mas também executa ações com filas, sessões, tópicos, subscrições, e assim por diante, por exemplo:

* Monitorize quando as mensagens chegarem (auto-completas) ou sejam recebidas (peek-lock) em filas, tópicos e subscrições de tópicos.
* Envie mensagens.
* Criar e eliminar subscrições de tópicos.
* Gerencie mensagens em filas e subscrições de tópicos, por exemplo, obter, obter diferido, completo, adiar, abandonar e carta morta.
* Renovar bloqueios em mensagens e sessões em filas e subscrições de tópicos.
* Encerrar sessões em filas e tópicos.

Pode utilizar gatilhos que obtêm respostas do Service Bus e disponibilizar a saída a outras ações nas suas aplicações lógicas. Também pode ter outras ações a utilizar a saída das ações do Service Bus. Se é novo em Aplicações de Ônibus de Serviço e Lógica, reveja [o que é o Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) e o que é [azure Logic Apps?](../logic-apps/logic-apps-overview.md)

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um espaço de nome de ônibus de serviço e entidade de mensagens, como uma fila. Estes itens e a sua aplicação lógica precisam de usar a mesma subscrição do Azure. Se não tiver estes itens, aprenda a criar o seu espaço de nome service [Bus e uma fila](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde utiliza o espaço de nome do Bus de Serviço e a entidade de mensagens. A sua aplicação lógica e o autocarro de serviço precisam de usar a mesma subscrição Azure. Para iniciar o seu fluxo de trabalho com um gatilho de ônibus de serviço, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação do Service Bus no seu fluxo de trabalho, inicie a sua aplicação lógica com outro gatilho, por exemplo, o [gatilho recurrence](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verifique permissões

Confirme que a sua aplicação lógica tem permissões para aceder ao seu espaço de nome service Bus.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá ao seu espaço de *nome*service Bus . Na página do espaço de nome, em **Definições,** selecione políticas de **acesso partilhado**. Em **Sinistros,** verifique se tem permissões **de gestão** para esse espaço de nome.

   ![Gerir permissões para o espaço de nome do ônibus de serviço](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Obtenha a corda de ligação para o seu espaço de nome service Bus. Precisa desta corda quando fornece as informações de ligação na sua aplicação lógica.

   1. No painel de políticas de **acesso partilhado,** selecione **RootManageSharedAccessKey**.
   
   1. Ao lado da corda de ligação primária, selecione o botão de cópia. Guarde a corda de ligação para posterior utilização.

      ![Linha de ligação nome do ônibus de serviço de cópia](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se a sua cadeia de ligação está associada ao seu espaço de nome service Bus ou a uma entidade de mensagens, como uma fila, procure na cadeia de ligação o `EntityPath`   parâmetro. Se encontrar este parâmetro, a cadeia de ligação é para uma entidade específica, e não é a corda correta para usar com a sua aplicação lógica.

## <a name="add-service-bus-trigger"></a>Adicionar gatilho de ônibus de serviço

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, introduza o "ônibus de serviço azul" como filtro. A partir da lista de gatilhos, selecione o gatilho que deseja.

   Por exemplo, para acionar a sua aplicação lógica quando um novo item é enviado para uma fila de ônibus de serviço, selecione o Quando uma mensagem é recebida num gatilho **de fila (automática).**

   ![Selecione o gatilho do ônibus de serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Todos os gatilhos do Autocarro de Serviço são gatilhos *de sondagens longas.* Esta descrição significa que quando o gatilho dispara, o gatilho processa todas as mensagens e espera 30 segundos para que mais mensagens apareçam na fila ou na subscrição do tópico. Se não aparecerem mensagens em 30 segundos, o gatilho é ignorado. Caso contrário, o gatilho continua a ler mensagens até que a fila ou a subscrição do tópico esteja vazia. A próxima sondagem é baseada no intervalo de recorrência especificado nas propriedades do gatilho.

   Alguns gatilhos, como o Quando uma ou mais mensagens chegam num gatilho **de fila (auto-completo),** podem devolver uma ou mais mensagens. Quando estes disparam, regressam entre uma e o número de mensagens especificadas pela propriedade de contagem máxima de **mensagens** do gatilho.

1. Se o seu gatilho estiver a ligar-se ao seu espaço de nome do Autocarro de Serviço pela primeira vez, siga estes passos quando o Logic App Designer lhe pedir informações sobre ligação.

   1. Forneça um nome para a sua ligação e selecione o seu espaço de nome service Bus.

      ![Criar conexão de ônibus de serviço, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Para introduzir manualmente a cadeia de ligação, selecione **manualmente as informações**de ligação . Se não tiver a sua corda de ligação, aprenda a encontrar a sua corda de [ligação.](#permissions-connection-string)

   1. Selecione a sua política de ônibus de serviço e selecione **Criar**.

      ![Criar conexão de ônibus de serviço, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Selecione a entidade de mensagens que deseja, como uma fila ou tópico. Para este exemplo, selecione a sua fila de ônibus de serviço.
   
      ![Selecione fila de ônibus de serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Forneça as informações necessárias para o seu gatilho selecionado. Para adicionar outras propriedades disponíveis à ação, abra a **lista de novos parâmetros e** selecione as propriedades que deseja.

   Para este gatilho, selecione o intervalo de votação e a frequência para verificar a fila.

   ![Configurar o intervalo de sondagens](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Para obter mais informações sobre os gatilhos e propriedades disponíveis, consulte a página de [referência](/connectors/servicebus/)do conector .

1. Continue a construir a sua aplicação lógica adicionando as ações que deseja.

   Por exemplo, pode adicionar uma ação que envia e-mail quando uma nova mensagem chega. Quando o gatilho verifica a sua fila e encontra uma nova mensagem, a sua aplicação lógica executa as suas ações selecionadas para a mensagem encontrada.

## <a name="add-service-bus-action"></a>Adicionar ação de ônibus de serviço

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer.

1. Sob o degrau onde pretende adicionar uma ação, selecione **Novo passo**.

   Ou, para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre esses passos. Selecione o sinal de mais ( **+** ) que aparece e selecione Adicionar uma **ação**.

1. Em **'Escolha uma ação**' na caixa de pesquisa, introduza "ônibus de serviço azul" como filtro. Na lista de ações, selecione a ação que deseja. 

   Para este exemplo, selecione a ação **Enviar** mensagem.

   ![Selecione ação de ônibus de serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se a sua ação estiver a ligar-se ao seu espaço de nome service Bus pela primeira vez, siga estes passos quando o Logic App Designer lhe pedir informações sobre ligação.

   1. Forneça um nome para a sua ligação e selecione o seu espaço de nome service Bus.

      ![Criar conexão de ônibus de serviço, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Para introduzir manualmente a cadeia de ligação, selecione **manualmente as informações**de ligação . Se não tiver a sua corda de ligação, aprenda a encontrar a sua corda de [ligação.](#permissions-connection-string)

   1. Selecione a sua política de ônibus de serviço e selecione **Criar**.

      ![Criar conexão de ônibus de serviço, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Selecione a entidade de mensagens que deseja, como uma fila ou tópico. Para este exemplo, selecione a sua fila de ônibus de serviço.

      ![Selecione fila de ônibus de serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Forneça os detalhes necessários para a sua ação selecionada. Para adicionar outras propriedades disponíveis à ação, abra a **lista de novos parâmetros e** selecione as propriedades que deseja.

   Por exemplo, selecione as propriedades **do Tipo de Conteúdo** e **Conteúdo** para que as adicione à ação. Em seguida, especifique o conteúdo da mensagem que pretende enviar.

   ![Fornecer conteúdo e detalhes da mensagem](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Para obter mais informações sobre as ações disponíveis e as suas propriedades, consulte a página de [referência](/connectors/servicebus/)do conector .

1. Continue a construir a sua aplicação lógica adicionando quaisquer outras ações que queira.

   Por exemplo, pode adicionar uma ação que envia e-mail para confirmar que a sua mensagem foi enviada.

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>Enviar mensagens correlacionadas por ordem

Quando necessitar de enviar mensagens relacionadas numa encomenda específica, pode utilizar o padrão de [ *comboio sequencial* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy) utilizando o [conector Azure Service Bus](../connectors/connectors-create-api-servicebus.md). As mensagens correlacionadas têm uma propriedade que define a relação entre essas mensagens, como o ID para a [sessão](../service-bus-messaging/message-sessions.md) no Service Bus.

Ao criar uma aplicação lógica, pode selecionar a entrega em encomenda da Correlated utilizando o modelo de **sessões de ônibus** de serviço, que implementa o padrão de comboio sequencial. Para mais informações, consulte [Enviar mensagens relacionadas por encomenda](../logic-apps/send-related-messages-sequential-convoy.md).

<a name="connector-reference"></a>

## <a name="connector-reference"></a>Referência do conector

O conector Service Bus pode economizar até 1.500 sessões únicas de cada vez, desde um autocarro de serviço até à cache do conector. Se a contagem de sessões exceder este limite, as sessões antigas são removidas da cache. Para mais informações, consulte [as sessões de mensagem](../service-bus-messaging/message-sessions.md).

Para outros detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição swagger do conector, reveja a página de referência do [conector](/connectors/servicebus/). Para mais informações sobre as Mensagens de Ônibus de Serviço Azure, veja [o que é o Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
