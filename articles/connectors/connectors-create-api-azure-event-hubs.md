---
title: Ligue-se aos Centros de Eventos Azure
description: Criar tarefas automatizadas e fluxos de trabalho que monitorizam e gerem eventos utilizando hubs de eventos azure e aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 32fa54ef0d8eccaf8745ee37cb028d4f3c6d73eb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247296"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorize, receba e envie eventos com Hubs de Eventos Azure e Aplicações Lógicas Azure

Este artigo mostra como pode monitorizar e gerir eventos enviados para [o Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) a partir de dentro de uma aplicação lógica com o conector Azure Event Hubs. Dessa forma, criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para verificação, envio e receção de eventos do Hub de Eventos. Para obter informações técnicas específicas do conector, consulte o [conector Azure Event Hubs](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Um [espaço de nome azure Event Hubs e Hub de Eventos](../event-hubs/event-hubs-create.md)

* A aplicação lógica onde pretende aceder ao seu Hub de Eventos. Para iniciar a sua aplicação lógica com um gatilho do Azure Event Hubs, você precisa de uma [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Verifique permissões e obtenha fio de ligação

Para garantir que a sua aplicação lógica pode aceder ao seu Hub de Eventos, verifique as suas permissões e obtenha a cadeia de ligação para o seu espaço de nome Sem Evento.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá ao espaço de *nome*do Seu Event Hubs, não a um Hub de Eventos específico. 

1. No menu espaço de nome, em **Definições,** selecione políticas de **acesso partilhado**. Em **Sinistros,** verifique se tem permissões **de gestão** para esse espaço de nome.

   ![Gerencie permissões para o seu espaço de nome do Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Se pretender introduzir manualmente as suas informações de ligação, obtenha a cadeia de ligação para o seu espaço de nome Sem Nome do Event Hubs.

   1. Em **Política,** escolha **RootManageSharedAccessKey**.

   1. Encontre a corda de ligação da chave principal. Escolha o botão de cópia e guarde a corda de ligação para posterior utilização.

      ![Copy Event Hubs nomespace string](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Para confirmar se a sua cadeia de ligação está associada ao seu espaço de nome sem nome do Event Hubs ou a um centro de eventos específico, certifique-se de que a cadeia de ligação não tem o `EntityPath` parâmetro. Se encontrar este parâmetro, a cadeia de ligação é para uma "entidade" específica do Event Hub e não é a corda correta para usar com a sua aplicação lógica.

1. Agora continue com [adicionar um gatilho de Hubs](#add-trigger) de eventoou adicionar uma ação de [Hubs](#add-action)de Evento .

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Adicionar gatilho de Hubs de Eventos

Nas Aplicações Lógicas Do Azure, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é satisfeita. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância de aplicação lógica e começa a executar o fluxo de trabalho da sua aplicação.

Este exemplo mostra como pode iniciar um fluxo de trabalho de aplicações lógicas quando novos eventos são enviados para o seu Hub de Eventos. 

1. No portal Azure ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic Apps Designer. Este exemplo utiliza o portal Azure.

1. Na caixa de pesquisa, introduza "centros de eventos" como filtro. A partir da lista de gatilhos, selecione este gatilho: **Quando os eventos estiverem disponíveis no Event Hub - Event Hubs**

   ![Selecione gatilho](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Se for solicitado para obter detalhes de ligação, crie agora a [sua ligação de Hubs](#create-connection)de Eventos . 

1. No gatilho, forneça informações sobre o Centro de Eventos que pretende monitorizar. Para mais propriedades, abra a **lista adicionar novo parâmetro.** A seleção de um parâmetro adiciona essa propriedade ao cartão de gatilho.

   ![Propriedades do acionador](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome do Hub do Evento** | Sim | O nome para o Hub de Eventos que você quer monitorizar |
   | **Tipo de Conteúdo** | Não | O tipo de conteúdo do evento. A predefinição é `application/octet-stream`. |
   | **Nome do grupo de consumidores** | Não | O [nome para o grupo de consumidores Event Hub](../event-hubs/event-hubs-features.md#consumer-groups) usar para ler eventos. Se não for especificado, o grupo de consumidores por defeito é utilizado. |
   | **Contagem máxima de eventos** | Não | O número máximo de eventos. O gatilho retorna entre um e o número de eventos especificados por esta propriedade. |
   | **Intervalo** | Sim | Um inteiro positivo que descreve quantas vezes o fluxo de trabalho funciona com base na frequência |
   | **Frequência** | Sim | A unidade de tempo para a recorrência |
   ||||

   **Propriedades adicionais**

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Esquema de conteúdo** | Não | O esquema de conteúdo jSON para os eventos para ler a partir do Event Hub. Por exemplo, se especificar o esquema de conteúdo, pode desencadear a aplicação lógica apenas para aqueles eventos que correspondam ao esquema. |
   | **Chave mínima de partição** | Não | Introduza o ID mínimo de [partição](../event-hubs/event-hubs-features.md#partitions) para ler. Por padrão, todas as divisórias são lidas. |
   | **Chave máxima de partição** | Não | Introduza o ID máximo [de partição](../event-hubs/event-hubs-features.md#partitions) para ler. Por padrão, todas as divisórias são lidas. |
   | **Time zone** (Fuso horário) | Não | Aplica-se apenas quando especifica um tempo de início porque este gatilho não aceita a compensação utc. Selecione o fuso horário que pretende aplicar. <p>Para mais informações, consulte [Criar e executar tarefas e fluxos de trabalho recorrentes com aplicações lógicas azure](../connectors/connectors-native-recurrence.md). |
   | **Start time** (Hora de início) | Não | Proporcionar um tempo de início neste formato: <p>YYYY-MM-DDThh:mm:ss se selecionar um fuso horário<p>-ou-<p>YYYY-MM-DDThh:mm:ssZ se não selecionar um fuso horário<p>Para mais informações, consulte [Criar e executar tarefas e fluxos de trabalho recorrentes com aplicações lógicas azure](../connectors/connectors-native-recurrence.md). |
   ||||

1. Quando terminar, na barra de ferramentas de design, escolha **Save**.

1. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados do gatilho. 

   Por exemplo, para filtrar eventos com base num valor específico, como uma categoria, pode adicionar uma condição para que a ação do **evento Enviar** envie apenas os eventos que cumprem a sua condição. 

> [!NOTE]
> Todos os gatilhos do Event Hub são gatilhos *de sondagens longas,* o que significa que quando um gatilho dispara, o gatilho processa todos os eventos e espera por 30 segundos para que mais eventos apareçam no seu Centro de Eventos.
> Se não forem recebidos eventos em 30 segundos, o gatilho é ignorado. Caso contrário, o gatilho continua a ler eventos até que o seu Centro de Eventos esteja vazio.
> A próxima sondagem de gatilho acontece com base no intervalo de recorrência que especifica nas propriedades do gatilho.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Adicionar ação de Hubs de Eventos

Nas Aplicações Lógicas Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, a aplicação lógica começa com um gatilho do Event Hubs que verifica novos eventos no seu Event Hub.

1. No portal Azure ou No Estúdio Visual, abra a sua aplicação lógica no Logic Apps Designer. Este exemplo utiliza o portal Azure.

1. Sob o gatilho ou ação, escolha **novo passo**.

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. 
   Escolha o sinal plus **(+)** que aparece e, em seguida, **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, introduza "centros de eventos" como filtro.
Da lista de ações, selecione esta ação: **Enviar evento - Hubs de Eventos**

   ![Selecione ação "Enviar evento"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Se for solicitado para obter detalhes de ligação, crie agora a [sua ligação de Hubs](#create-connection)de Eventos . 

1. Na ação, forneça informações sobre os eventos que pretende enviar. Para mais propriedades, abra a **lista adicionar novo parâmetro.** A seleção de um parâmetro adiciona essa propriedade ao cartão de ação.

   ![Selecione o nome do Event Hub e forneça conteúdo do evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome do Hub do Evento** | Sim | O Hub do Evento onde pretende enviar o evento |
   | **Conteúdo** | Não | O conteúdo do evento que pretende enviar |
   | **Propriedades** | Não | As propriedades e valores da aplicação para enviar |
   | **Chave de partição** | Não | O ID [de partição](../event-hubs/event-hubs-features.md#partitions) para onde enviar o evento |
   ||||

   Por exemplo, pode enviar a saída do gatilho dos seus Centros de Eventos para outro Hub de Eventos:

   ![Enviar exemplo de evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Quando terminar, na barra de ferramentas de design, escolha **Save**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ligue-se ao seu Hub de Eventos

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Quando for solicitado informações sobre ligação, forneça estes detalhes:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da Ligação** | Sim | < *> de nome de ligação* | O nome a criar para a sua ligação |
   | **Espaço de nome de Hubs de Eventos** | Sim | <espaço *de nome de centros de eventos*> | Selecione o espaço de nome seletiva do Event Hubs que pretende utilizar. |
   |||||  

   Por exemplo:

   ![Criar conexão Hub de Eventos](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Para introduzir manualmente a cadeia de ligação, **selecione manualmente introduzir informações**de ligação . 
   Aprenda a encontrar a sua corda de [ligação.](#permissions-connection-string)

2. Selecione a política de Hubs de Eventos para usar, se ainda não for selecionada. Escolha **Criar**.

   ![Criar conexão Event Hub, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Depois de criar a sua ligação, continue com [add Event Hubs gatilho](#add-trigger) ou adicionar ação de Hubs de [Eventos](#add-action).

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/eventhubs/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)