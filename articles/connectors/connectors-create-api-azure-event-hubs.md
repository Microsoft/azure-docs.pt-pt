---
title: Conectar-se aos hubs de eventos do Azure
description: Gerenciar e monitorar eventos com hubs de eventos do Azure e aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 77ff24f3af77e012b9ae9bc702d6a5a2639a5b11
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789931"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorar, receber e enviar eventos com hubs de eventos do Azure e aplicativos lógicos do Azure

Este artigo mostra como você pode monitorar e gerenciar eventos enviados aos [hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) de dentro de um aplicativo lógico com o conector de hubs de eventos do Azure. Dessa forma, criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para verificação, envio e receção de eventos do Hub de Eventos. Para obter informações técnicas específicas do conector, consulte a [referência do conector dos hubs de eventos do Azure](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Um [namespace de hubs de eventos do Azure e um hub de eventos](../event-hubs/event-hubs-create.md)

* O aplicativo lógico no qual você deseja acessar o Hub de eventos. Para iniciar seu aplicativo lógico com um gatilho de hubs de eventos do Azure, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se você for novo em aplicativos lógicos, examine [o que é o início rápido e aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) [: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Verificar permissões e obter cadeia de conexão

Para garantir que seu aplicativo lógico possa acessar seu hub de eventos, verifique suas permissões e obtenha a cadeia de conexão para seu namespace de hubs de eventos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá para seu *namespace*de hubs de eventos, não para um hub de eventos específico. 

1. No menu namespace, em **configurações**, selecione **políticas de acesso compartilhado**. Em **declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace.

   ![Gerenciar permissões para seu namespace do hub de eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Se você quiser inserir manualmente as informações de conexão, obtenha a cadeia de conexão para seu namespace de hubs de eventos.

   1. Em **política**, escolha **RootManageSharedAccessKey**.

   1. Localize a cadeia de conexão da sua chave primária. Escolha o botão Copiar e salve a cadeia de conexão para uso posterior.

      ![Copiar cadeia de conexão do namespace de hubs de eventos](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Para confirmar se a cadeia de conexão está associada ao namespace de seus hubs de eventos ou a um hub de eventos específico, verifique se a cadeia de conexão não tem o parâmetro `EntityPath` . Se você encontrar esse parâmetro, a cadeia de conexão será para uma "entidade" específica do hub de eventos e não será a cadeia de caracteres correta a ser usada com seu aplicativo lógico.

1. Agora, continue com [Adicionar um gatilho de hubs de eventos](#add-trigger) ou [Adicionar uma ação de hubs de eventos](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Adicionar gatilho de hubs de eventos

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico quando novos eventos são enviados ao seu hub de eventos. 

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer de aplicativos lógicos. Este exemplo usa o portal do Azure.

1. Na caixa de pesquisa, insira "hubs de eventos" como filtro. Na lista de gatilhos, selecione este gatilho: **quando os eventos estão disponíveis no Hub de eventos-hubs de eventos**

   ![Selecionar gatilho](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão de hubs de eventos agora](#create-connection). 

1. No gatilho, forneça informações sobre o Hub de eventos que você deseja monitorar. Para obter mais propriedades, abra a lista **Adicionar novo parâmetro** . A seleção de um parâmetro adiciona essa propriedade ao cartão de gatilho.

   ![Propriedades do acionador](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome do hub de eventos** | Sim | O nome do hub de eventos que você deseja monitorar |
   | **Tipo de Conteúdo** | Não | O tipo de conteúdo do evento. A predefinição é `application/octet-stream`. |
   | **Nome do grupo de consumidores** | Não | O [nome do grupo de consumidores do hub de eventos](../event-hubs/event-hubs-features.md#consumer-groups) a ser usado para ler eventos. Se não for especificado, o grupo de consumidores padrão será usado. |
   | **Contagem máxima de eventos** | Não | O número máximo de eventos. O gatilho retorna entre um e o número de eventos especificados por essa propriedade. |
   | **Intervalo** | Sim | Um inteiro positivo que descreve com que frequência o fluxo de trabalho é executado com base na frequência |
   | **Frequência** | Sim | A unidade de tempo para a recorrência |
   ||||

   **Propriedades adicionais**

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Esquema de conteúdo** | Não | O esquema de conteúdo JSON para os eventos a serem lidos do hub de eventos. Por exemplo, se você especificar o esquema de conteúdo, poderá disparar o aplicativo lógico somente para os eventos que correspondam ao esquema. |
   | **Chave de partição mínima** | Não | Insira a ID de [partição](../event-hubs/event-hubs-features.md#partitions) mínima a ser lida. Por padrão, todas as partições são lidas. |
   | **Chave de partição máxima** | Não | Insira a ID de [partição](../event-hubs/event-hubs-features.md#partitions) máxima a ser lida. Por padrão, todas as partições são lidas. |
   | **Time zone** (Fuso horário) | Não | Aplica-se somente quando você especifica uma hora de início porque esse gatilho não aceita o deslocamento UTC. Selecione o fuso horário que você deseja aplicar. <p>Para obter mais informações, consulte [criar e executar tarefas e fluxos de trabalho recorrentes com o aplicativo lógico do Azure](../connectors/connectors-native-recurrence.md). |
   | **Start time** (Hora de início) | Não | Forneça uma hora de início neste formato: <p>AAAA-MM-DDThh: mm: SS se você selecionar um fuso horário<p>-ou-<p>AAAA-MM-DDThh: mm: ssZ se você não selecionar um fuso horário<p>Para obter mais informações, consulte [criar e executar tarefas e fluxos de trabalho recorrentes com o aplicativo lógico do Azure](../connectors/connectors-native-recurrence.md). |
   ||||

1. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

1. Agora, continue adicionando uma ou mais ações ao seu aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho. 

   Por exemplo, para filtrar eventos com base em um valor específico, como uma categoria, você pode adicionar uma condição para que a ação **enviar evento** envie somente os eventos que atendem à sua condição. 

> [!NOTE]
> Todos os gatilhos de Hub de eventos são gatilhos *de sondagem longa* , o que significa que quando um gatilho é acionado, o gatilho processa todos os eventos e aguarda 30 segundos para que mais eventos apareçam em seu hub de eventos.
> Se nenhum evento for recebido em 30 segundos, a execução do gatilho será ignorada. Caso contrário, o gatilho continuará lendo eventos até que seu hub de eventos esteja vazio.
> A sondagem do gatilho seguinte ocorre com base no intervalo de recorrência especificado nas propriedades do gatilho.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Adicionar ação de hubs de eventos

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, o aplicativo lógico começa com um gatilho de hubs de eventos que verifica se há novos eventos em seu hub de eventos.

1. No portal do Azure ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativos lógicos. Este exemplo usa o portal do Azure.

1. No gatilho ou ação, escolha **nova etapa**.

   Para adicionar uma ação entre as etapas existentes, mova o mouse sobre a seta de conexão. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "hubs de eventos" como filtro.
Na lista ações, selecione esta ação: **enviar evento-hubs de eventos**

   ![Selecione a ação "enviar evento"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão de hubs de eventos agora](#create-connection). 

1. Na ação, forneça informações sobre os eventos que você deseja enviar. Para obter mais propriedades, abra a lista **Adicionar novo parâmetro** . A seleção de um parâmetro adiciona essa propriedade ao cartão de ação.

   ![Selecione o nome do hub de eventos e forneça o conteúdo do evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome do hub de eventos** | Sim | O Hub de eventos para o qual você deseja enviar o evento |
   | **Conteúdo** | Não | O conteúdo do evento que você deseja enviar |
   | **Properties** | Não | As propriedades e os valores do aplicativo a serem enviados |
   | **Chave de partição** | Não | A ID da [partição](../event-hubs/event-hubs-features.md#partitions) para onde enviar o evento |
   ||||

   Por exemplo, você pode enviar a saída de seu gatilho de hubs de eventos para outro hub de eventos:

   ![Exemplo de evento Send](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Conectar-se ao seu hub de eventos

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Quando as informações de conexão forem solicitadas, forneça estes detalhes:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da Ligação** | Sim | <*nome da conexão*> | O nome a ser criado para a conexão |
   | **Namespace de hubs de eventos** | Sim | <o *namespace de hubs de eventos*> | Selecione o namespace de hubs de eventos que você deseja usar. |
   |||||  

   Por exemplo:

   ![Criar conexão do hub de eventos](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Para inserir manualmente a cadeia de conexão, selecione **inserir manualmente as informações de conexão**. 
   Saiba [como localizar a cadeia de conexão](#permissions-connection-string).

2. Selecione a política de hubs de eventos a ser usada, se ainda não estiver selecionada. Escolha **Criar**.

   ![Criar conexão de Hub de eventos, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Depois de criar a conexão, continue com a ação [Adicionar gatilho de hubs de eventos](#add-trigger) ou [Adicionar hubs](#add-action)de eventos.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/eventhubs/).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)