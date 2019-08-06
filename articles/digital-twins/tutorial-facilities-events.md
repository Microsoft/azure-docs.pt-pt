---
title: 'Tutorial: Capturar eventos de um espaço de gêmeos digital do Azure | Microsoft Docs'
description: Saiba como receber notificações dos seus espaços mediante a integração do Azure Digital Twins no Logic Apps através dos passos descritos neste tutorial.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: alinast
ms.openlocfilehash: 30d43831b73edc52b461512faecac369f6bf00b0
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827826"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Tutorial: Receber notificações de seus espaços de gêmeos digitais do Azure usando aplicativos lógicos

Depois de implantar sua instância do gêmeos digital do Azure, provisionar seus espaços e implementar funções personalizadas para monitorar condições específicas, você poderá notificar o administrador do Office por email quando as condições monitoradas ocorrerem.

No [primeiro tutorial](tutorial-facilities-setup.md), você configurou o grafo espacial de uma compilação imaginária. Uma sala na construção contém sensores de movimento, dióxido carbono e temperatura. No [segundo tutorial](tutorial-facilities-udf.md), você provisionou seu grafo e uma função definida pelo usuário para monitorar esses valores de sensor e disparar notificações quando a sala está vazia, e a temperatura e dióxido de carbono estão em um intervalo confortável. 

Este tutorial mostra-lhe como pode integrar essas notificações no Azure Logic Apps para enviar e-mails quando uma divisão que cumpra estes requisitos esteja disponível. Os administradores dos escritórios podem utilizar estas informações para ajudar os funcionários a reservar a sala de reuniões mais produtiva.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Integre eventos com a grade de eventos do Azure.
> * Notifique eventos com aplicativos lógicos.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, parte-se do princípio de que [configurou](tutorial-facilities-setup.md) e [aprovisionou](tutorial-facilities-udf.md) a sua configuração do Azure Digital Twins. Antes de avançar, confirme que tem:

- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância do Digital Twins em execução.
- Os [exemplos do Digital Twins em C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) transferidos e extraídos para o computador de trabalho.
- [SDK do .NET Core versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) em seu computador de desenvolvimento para executar o exemplo. Execute `dotnet --version` para verificar se a versão correta está instalada.
- Uma conta do Office 365 para enviar e-mails de notificação.

## <a name="integrate-events-with-event-grid"></a>Integrar eventos com o Event Grid

Nesta seção, você configura a [grade de eventos](../event-grid/overview.md) para coletar eventos de sua instância do gêmeos digital do Azure e redirecioná-los para um [manipulador de eventos](../event-grid/event-handlers.md) , como aplicativos lógicos.

### <a name="create-an-event-grid-topic"></a>Criar um tópico de grade de eventos

Um [tópico de grade de eventos](../event-grid/concepts.md#topics) fornece uma interface para rotear os eventos gerados pela função definida pelo usuário. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**. 

1. Procure e selecione **Event Grid Topic** (Tópico do Event Grid). Selecione **Criar**.

1. Introduza **Name** (Nome) do tópico do Event Grid e escolha **Subscrição**. Selecione o **grupo de recursos** que você usou ou criou para sua instância de gêmeos digital e o **local**. Selecione **Criar**. 

    ![Criar um tópico de grade de eventos](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Navegue até o tópico da grade de eventos do seu grupo de recursos, selecione **visão geral**e copie o valor do **ponto de extremidade do tópico** para um arquivo temporário. Você precisará dessa URL na próxima seção. 

1. Selecione **chaves de acesso**e copie **YOUR_KEY_1** e **YOUR_KEY_2** para um arquivo temporário. Você precisará desses valores para criar o ponto de extremidade na próxima seção.

    ![Chaves de grade de eventos](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Criar um ponto de extremidade para o tópico da grade de eventos

1. Na janela de comando, verifique se você está na pasta **occupancy-quickstart\src** do exemplo gêmeos digital.

1. Abra o arquivo **actions\createEndpoints.YAML** no seu editor de Visual Studio Code. Verifique se os conteúdos seguintes estão presentes:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: <Primary connection string for your Event Grid>
      secondaryConnectionString: <Secondary connection string for your Event Grid>
      path: <Event Grid Topic Name without https:// and /api/events, e.g. eventgridname.region.eventgrid.azure.net>
    ```

1. Substitua o espaço `<Primary connection string for your Event Grid>` reservado pelo valor de **YOUR_KEY_1**.

1. Substitua o espaço `<Secondary connection string for your Event Grid>` reservado pelo valor de **YOUR_KEY_2**.

1. Substitua o espaço reservado pelo caminho pelo caminho do tópico da grade de eventos. Obtenha esse caminho removendo **https://** e os caminhos de recurso à direita da URL do **ponto de extremidade do tópico** . Deverá ser semelhante ao formato *NomedoEventGrid.asuaLocalização.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Introduza todos os valores, sem aspas. Verifique se há pelo menos um caractere de espaço após os dois-pontos no arquivo YAML. Você também pode validar o conteúdo do arquivo YAML usando qualquer validador YAML online, como [essa ferramenta](https://onlineyamltools.com/validate-yaml).

1. Guarde e feche o ficheiro. Na janela de comandos, execute o comando seguinte e inicie sessão quando lhe for pedido. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Este comando cria o ponto de extremidade para a grade de eventos. 

   ![Pontos finais do Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)

## <a name="notify-events-with-logic-apps"></a>Notificar eventos com aplicativos lógicos

Você pode usar o serviço de [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) para criar tarefas automatizadas para eventos recebidos de outros serviços. Nesta seção, você configura aplicativos lógicos para criar notificações por email para eventos roteados de seus sensores espaciais, com a ajuda de um [tópico de grade de eventos](../event-grid/overview.md).

1. No painel esquerdo da [portal do Azure](https://portal.azure.com), selecione **criar um recurso**.

1. Procure e selecione o recurso **Logic App**. Selecione **Criar**.

1. Insira um **nome** para o recurso de aplicativo lógico e, em seguida, selecione sua **assinatura**, **grupo de recursos**e **local**. Selecione **Criar**.

    ![Criar um recurso de aplicativos lógicos](./media/tutorial-facilities-events/create-logic-app.png)

1. Abra o recurso de aplicativos lógicos quando ele for implantado e, em seguida, abra o painel **Designer de aplicativo lógico** . 

1. Selecione o gatilho **quando ocorre um evento de recurso da grade de eventos** . Entre em seu locatário com sua conta do Azure quando solicitado. Selecione **permitir acesso** para seu recurso de grade de eventos, se solicitado. Selecione **Continuar**.

1. Na janela **quando um evento de recurso ocorre (visualização)** : 
   
   a. Selecione a **assinatura** que você usou para criar o tópico da grade de eventos.

   b. Selecione **Microsoft. EventGrid. topics** para **tipo de recurso**.

   c. Selecione o recurso de grade de eventos na caixa suspensa para **nome do recurso**.

   ![Painel do designer de aplicativo lógico](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Selecione o botão **nova etapa** .

1. Na janela **escolher uma ação** :

   a. Procure a expressão **parse json** (analisar json) e selecione a ação **Parse JSON**.

   b. No campo **conteúdo** , selecione **corpo** na lista de **conteúdo dinâmico** .

   c. Selecione **Utilizar o payload de exemplo para gerar esquema**. Cole o seguinte conteúdo JSON e, em seguida, selecione **concluído**.

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

    Este payload tem valores fictícios. Os aplicativos lógicos usam esse conteúdo de exemplo para gerar um *esquema*.

    ![Os aplicativos lógicos analisam a janela JSON para a grade de eventos](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Selecione o botão **nova etapa** .

1. Na janela **escolher uma ação** :

   a. Selecione **controle > condição** ou **condição** de pesquisa na lista **ações** . 

   b. Na primeira caixa de texto **escolher um valor** , selecione **EventType** na lista de **conteúdo dinâmico** para a janela **analisar JSON** .

   c. Na segunda caixa de texto **escolher um valor** , digite `UdfCustom`.

   ![Condições selecionadas](./media/tutorial-facilities-events/logic-app-condition.png)

1. Na janela **se verdadeiro** :

   a. Selecione **Adicionar uma ação**e selecione **Office 365 Outlook**.

   b. Na lista **ações** , selecione **enviar um email**. Selecione **entrar** e use suas credenciais de conta de email. Selecione **permitir acesso** , se solicitado.

   c. Na caixa **To** (Para), introduza o ID do seu e-mail para receber notificações. Em **assunto**, insira o texto **notificação de gêmeos digital para uma qualidade de ar ruim no espaço**. Em seguida, selecione **TopologyObjectId** na lista de **conteúdo dinâmico** para **analisar JSON**.

   d. Em **corpo** na mesma janela, insira um texto semelhante a este: **Qualidade de ar ruim detectada em uma sala e a temperatura precisa ser ajustada**. Sinta-se à vontade para elaborar usando elementos da lista de **conteúdo dinâmico** .

   ![Seleções de aplicativos lógicos "enviar um email"](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Selecione o botão **salvar** na parte superior do painel **Designer de aplicativo lógico** .

1. Certifique-se de simular os dados do sensor navegando até a pasta de **conectividade do dispositivo** do exemplo digital gêmeos em uma janela `dotnet run`de comando e executando.

Em alguns minutos, você deve começar a receber notificações por email desse recurso de aplicativos lógicos. 

   ![Notificação de e-mail](./media/tutorial-facilities-events/logic-app-notification.png)

Para parar de receber esses emails, vá para o recurso aplicativos lógicos no portal e selecione o painel **visão geral** . Selecione **desativar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender parar a explorar duplos Digital do Azure neste momento, fique à vontade eliminar recursos criados neste tutorial:

1. No menu do lado esquerdo da [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, selecione o grupo de recursos digitais duplos e selecione **eliminar**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

2. Se necessário, exclua os aplicativos de exemplo em seu computador de trabalho.

## <a name="next-steps"></a>Passos seguintes

Para saber como visualizar os dados do sensor, analisar tendências e identificar anomalias, vá para o próximo tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Visualize e analise eventos de seus espaços de gêmeos digitais do Azure usando Time Series Insights](tutorial-facilities-analyze.md)

Você também pode saber mais sobre os gráficos de inteligência espacial e os modelos de objeto no gêmeos digital do Azure:

> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Compreender os modelos de objetos e o gráfico de inteligência espacial do Digital Twins)
