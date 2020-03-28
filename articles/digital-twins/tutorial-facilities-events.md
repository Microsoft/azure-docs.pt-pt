---
title: 'Tutorial: Capturar eventos de dispositivos de um espaço IoT - Azure Digital Twins Microsoft Docs'
description: Saiba como receber notificações dos seus espaços mediante a integração do Azure Digital Twins no Logic Apps através dos passos descritos neste tutorial.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 1cd617204bbc12a99b6ae9e3b55fbc59b0e0578a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75933701"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Tutorial: Receba notificações dos seus espaços Azure Digital Twins utilizando aplicações lógicas

Depois de implementar a sua instância De Gémeos Digitais Azure, fornecer os seus espaços e implementar funções personalizadas para monitorizar condições específicas, pode notificar o seu administrador de escritório por e-mail quando as condições monitorizadas ocorrerem.

No [primeiro tutorial,](tutorial-facilities-setup.md)configuraste o gráfico espacial de um edifício imaginário. Uma sala no edifício contém sensores para movimento, dióxido de carbono e temperatura. No [segundo tutorial,](tutorial-facilities-udf.md)forprovisionou o seu gráfico e uma função definida pelo utilizador para monitorizar estes valores de sensor e disparar notificações quando a sala está vazia, e a temperatura e o dióxido de carbono estão numa gama confortável. 

Este tutorial mostra-lhe como pode integrar essas notificações no Azure Logic Apps para enviar e-mails quando uma divisão que cumpra estes requisitos esteja disponível. Os administradores dos escritórios podem utilizar estas informações para ajudar os funcionários a reservar a sala de reuniões mais produtiva.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Integre eventos com a Azure Event Grid.
> * Notifique eventos com Aplicações Lógicas.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, parte-se do princípio de que [configurou](tutorial-facilities-setup.md) e [aprovisionou](tutorial-facilities-udf.md) a sua configuração do Azure Digital Twins. Antes de avançar, confirme que tem:

- Uma [conta Azure.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma instância do Digital Twins em execução.
- As [amostras Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) foram descarregadas e extraídas na sua máquina de trabalho.
- [.NET Core SDK versão 2.1.403 ou mais tarde](https://www.microsoft.com/net/download) na sua máquina de desenvolvimento para executar a amostra. Corra `dotnet --version` para verificar se a versão certa está instalada.
- Uma conta [do Office 365](https://products.office.com/home) para enviar e-mails de notificação.

> [!TIP]
> Use um nome único de gémeos digitais se estiver a fornecer uma nova instância.

## <a name="integrate-events-with-event-grid"></a>Integrar eventos com o Event Grid

Nesta secção, você configura a Rede de [Eventos](../event-grid/overview.md) para recolher eventos da sua instância Azure Digital Twins, e redirecioná-los para um manipulador de [eventos](../event-grid/event-handlers.md) como Apps Lógicas.

### <a name="create-an-event-grid-topic"></a>Criar um tópico de grelha de evento

Um tópico de grelha de [evento](../event-grid/concepts.md#topics) sintetiza uma interface para encaminhar os eventos gerados pela função definida pelo utilizador. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso**. 

1. Procure e selecione **Event Grid Topic** (Tópico do Event Grid). Selecione **Criar**.

1. Introduza **Name** (Nome) do tópico do Event Grid e escolha **Subscrição**. Selecione o **grupo Derecursos** que usou ou criou para a sua instância De Gémeos Digitais e a **Localização**. Selecione **Criar**. 

    [![Criar um tópico de grelha de evento](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Navegue no tópico da grelha de eventos do seu grupo de recursos, selecione **Visão Geral**e copie o valor para Tópico **Final point** para um ficheiro temporário. Vai precisar deste URL na próxima secção. 

1. Selecione **teclas de acesso**e copie **a Chave 1** e a Chave **2** para um ficheiro temporário. Vai precisar destes valores para criar o ponto final na próxima secção.

    [![Chaves da Grelha de Eventos](./media/tutorial-facilities-events/tutorial-event-grid-keys.png)](./media/tutorial-facilities-events/tutorial-event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Criar um ponto final para o tópico da grelha de eventos

1. Na janela de comando, certifique-se de que está na pasta de **ocupação-quickstart\src** da amostra Das Gémeas Digitais.

1. Abra o ficheiro **actions\createEndpoints.yaml** no editor do Visual Studio Code. Verifique se os conteúdos seguintes estão presentes:

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

1. Substitua o `<Primary connection string for your Event Grid>` espaço reservado pelo valor da **chave 1**.

1. Substitua o `<Secondary connection string for your Event Grid>` espaço reservado pelo valor da **chave 2**.

1. Substitua o espaço reservado para **o caminho** pelo caminho do tópico da grelha de eventos. Obtenha este caminho removendo **https://** e os caminhos de recursos de trailing do URL **de Ponto Final** tópico. Deverá ser semelhante ao formato *NomedoEventGrid.asuaLocalização.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Introduza todos os valores, sem aspas. Certifique-se de que há pelo menos um personagem espacial depois dos cólons no ficheiro YAML. Também pode validar o conteúdo do seu ficheiro YAML utilizando qualquer validador YAML online, como [esta ferramenta](https://onlineyamltools.com/validate-yaml).

1. Guarde e feche o ficheiro. Na janela de comandos, execute o comando seguinte e inicie sessão quando lhe for pedido. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Este comando cria o ponto final para a Grelha de Eventos. 

   [![Pontos finais do Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Notificar eventos com aplicações lógicas

Pode utilizar o serviço [De Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) para criar tarefas automatizadas para eventos recebidos de outros serviços. Nesta secção, configura as Aplicações Lógicas para criar notificações por e-mail para eventos encaminhados dos seus sensores espaciais, com a ajuda de um tópico de grelha de [eventos.](../event-grid/overview.md)

1. No painel esquerdo do [portal Azure,](https://portal.azure.com)selecione **Criar um recurso**.

1. Procure e selecione o recurso **Logic App**. Selecione **Criar**.

1. Introduza um **Nome** para o seu recurso Logic App e, em seguida, selecione a sua **Subscrição,** **Grupo de Recursos**e **Localização**. Selecione **Criar**.

    [![Criar um recurso de Aplicações Lógicas](./media/tutorial-facilities-events/tutorial-create-logic-app.png)](./media/tutorial-facilities-events/tutorial-create-logic-app.png#lightbox)

1. Abra o seu recurso Logic Apps quando estiver implantado e, em seguida, abra o painel de **designerde aplicações Logic.** 

1. Selecione o gatilho quando ocorre um evento de recurso da Grelha de **Eventos.** Expanda a opção **Azure Event Grid** e inscreva-se no seu inquilino com a sua conta Azure quando solicitado. Selecione **Permitir o acesso** ao seu recurso da Rede de Eventos, se solicitado. Selecione **Continuar**.

1. No **Quando ocorre um evento** de recursos: 
   
   a. Selecione a **Subscrição** que usou para criar o tópico da grelha de eventos.

   b. Selecione **Microsoft.EventGrid.Tópicos** para **Tipo de Recurso**.

   c. Selecione o seu recurso De Rede de Eventos a partir da caixa drop-down para nome de **recurso**.

   [![Painel de designer de aplicativos lógico](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Selecione o novo botão **de passo.**

1. Na **janela Escolha uma** janela de ação:

   a. Procure a expressão **parse json** (analisar json) e selecione a ação **Parse JSON**.

   b. No campo **Conteúdo,** selecione **Corpo** da lista de **conteúdos Dinâmicos.**

   c. Selecione **Utilizar o payload de exemplo para gerar esquema**. Colhe a seguinte carga útil jSON e, em seguida, selecione **Done**.

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

    Este payload tem valores fictícios. As Aplicações Lógicas usam esta carga útil da amostra para gerar um *esquema*.

    [![Lógica Apps Parse JSON janela para grelha de eventos](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Selecione o novo botão **de passo.**

1. Na **janela Escolha uma** janela de ação:

   a. Selecione **Controlar > Condição** ou procurar **condição** na lista **de Ações.** 

   b. Na primeira Escolha uma caixa de texto de **valor,** selecione **eventoType** da lista de **conteúdos Dinâmicos** para a janela **Parse JSON.**

   c. Na segunda Escolha uma caixa `UdfCustom`de texto de **valor,** introduza .

   [![Condições selecionadas](./media/tutorial-facilities-events/tutorial-logic-app-condition.png)](./media/tutorial-facilities-events/tutorial-logic-app-condition.png#lightbox)

1. Na janela **"Se verdadeiro":**

   a. **Selecione Adicionar uma ação**, e selecione Office **365 Outlook**.

   b. A partir da lista **de Ações,** selecione **Enviar um e-mail (V2)**. Selecione **Iniciar sessão** e utilizar as credenciais da sua conta de e-mail. Selecione **Permitir o acesso** se solicitado.

   c. Na caixa **To** (Para), introduza o ID do seu e-mail para receber notificações. Em **Assunto,** insira o texto **Digital Twins notificação por má qualidade do ar no espaço.** Em seguida, selecione **TopologyObjectId** da lista de **conteúdos Dinâmicos** para **Parse JSON**.

   d. Sob o **corpo** na mesma janela, introduza texto semelhante ao seguinte: Má qualidade do **ar detetada numa sala e a temperatura precisa de ser ajustada**. Sinta-se livre para elaborar usando elementos da lista de **conteúdos Dinâmicos.**

   [![Aplicações lógicas "Enviar um e-mail" seleções](./media/tutorial-facilities-events/tutorial-logic-app-send-email.png)](./media/tutorial-facilities-events/tutorial-logic-app-send-email.png#lightbox)

1. Selecione o botão **Guardar** no topo do painel de designers de **aplicações Logic.**

1. **Certifique-se** de simular os dados do sensor navegando para a pasta de `dotnet run`conectividade do dispositivo da amostra De Gémeos Digitais numa janela de comando, e executando .

Em poucos minutos, deverá começar a receber notificações por e-mail deste recurso de Aplicações Lógicas. 

   [![Notificação por e-mail](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Para parar de receber estes e-mails, vá ao recurso das Suas Apps Lógicas no portal e selecione o painel **de visão geral.** Selecione **Desativar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser parar de explorar as Gémeas Digitais Azure neste momento, sinta-se à vontade para apagar os recursos criados neste tutorial:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos,** selecione o seu grupo de recursos Digital Twins e selecione **Delete**.

    > [!TIP]
    > Se teve problemas em apagar a sua instância De Gémeos Digitais, foi lançada uma atualização de serviço com a correção. Por favor, tente apagar o seu caso.

2. Se necessário, elimine as aplicações da amostra na sua máquina de trabalho.

## <a name="next-steps"></a>Passos seguintes

Para aprender a visualizar os dados do seu sensor, analisar tendências e detetar anomalias, vá ao próximo tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Visualize and analyze events from your Azure Digital Twins spaces using Time Series Insights](tutorial-facilities-analyze.md) (Tutorial: Visualizar e analisar eventos dos seus espaços do Azure Digital Twins com o Time Series Insights)

Você também pode aprender mais sobre os gráficos de inteligência espacial e modelos de objetos em Azure Digital Twins:

> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Compreender os modelos de objetos e o gráfico de inteligência espacial do Digital Twins)
