---
title: 'Tutorial: Capturar eventos a partir de um espaço de duplos Digital do Azure | Documentos da Microsoft'
description: Saiba como receber notificações dos seus espaços mediante a integração do Azure Digital Twins no Logic Apps através dos passos descritos neste tutorial.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: dkshir
ms.openlocfilehash: 524ca96687e9395b65ec513326ad0fd4f7c6d429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57528911"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Tutorial: Receber notificações do seus espaços duplos Digital do Azure ao utilizar o Logic Apps

Depois de implementar a sua instância de duplos Digital do Azure, aprovisionar os seus espaços e implementar funções personalizadas para monitorizar a condições específicas, pode notificar o administrador do office por e-mail quando ocorrem as condições monitorizadas.

Na [o primeiro tutorial](tutorial-facilities-setup.md), configurou o gráfico geográfico de um edifício imaginário. Uma sala no prédio contém sensores de temperatura, dióxido de carbono e movimento. Na [o segundo tutorial](tutorial-facilities-udf.md), aprovisionou o gráfico e uma função definida pelo utilizador para monitorizar estes valores de sensor e acionem notificações quando o espaço está vazio e a temperatura e as emissões de dióxido de carbono estão num intervalo de conforto. 

Este tutorial mostra-lhe como pode integrar essas notificações no Azure Logic Apps para enviar e-mails quando uma divisão que cumpra estes requisitos esteja disponível. Os administradores dos escritórios podem utilizar estas informações para ajudar os funcionários a reservar a sala de reuniões mais produtiva.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Integre eventos com o Azure Event Grid.
> * Notificar os eventos com o Logic Apps.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, parte-se do princípio de que [configurou](tutorial-facilities-setup.md) e [aprovisionou](tutorial-facilities-udf.md) a sua configuração do Azure Digital Twins. Antes de avançar, confirme que tem:

- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância do Digital Twins em execução.
- Os [exemplos do Digital Twins em C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) transferidos e extraídos para o computador de trabalho.
- [.NET core SDK versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) no computador de desenvolvimento para executar o exemplo. Executar `dotnet --version` para verificar se a versão correta está instalada.
- Uma conta do Office 365 para enviar e-mails de notificação.

## <a name="integrate-events-with-event-grid"></a>Integrar eventos com o Event Grid

Nesta secção, configurou [Event Grid](../event-grid/overview.md) para recolher eventos de sua instância de duplos Digital do Azure e redirecioná-los para um [manipulador de eventos](../event-grid/event-handlers.md) como o Logic Apps.

### <a name="create-an-event-grid-topic"></a>Criar um tópico do event grid

Uma [tópico do event grid](../event-grid/concepts.md#topics) fornece uma interface para encaminhar os eventos gerados pela função definida pelo utilizador. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**. 

1. Procure e selecione **Event Grid Topic** (Tópico do Event Grid). Selecione **Criar**.

1. Introduza **Name** (Nome) do tópico do Event Grid e escolha **Subscrição**. Selecione o **grupo de recursos** utilizados ou criado para a sua instância de duplos Digital e o **localização**. Selecione **Criar**. 

    ![Criar um tópico do event grid](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Navegue para o tópico do event grid, do grupo de recursos, selecione **descrição geral**e copie o valor para **ponto final do tópico** num arquivo temporário. Precisará este URL na secção seguinte. 

1. Selecione **chaves de acesso**e copie **YOUR_KEY_1** e **YOUR_KEY_2** num arquivo temporário. Precisará estes valores para criar o ponto final na secção seguinte.

    ![Chaves de grelha de eventos](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Criar um ponto final para o tópico do event grid

1. Na janela de comando, certifique-se de que está na **ocupação quickstart\src** pasta do exemplo duplos Digital.

1. Abra o ficheiro **actions\createEndpoints.yaml** no seu editor de código do Visual Studio. Verifique se os conteúdos seguintes estão presentes:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Substitua o marcador de posição `Primary_connection_string_for_your_Event_Grid` com o valor de **YOUR_KEY_1**.

1. Substitua o marcador de posição `Secondary_connection_string_for_your_Event_Grid` com o valor de **YOUR_KEY_2**.

1. Substitua o marcador de posição `Event_Grid_Topic_Path` pelo caminho do tópico do Event Grid. Obter esse caminho, removendo **https://** e os caminhos de recursos à direita da **ponto final do tópico** URL. Deverá ser semelhante ao formato *NomedoEventGrid.asuaLocalização.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Introduza todos os valores, sem aspas. Certificar-se de que existe caráter de espaço, pelo menos, uma após a vírgula no ficheiro YAML. Também pode validar o seu conteúdo do ficheiro YAML, utilizando qualquer validador YAML online, tal como [essa ferramenta](https://onlineyamltools.com/validate-yaml).

1. Guarde e feche o ficheiro. Na janela de comandos, execute o comando seguinte e inicie sessão quando lhe for pedido. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Este comando cria o ponto final do Event Grid. 

   ![Pontos finais do Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)

## <a name="notify-events-with-logic-apps"></a>Notificar os eventos com o Logic Apps

Pode utilizar o [do Azure Logic Apps](../logic-apps/logic-apps-overview.md) serviço para criar tarefas automatizadas para eventos recebidos de outros serviços. Nesta secção, vai configurar Logic Apps para criar notificações de e-mail para eventos encaminhados a partir de seu sensores espaciais, com a ajuda de um [tópico do event grid](../event-grid/overview.md).

1. No painel esquerdo dos [portal do Azure](https://portal.azure.com), selecione **criar um recurso**.

1. Procure e selecione o recurso **Logic App**. Selecione **Criar**.

1. Introduza um **Name** para o seu recurso da aplicação lógica e, em seguida, selecione seu **subscrição**, **grupo de recursos**, e **localização**. Selecione **Criar**.

    ![Criar um recurso do Logic Apps](./media/tutorial-facilities-events/create-logic-app.png)

1. Abra o seu recurso do Logic Apps quando é implementada e, em seguida, abra a **Estruturador da aplicação lógica** painel. 

1. Selecione o acionador **When an Event Grid event occurs** (Quando ocorrer um evento do Event Grid). Inicie sessão no seu inquilino com a sua conta do Azure quando lhe for pedido. Selecione **permitir o acesso** para o seu recurso do Event Grid quando lhe for pedido. Selecione **Continuar**.

1. Na **ocorrência de um evento do recurso (pré-visualização)** janela: 
   
   a. Selecione o **subscrição** que utilizou para criar o tópico do event grid.

   b. Selecione **Microsoft.EventGrid.Topics** para **tipo de recurso**.

   c. Selecione o seu recurso do Event Grid na caixa pendente para **nome do recurso**.

   ![Painel de Estruturador da aplicação lógica](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Selecione o **novo passo** botão.

1. Na **escolher uma ação** janela:

   a. Procure a expressão **parse json** (analisar json) e selecione a ação **Parse JSON**.

   b. Na **conteúdo** campo, selecione **corpo** partir o **conteúdo dinâmico** lista.

   c. Selecione **exemplo de utilização para o payload para gerar esquema**. Cole o payload JSON seguinte e, em seguida, selecione **feito**.

    ```JSON
    {
    "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
      "ResourceType": "Space",
      "Payload": "\"Air quality is poor.\"",
      "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
    },
    "eventType": "UdfCustom",
    "eventTime": "0001-01-01T00:00:00Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
    }
    ```

    Este payload tem valores fictícios. O Logic Apps utiliza este payload de exemplo para gerar um *esquema*.

    ![Janela de aplicações Parse JSON de lógica para o Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Selecione o **novo passo** botão.

1. Na **escolher uma ação** janela:

   a. Selecione **controle > condição** ou pesquisa **condição** partir o **ações** lista. 

   b. Na primeira **escolher um valor** caixa de texto, selecione **eventType** partir o **conteúdo dinâmico** listar para o **Parse JSON** janela.

   c. Na segunda **escolher um valor** texto, digite `UdfCustom`.

   ![Condições selecionadas](./media/tutorial-facilities-events/logic-app-condition.png)

1. Na **se for verdadeiro** janela:

   a. Selecione **adicionar uma ação**e selecione **Outlook do Office 365**.

   b. Partir do **ações** lista, selecione **enviar um e-mail**. Selecione **iniciar sessão** e utilize as suas credenciais de conta de e-mail. Selecione **permitir o acesso** quando lhe for pedido.

   c. Na caixa **To** (Para), introduza o ID do seu e-mail para receber notificações. Na **assunto**, introduza o texto **notificação duplos Digital para a qualidade de ar fraco no espaço**. Em seguida, selecione **TopologyObjectId** partir do **conteúdo dinâmico** listar para **Parse JSON**.

   d. Sob **corpo** na mesma janela, introduza texto semelhante ao seguinte: **Qualidade de ar fraco detetado numa sala e temperatura tem de ser ajustado**. Fique à vontade poder elaborar, utilizando elementos do **conteúdo dinâmico** lista.

   ![Seleções de "Enviar e-mail" do Logic Apps](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Selecione o **salvar** botão na parte superior a **Estruturador da aplicação lógica** painel.

1. Certifique-se simular os dados de sensor ao navegar para o **conectividade do dispositivo** pasta do exemplo duplos Digital na janela de comando e, em execução `dotnet run`.

Dentro de alguns minutos, deve começar a obter notificações por correio eletrónico deste recurso do Logic Apps. 

   ![Notificação de e-mail](./media/tutorial-facilities-events/logic-app-notification.png)

Para parar o recebimento destes emails, aceda ao seu recurso do Logic Apps no portal e selecione o **descrição geral** painel. Selecione **desativar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender parar a explorar duplos Digital do Azure neste momento, fique à vontade eliminar recursos criados neste tutorial:

1. No menu do lado esquerdo da [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, selecione o grupo de recursos digitais duplos e selecione **eliminar**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

2. Se necessário, elimine os aplicativos de exemplo no seu computador de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Para saber como visualizar os seus dados de sensor, analisar tendências e detetar anomalias, vá para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Tutorial: Visualize e analise os eventos a partir de seus espaços duplos Digital do Azure com o Time Series Insights](tutorial-facilities-analyze.md)

Também pode saber mais sobre os gráficos de inteligência geográficos e os modelos de objeto no duplos Digital do Azure:

> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Compreender os modelos de objetos e o gráfico de inteligência espacial do Digital Twins)
