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
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 882bae14678d8bfff15b35c63c666a20aeee3d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720055"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorizar, receber e enviar eventos com os Hubs de eventos do Azure e Azure Logic Apps

Este artigo mostra como pode monitorizar e gerir eventos enviados para o [os Hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) de dentro de uma aplicação lógica com o conector de Hubs de eventos do Azure. Dessa forma, pode criar aplicações lógicas que automatizam as tarefas e fluxos de trabalho para a verificação, enviar e receber eventos de seu Hub de eventos. Para obter informações técnicas específicas do conector, consulte a [referência do conector de Hubs de eventos](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Um [espaço de nomes de Hubs de eventos do Azure e o Hub de eventos](../event-hubs/event-hubs-create.md)

* A aplicação de lógica onde pretende aceder ao seu Hub de eventos. Para começar a sua aplicação lógica com um acionador do Event Hubs do Azure, tem um [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Verifique as permissões e obter cadeia de ligação

Para a sua aplicação lógica aceder ao seu Hub de eventos, verifique as suas permissões e obter a cadeia de ligação para o espaço de nomes de Hubs de eventos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá para os Hubs de eventos *espaço de nomes*, não é um Hub de eventos específico. 

1. No menu do espaço de nomes, sob **configurações**, selecione **políticas de acesso partilhado**. Sob **afirmações**, verifique se tem **gerir** permissões para esse espaço de nomes.

   ![Gerir permissões para o espaço de nomes do Hub de eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Se quiser manualmente mais tarde, introduza as informações da ligação, obtenha a cadeia de ligação para o espaço de nomes de Hubs de eventos.

   1. Sob **diretiva**, escolha **RootManageSharedAccessKey**.

   1. Localize a cadeia de ligação da chave primária. Escolha o botão de cópia e guarde a cadeia de ligação para utilização posterior.

      ![Copie a cadeia de ligação do espaço de nomes de Hubs de eventos](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Para confirmar se a cadeia de ligação está associada com o seu espaço de nomes de Hubs de eventos ou com um hub de eventos específico, certifique-se a cadeia de ligação não tem o `EntityPath`  parâmetro. Se encontrar este parâmetro, a cadeia de ligação é para um Hub de eventos específico "entidade" e não é a cadeia de caracteres correta para utilizar com a sua aplicação lógica.

1. Agora avance [adicionar um acionador de Hubs de eventos](#add-trigger) ou [adicionar uma ação de Hubs de eventos](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Adicionar acionador de Hubs de eventos

No Azure Logic Apps, todas as aplicações lógicas têm de começar com uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico acontece ou quando for cumprida uma condição específica. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica e começa a ser executado o fluxo de trabalho da sua aplicação.

Este exemplo mostra como pode iniciar um fluxo de trabalho de aplicação lógica quando novos eventos são enviados para o Hub de eventos. 

1. O portal do Azure ou o Visual Studio, crie uma aplicação lógica em branco, que abre o estruturador de aplicações lógicas. Este exemplo utiliza o portal do Azure.

1. Na caixa de pesquisa, introduza "os hubs de eventos" como o filtro. Na lista de disparadores, selecione este acionador: **Quando eventos estão disponíveis no Hub de eventos - Hubs de eventos**

   ![Selecionar acionador](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Se lhe for pedido para obter detalhes de ligação, [criar a ligação dos Hubs de eventos agora](#create-connection). 

1. No acionador, fornecem informações sobre o Hub de eventos que pretende monitorizar. Para mais propriedades, abra a **adicione o novo parâmetro** lista. Selecionar um parâmetro adiciona essa propriedade para o cartão do acionador.

   ![Propriedades do acionador](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome do Hub de eventos** | Sim | O nome para o Hub de eventos que pretende monitorizar |
   | **Tipo de Conteúdo** | Não | Tipo de conteúdo do evento. A predefinição é `application/octet-stream`. |
   | **Nome do grupo de consumidores** | Não | O [nome para o grupo de consumidores do Hub de eventos](../event-hubs/event-hubs-features.md#consumer-groups) a utilizar para ler eventos. Se não for especificado, é utilizado o grupo de consumidores predefinido. |
   | **Contagem máxima de eventos** | Não | O número máximo de eventos. O acionador devolver entre um e o número de eventos especificado por esta propriedade. |
   | **Intervalo** | Sim | Um número inteiro que descreve a frequência com que o fluxo de trabalho é executada com base na frequência |
   | **Frequência** | Sim | A unidade de tempo da periodicidade |
   ||||

   **Propriedades adicionais**

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Esquema do conteúdo** | Não | O esquema do conteúdo JSON para os eventos ler a partir do Hub de eventos. Por exemplo, se especificar o esquema do conteúdo, pode acionar a aplicação lógica para apenas os eventos que corresponde ao esquema. |
   | **Chave de partição mínimo** | Não | Introduza o mínimo [partição](../event-hubs/event-hubs-features.md#partitions) ID para ler. Por predefinição, todas as partições são de leitura. |
   | **Chave de partição máximo** | Não | Introduza o máximo [partição](../event-hubs/event-hubs-features.md#partitions) ID para ler. Por predefinição, todas as partições são de leitura. |
   | **Fuso horário** | Não | Aplica-se apenas quando especificar uma hora de início, porque este acionador não aceita a compensação UTC. Selecione o fuso horário que pretende aplicar. <p>Para obter mais informações, consulte [criar e executadas tarefas recorrentes e fluxos de trabalho com o Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Start time** (Hora de início) | Não | Forneça uma hora de início no seguinte formato: <p>AAAA-MM-ddTHH se selecionar um fuso horário<p>-ou-<p>AAAA-MM-: ssZ se não selecionar um fuso horário<p>Para obter mais informações, consulte [criar e executadas tarefas recorrentes e fluxos de trabalho com o Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

1. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados de Acionador. 

   Por exemplo, para filtrar eventos com base num valor específico, por exemplo, uma categoria, pode adicionar uma condição para que o **enviar event - Hubs de eventos** ação envia apenas os eventos que cumprem a condição. 

> [!NOTE]
> Todos os acionadores do Hub de eventos são *consulta longa de* acionadores, que significa que, quando um acionador é acionado, o acionador processa todos os eventos e, em seguida, aguarda 30 segundos para mais eventos são apresentados no seu Hub de eventos.
> Se não existem eventos são recebidos em 30 segundos, a execução de Acionador é ignorada. Caso contrário, o acionador continua a ler eventos até que o seu Hub de eventos está vazio.
> A próxima pesquisa de Acionador acontece com base no intervalo de periodicidade que especificou nas propriedades do acionador.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Adicionar ação de Hubs de eventos

No Azure Logic Apps, um [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no fluxo de trabalho que se segue um acionador ou outra ação. Neste exemplo, a aplicação lógica começa com um acionador de Hubs de eventos que verifica a existência de novos eventos no seu Hub de eventos.

1. No portal do Azure ou Visual Studio, abra a aplicação lógica no estruturador de aplicações lógicas. Este exemplo utiliza o portal do Azure.

1. Sob o acionador ou ação, escolha **novo passo**.

   Para adicionar uma ação entre os passos existentes, mova o rato por cima da seta de ligação. 
   Selecione o sinal de adição ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Na caixa de pesquisa, introduza "os hubs de eventos" como o filtro.
Na lista de ações, selecione a ação: **Enviar event - Hubs de eventos**

   ![Selecione a ação "Enviar eventos"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Se lhe for pedido para obter detalhes de ligação, [criar a ligação dos Hubs de eventos agora](#create-connection). 

1. A ação, fornece informações sobre os eventos que deseja enviar. Para mais propriedades, abra a **adicione o novo parâmetro** lista. Selecionar um parâmetro adiciona essa propriedade para o cartão de ação.

   ![Selecione o nome do Hub de eventos e fornecer o conteúdo do evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome do Hub de eventos** | Sim | O Hub de eventos em que pretende enviar o evento |
   | **Conteúdo** | Não | O conteúdo para o evento que pretende enviar |
   | **Propriedades** | Não | Os valores para enviar e as propriedades da aplicação |
   | **Chave de partição** | Não | O [partição](../event-hubs/event-hubs-features.md#partitions) ID para onde enviar o evento |
   ||||

   Por exemplo, pode enviar a saída de Acionador de Hubs de eventos para outra Hub de eventos:

   ![Enviar o exemplo de evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ligar ao seu Hub de eventos

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Quando lhe for pedido para obter informações de ligação, forneça estes detalhes:

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da Ligação** | Sim | <*connection-name*> | O nome para criar para a sua ligação |
   | **Espaço de nomes de Hubs de eventos** | Sim | <*event-hubs-namespace*> | Selecione o espaço de nomes de Hubs de eventos que pretende utilizar. |
   |||||  

   Por exemplo:

   ![Criar ligação do Hub de eventos](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Para introduzir manualmente a cadeia de ligação, escolha **introduzir manualmente as informações de ligação**. 
   Saiba mais [como encontrar a cadeia de ligação](#permissions-connection-string).

2. Selecione a política de Hubs de eventos a utilizar, se não estiver selecionado. Selecione **Criar**.

   ![Criar ligação do Hub de eventos, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Após criar a ligação, continue [os Hubs de eventos adicionar acionar](#add-trigger) ou [ação de adicionar os Hubs de eventos](#add-action).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente Swagger) de ficheiros, consulte a [página de referência do conector](/connectors/eventhubs/).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)