---
title: Ligue-se aos hubs de eventos Azure
description: Criar tarefas automatizadas e fluxos de trabalho que monitorizem e gerem eventos utilizando hubs de eventos Azure e Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 65da8e07c01561577fe7eff449bfc10348c7f277
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716606"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorizar, receber e enviar eventos com Hubs de Eventos do Azure e Azure Logic Apps

Este artigo mostra como pode monitorizar e gerir eventos enviados para [a Azure Event Hubs](../event-hubs/event-hubs-about.md) a partir de dentro de uma aplicação lógica com o conector Azure Event Hubs. Dessa forma, criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para verificação, envio e receção de eventos do Hub de Eventos. Para obter informações técnicas específicas do conector, consulte a referência do [conector Azure Event Hubs](/connectors/eventhubs/) </a> .

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Um [Azure Event Hubs namespace e Event Hub](../event-hubs/event-hubs-create.md)

* A aplicação lógica onde pretende aceder ao seu Centro de Eventos. Para iniciar a sua aplicação lógica com um gatilho Azure Event Hubs, precisa de uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)
Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Verifique permissões e obtenha a cadeia de ligação

Para se certificar de que a sua aplicação lógica pode aceder ao seu Event Hub, verifique as suas permissões e obtenha a cadeia de ligação para o seu espaço de nomes Desempreso De Event Hubs.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá ao seu espaço de *nomes* do Event Hubs, não um Centro de Eventos específico. 

1. No menu namespace, em **Definições,** selecione **Políticas de acesso partilhado**. Em **Sinistros,** verifique se tem permissões **de Gestão** para esse espaço de nome.

   ![Gerir permissões para o seu espaço de nomes do Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Se pretender introduzir manualmente as informações de ligação, obtenha a cadeia de ligação para o seu espaço de nomes 'Event Hubs'.

   1. Em **Política**, escolha **RootManageSharedAccessKey**.

   1. Encontre a corda de ligação da chave primária. Escolha o botão de cópia e guarde o fio de ligação para utilização posterior.

      ![Copy Event Hubs linha de conexão de espaço de nome](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Para confirmar se a sua cadeia de ligação está associada ao espaço de nomes do Event Hubs ou a um centro de eventos específico, certifique-se de que a cadeia de ligação não tem o `EntityPath` parâmetro. Se encontrar este parâmetro, a cadeia de ligação destina-se a uma "entidade" específica do Event Hub e não é a cadeia correta para usar com a sua aplicação lógica.

1. Agora continue com [adicionar um gatilho de Centros de Eventos](#add-trigger) ou Adicionar uma [ação de Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Adicionar o acionador do Hubs de Eventos

Nas Azure Logic Apps, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância lógica de aplicações e começa a executar o fluxo de trabalho da sua aplicação.

Este exemplo mostra como pode iniciar um fluxo de trabalho de aplicações lógicas quando novos eventos são enviados para o seu Centro de Eventos. 

> [!NOTE]
> Todos os gatilhos do Event Hub são gatilhos *de sondagens longas,* o que significa que o gatilho processa todos os eventos e, em seguida, espera 30 segundos por partição para que mais eventos apareçam no seu Event Hub. Então, se o gatilho for configurado com quatro divisórias, este atraso pode levar até dois minutos antes que o gatilho termine de sondar todas as divisórias. Se não forem recebidos eventos dentro deste atraso, o gatilho é ignorado. Caso contrário, o gatilho continua a ler eventos até que o seu Centro de Eventos esteja vazio. A próxima sondagem do gatilho acontece com base no intervalo de recorrência que se especifica nas propriedades do gatilho.

1. No portal Azure ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic Apps Designer. Este exemplo utiliza o portal Azure.

1. Na caixa de pesquisa, introduza "centros de eventos" como filtro. A partir da lista de gatilhos, selecione este gatilho: **Quando os eventos estiverem disponíveis no Event Hub - Event Hubs**

   ![Selecione o gatilho](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Se for solicitado para obter detalhes de ligação, [crie já a sua ligação 'Event Hubs'.](#create-connection) 

1. No gatilho, forneça informações sobre o Centro de Eventos que pretende monitorizar. Para mais propriedades, abra a nova lista **de parâmetros Adicionar.** A seleção de um parâmetro adiciona essa propriedade ao cartão de gatilho.

   ![Propriedades do acionador](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome do Hub de Eventos** | Yes | O nome para o Centro de Eventos que pretende monitorizar |
   | **Tipo de conteúdo** | No | O tipo de conteúdo do evento. A predefinição é `application/octet-stream`. |
   | **Nome do grupo de consumidores** | No | O [nome para o grupo de consumidores Event Hub](../event-hubs/event-hubs-features.md#consumer-groups) para usar para eventos de leitura. Se não for especificado, o grupo de consumidores predefinido é utilizado. |
   | **Contagem máxima de eventos** | No | O número máximo de eventos. O gatilho retorna entre um e o número de eventos especificados por esta propriedade. |
   | **Intervalo** | Yes | Um inteiro positivo que descreve quantas vezes o fluxo de trabalho funciona com base na frequência |
   | **Frequência** | Yes | A unidade de tempo para a recorrência |
   ||||

   **Propriedades adicionais**

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Esquema de conteúdo** | No | O esquema de conteúdo JSON para os eventos a ler no Centro de Eventos. Por exemplo, se especificar o esquema de conteúdo, pode desencadear a aplicação lógica apenas para os eventos que correspondam ao esquema. |
   | **Chave de partição mínima** | No | Introduza o ID de [partição](../event-hubs/event-hubs-features.md#partitions) mínima para ler. Por predefinição, todas as divisórias são lidas. |
   | **Chave de partição máxima** | No | Introduza o ID de [partição](../event-hubs/event-hubs-features.md#partitions) máxima para ler. Por predefinição, todas as divisórias são lidas. |
   | **Fuso horário** | No | Aplica-se apenas quando especifica uma hora de início porque este gatilho não aceita a compensação UTC. Selecione o fuso horário que pretende aplicar. <p>Para obter mais informações, consulte [Criar e executar tarefas e fluxos de trabalho recorrentes com apps Azure Logic](../connectors/connectors-native-recurrence.md). |
   | **Hora de início** | No | Fornecer uma hora de início neste formato: <p>YYYY-MM-DDThh:mm:mm se selecionar um fuso horário<p>-ou-<p>YYYY-MM-DDThh:mm:ssZ se não selecionar um fuso horário<p>Para obter mais informações, consulte [Criar e executar tarefas e fluxos de trabalho recorrentes com apps Azure Logic](../connectors/connectors-native-recurrence.md). |
   ||||

1. Quando terminar, na barra de ferramentas do designer, escolha **Save**.

1. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados do gatilho. 

   Por exemplo, para filtrar eventos com base num valor específico, como uma categoria, pode adicionar uma condição para que a ação do **evento Enviar** envie apenas os eventos que satisfaçam a sua condição. 

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Adicionar a ação do Hubs de Eventos

Nas Azure Logic Apps, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, a aplicação lógica começa com um gatilho de Event Hubs que verifica novos eventos no seu Event Hub.

1. No portal Azure ou Visual Studio, abra a sua aplicação lógica no Logic Apps Designer. Este exemplo utiliza o portal Azure.

1. Sob o gatilho ou ação, escolha **Novo passo**.

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. 
   Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa, introduza "centros de eventos" como filtro.
A partir da lista de ações, selecione esta ação: **Enviar evento - Centros de Eventos**

   ![Selecione ação "Enviar evento"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Se for solicitado para obter detalhes de ligação, [crie já a sua ligação 'Event Hubs'.](#create-connection) 

1. Na ação, forneça informações sobre os eventos que pretende enviar. Para mais propriedades, abra a nova lista **de parâmetros Adicionar.** A seleção de um parâmetro adiciona essa propriedade ao cartão de ação.

   ![Selecione o nome do Centro de Eventos e forneça conteúdo de evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome do Hub de Eventos** | Yes | O Centro de Eventos onde pretende enviar o evento |
   | **Conteúdo** | No | O conteúdo do evento que deseja enviar |
   | **Propriedades** | No | As propriedades e valores da aplicação para enviar |
   | **Chave de partição** | No | O [ID de partição](../event-hubs/event-hubs-features.md#partitions) para onde enviar o evento |
   ||||

   Por exemplo, pode enviar a saída do seu gatilho de Centros de Eventos para outro Centro de Eventos:

   ![Enviar exemplo de evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **Save**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ligue-se ao seu Centro de Eventos

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Quando lhe for solicitada informação de ligação, forneça estes detalhes:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome de conexão** | Yes | <*nome de conexão*> | O nome para criar para a sua ligação |
   | **Espaço de nomes de centros de eventos** | Yes | <*espaço de nome de centros de eventos*> | Selecione o espaço de nomes 'Centros de Eventos' que pretende utilizar. |
   |||||  

   Por exemplo:

   ![Criar ligação de centro de eventos](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Para introduzir manualmente a cadeia de ligação, selecione **instrua manualmente as informações de ligação**. 
   Saiba [como encontrar a sua cadeia de ligação.](#permissions-connection-string)

2. Selecione a política de Centros de Eventos a utilizar, se ainda não selecionada. Escolha **Criar**.

   ![Criar conexão Centro de Eventos, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Depois de criar a sua ligação, continue com o [gatilho add Event Hubs](#add-trigger) ou [add Event Hubs .](#add-action)

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/eventhubs/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
