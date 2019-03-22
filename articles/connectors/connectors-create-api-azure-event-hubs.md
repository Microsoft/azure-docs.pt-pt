---
title: Ligar para os Hubs de eventos do Azure - Azure Logic Apps
description: Gerir e monitorizar eventos com os Hubs de eventos do Azure e Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: a59f21478f85f238d91c01faed44d8e49cb15f0a
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310800"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorizar, receber e enviar eventos com os Hubs de eventos do Azure e Azure Logic Apps

Este artigo mostra como pode monitorizar e gerir eventos enviados para o [os Hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) de dentro de uma aplicação lógica com o conector de Hubs de eventos do Azure. Dessa forma, pode criar aplicações lógicas que automatizam as tarefas e fluxos de trabalho para a verificação, enviar e receber eventos de seu Hub de eventos.

Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">referência do conector de Hubs de eventos</a>.

## <a name="prerequisites"></a>Pré-requisitos

* Um [espaço de nomes de Hubs de eventos do Azure e o Hub de eventos](../event-hubs/event-hubs-create.md)

* A aplicação de lógica onde pretende aceder ao seu Hub de eventos. Para começar a sua aplicação lógica com um acionador do Event Hubs do Azure, tem um [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Verifique as permissões e obter cadeia de ligação

Para a sua aplicação lógica aceder ao seu Hub de eventos, verifique as suas permissões e obter a cadeia de ligação para o espaço de nomes de Hubs de eventos.

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>.

2. Vá para os Hubs de eventos *espaço de nomes*, não é um Hub de eventos específico. Na página do espaço de nomes, sob **configurações**, escolha **políticas de acesso partilhado**. Sob **afirmações**, verifique se tem **gerir** permissões para esse espaço de nomes.

   ![Gerir permissões para o espaço de nomes do Hub de eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Se quiser manualmente mais tarde, introduza as informações da ligação, obtenha a cadeia de ligação para o espaço de nomes de Hubs de eventos.

   1. Sob **diretiva**, escolha **RootManageSharedAccessKey**.

   2. Localize a cadeia de ligação da chave primária. Escolha o botão de cópia e guarde a cadeia de ligação para utilização posterior.

      ![Copie a cadeia de ligação do espaço de nomes de Hubs de eventos](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Para confirmar se a cadeia de ligação está associada com o seu espaço de nomes de Hubs de eventos ou com um hub de eventos específico, certifique-se a cadeia de ligação não tem o `EntityPath`  parâmetro. Se encontrar este parâmetro, a cadeia de ligação é para um Hub de eventos específico "entidade" e não é a cadeia de caracteres correta para utilizar com a sua aplicação lógica.

4. Agora avance [adicionar um acionador de Hubs de eventos](#add-trigger) ou [adicionar uma ação de Hubs de eventos](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Adicionar um acionador de Hubs de eventos

No Azure Logic Apps, todas as aplicações lógicas têm de começar com uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico acontece ou quando for cumprida uma condição específica. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica e começa a ser executado o fluxo de trabalho da sua aplicação.

Este exemplo mostra como pode iniciar um fluxo de trabalho de aplicação lógica quando novos eventos são enviados para o Hub de eventos.

1. O portal do Azure ou o Visual Studio, crie uma aplicação lógica em branco, que abre o estruturador de aplicações lógicas. Este exemplo utiliza o portal do Azure.

2. Na caixa de pesquisa, introduza "os hubs de eventos" como o filtro. Na lista de disparadores, selecione o acionador que pretende.

   Este exemplo utiliza este acionador:

   **Hubs de eventos - quando eventos estão disponíveis no Hub de eventos**

   ![Selecionar acionador](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Se lhe for pedido para obter detalhes de ligação, [criar a ligação dos Hubs de eventos agora](#create-connection). Em alternativa, se a ligação já existir, fornecer as informações necessárias para o acionador.

   1. Partir do **nome do Hub de eventos** , selecione o Hub de eventos que pretende monitorizar.

      ![Especifique o Hub de eventos ou grupo de consumidores](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Selecione o intervalo e a frequência para a frequência com que pretende que o acionador para verificar o Hub de eventos.

   3. A opção de selecionar algumas das opções avançadas de Acionador, escolha **Mostrar opções avançadas**.

      ![Opções avançadas de Acionador](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Propriedade | Detalhes | 
      |----------|---------| 
      | Tipo de conteúdo  | Selecione o tipo de conteúdo do evento. A predefinição é "application/octet-stream". |
      | Esquema do conteúdo | Introduza o esquema do conteúdo em JSON para os eventos que são lidos a partir do Hub de eventos. |
      | Nome do grupo de consumidores | Introduza o Hub de eventos [nome do grupo de consumidor](../event-hubs/event-hubs-features.md#consumer-groups) para ler eventos. Se não for especificado, é utilizado o grupo de consumidores predefinido. |
      | Chave de partição mínimo | Introduza o mínimo [partição](../event-hubs/event-hubs-features.md#partitions) ID para ler. Por predefinição, todas as partições são de leitura. |
      | Chave de partição máximo | Introduza o máximo [partição](../event-hubs/event-hubs-features.md#partitions) ID para ler. Por predefinição, todas as partições são de leitura. |
      | Contagem máxima de eventos | Introduza um valor para o número máximo de eventos. O acionador devolver entre um e o número de eventos especificado por esta propriedade. |
      |||

4. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

5. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados de Acionador.

> [!NOTE]
> Todos os acionadores do Hub de eventos são *consulta longa de* acionadores, que significa que, quando um acionador é acionado, o acionador processa todos os eventos e, em seguida, aguarda 30 segundos para mais eventos são apresentados no seu Hub de eventos.
> Se não existem eventos são recebidos em 30 segundos, a execução de Acionador é ignorada. Caso contrário, o acionador continua a ler eventos até que o seu Hub de eventos está vazio.
> A próxima pesquisa de Acionador acontece com base no intervalo de periodicidade que especificou nas propriedades do acionador.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Adicionar uma ação de Hubs de eventos

No Azure Logic Apps, um [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no fluxo de trabalho que se segue um acionador ou outra ação. Neste exemplo, a aplicação lógica começa com um acionador de Hubs de eventos que verifica a existência de novos eventos no seu Hub de eventos.

1. No portal do Azure ou Visual Studio, abra a aplicação lógica no estruturador de aplicações lógicas. Este exemplo utiliza o portal do Azure.

2. Sob o acionador ou ação, escolha **novo passo** > **adicionar uma ação**.

   Para adicionar uma ação entre os passos existentes, mova o rato por cima da seta de ligação. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, escolha **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "os hubs de eventos" como o filtro.
Na lista de ações, selecione a ação que pretende.

   Neste exemplo, selecione a ação: **Hubs de eventos - evento de envio**

   ![Selecione "Dos Hubs de eventos - evento de envio"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Se lhe for pedido para obter detalhes de ligação, [criar a ligação dos Hubs de eventos agora](#create-connection). Em alternativa, se a ligação já existir, fornecer as informações necessárias para a ação.

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | O nome do hub de eventos | Sim | Selecione o Hub de eventos em que pretende enviar o evento |
   | Conteúdo do evento | Não | O conteúdo para o evento que pretende enviar |
   | Propriedades | Não | Os valores para enviar e as propriedades da aplicação |
   ||||

   Por exemplo:

   ![Selecione o nome do Hub de eventos e fornecer o conteúdo do evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ligar ao seu Hub de eventos

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Quando lhe for pedido para obter informações de ligação, forneça estes detalhes:

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | Nome da Ligação | Sim | <*connection-name*> | O nome para criar para a sua ligação |
   | Espaço de nomes dos Event Hubs | Sim | <*event-hubs-namespace*> | Selecione o espaço de nomes de Hubs de eventos que pretende utilizar. |
   |||||  

   Por exemplo:

   ![Criar ligação do Hub de eventos](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Para introduzir manualmente a cadeia de ligação, escolha **introduzir manualmente as informações de ligação**. 
   Saiba mais [como encontrar a cadeia de ligação](#permissions-connection-string).

2. Selecione a política de Hubs de eventos a utilizar, se não estiver selecionado. Escolha **Criar**.

   ![Criar ligação do Hub de eventos, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Após criar a ligação, continue [os Hubs de eventos adicionar acionar](#add-trigger) ou [ação de adicionar os Hubs de eventos](#add-action).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo ficheiro de Swagger do conector, consulte a [página de referência do conector](/connectors/eventhubs/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)