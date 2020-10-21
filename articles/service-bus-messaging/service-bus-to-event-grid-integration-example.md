---
title: 'Tutorial: Azure Service Bus para exemplos de integração da Grade de Eventos'
description: 'Tutorial: Este artigo fornece exemplos de mensagens de Service Bus e integração de Event Grid.'
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: c7f177b8738fdaf8d0975877d9af094b6f0a49a9
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328152"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Tutorial: Responder aos eventos de autocarro do Azure Service recebidos através da Azure Event Grid utilizando funções Azure e Apps Azure Logic
Neste tutorial, você aprende a responder aos eventos do Azure Service Bus que são recebidos através da Azure Event Grid utilizando Azure Functions e Azure Logic Apps. 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes do Service Bus
> * Preparar uma aplicação de amostra para enviar mensagens
> * Envie mensagens para o tema do Service Bus
> * Receber mensagens através do Logic Apps
> * Configurar uma função de teste no Azure
> * Ligar a função e o espaço de nomes através do Event Grid
> * Receber mensagens através das Funções do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem instalada:

- [Visual Studio 2017 Atualização 3 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
- [SDK NET Core](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus
Siga as instruções neste tutorial: [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para fazer as seguintes tarefas:

- Crie um espaço de nome de ônibus de serviço **premium.** 
- Pegue a corda de ligação. 
- Crie um tópico de ônibus de serviço.
- Crie duas subscrições para o tema. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Preparar uma aplicação de amostra para enviar mensagens
Pode utilizar qualquer método para enviar uma mensagem para o tópico do Service Bus. O código de amostra no final deste procedimento pressupõe que está a usar o Visual Studio 2017.

1. Clone [o repositório azure-service-bus do GitHub](https://github.com/Azure/azure-service-bus/).
2. No Visual Studio, aceda à pasta *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* e abra o ficheiro *SBEventGridIntegration.sln*.
3. Aceda ao projeto **MessageSender** e selecione **Program.cs**.
4. Preencha o nome do tópico do seu Service Bus e a cadeia de ligação que obteve do passo anterior:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Atualizar o `numberOfMessages` valor para **5**. 
5. Construa e execute o programa para enviar mensagens de teste para o tópico do Service Bus. 

## <a name="receive-messages-by-using-logic-apps"></a>Receber mensagens através do Logic Apps
Conecte uma aplicação lógica com a Azure Service Bus e a Azure Event Grid seguindo estes passos:

1. Crie uma aplicação lógica no portal Azure.
    1. Selecione **+ Crie um recurso,** selecione **Integração**e, em seguida, selecione **a Aplicação Lógica**. 
    2. Na **Aplicação Lógica - Criar** página, insira um **nome** para a aplicação lógica.
    3. Selecione a sua **subscrição Azure**. 
    4. Selecione **Utilizar o** **grupo de recursos**e selecione o grupo de recursos que utilizou para outros recursos (como a função Azure, Service Bus namespace) que criou anteriormente. 
    5. Selecione a **Localização** para a aplicação lógica. 
    6. Selecione **Criar** para criar a aplicação lógica. 
2. Na página **de Design de Aplicações Lógicas,** selecione Blank Logic **App** em **modelos**. 
3. No designer, faça os seguintes passos:
    1. Procure por **Grelha de Eventos.** 
    2. Selecione **Quando ocorrer um evento de recurso - Azure Event Grid**. 

        ![Logic Apps Designer - selecione Event Grid trigger](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecione **Iniciar sôms,** insira as suas credenciais Azure e selecione **Permitir o Acesso**. 
5. Na página **Quando ocorrer um evento de recurso,** faça os seguintes passos:
    1. Selecione a sua subscrição do Azure. 
    2. Para **o tipo de recurso**, selecione **Microsoft.ServiceBus.Namespaces**. 
    3. Para **obter nome de recurso,** selecione o seu espaço de nomes de Service Bus. 
    4. **Selecione Adicione novo parâmetro**e selecione Filtro de **Sufixo**. 
    5. Para **o Filtro Sfixix,** insira o nome da sua segunda subscrição de tópico Service Bus. 
        ![Logic Apps Designer - evento de configuração](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecione **+ Novo Passo** no designer e faça os seguintes passos:
    1. Pesquisa rumo **ao Service Bus**.
    2. Selecione **Service Bus** na lista. 
    3. Selecione para **Obter mensagens** na lista **de Ações.** 
    4. **Selecione Obter mensagens de uma subscrição de tópico (peek-lock)**. 

        ![Logic Apps Designer - obtenha ação de mensagens](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Introduza um **nome para a ligação**. Por exemplo: **Obtenha mensagens da subscrição de tópicos**e selecione o espaço de nomes do Service Bus. 

        ![Logic Apps Designer - selecione o espaço de nomes do Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selecione **RootManageSharedAccessKey**e, em seguida, **selecione Create**.

        ![Logic Apps Designer - selecione a chave de acesso partilhada](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Selecione o seu **tópico** e **subscrição**. 
    
        ![Screenshot que mostra onde seleciona o seu tópico e subscrição.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecione **+ Novo passo**, e faça os seguintes passos: 
    1. Selecione **Service Bus**.
    2. **Selecione Preencha a mensagem numa subscrição de tópico** da lista de ações. 
    3. Selecione o seu tópico de ônibus **de serviço**.
    4. Selecione a segunda **subscrição** do tópico.
    5. Para **bloquear o sinal da mensagem,** selecione Lock **Token** a partir do **conteúdo Dynamic**. 

        ![Logic Apps Designer - selecione o tópico e subscrição do Service Bus](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. **Selecione Guardar** na barra de ferramentas no Logic Apps Designer para guardar a aplicação lógica. 
9. Siga as instruções na secção Enviar mensagens para a secção [de tópicos do Service Bus](#send-messages-to-the-service-bus-topic) para enviar mensagens para o tema. 
10. Mude para a página **geral** da sua aplicação lógica. Você vê a aplicação lógica corre na história do **Runs** para as mensagens enviadas.

    ![Logic Apps Designer - aplicação lógica corre](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="set-up-a-test-function-on-azure"></a>Configurar uma função de teste no Azure 
Antes de trabalhar em todo o cenário, crie pelo menos uma pequena função de teste, que pode usar para depurar e observar os eventos que estão a fluir. Siga as instruções no Criar a sua primeira função no artigo [do portal Azure](../azure-functions/functions-create-first-azure-function.md) para fazer as seguintes tarefas: 

1. Criar uma aplicação de função.
2. Crie uma função de desencadeamento HTTP. 

Em seguida, faça os seguintes passos: 


# <a name="azure-functions-v2"></a>[Funções Azure V2](#tab/v2)

1. Expanda **as funções** na vista da árvore e selecione a sua função. Substitua o código da função pelo seguinte código: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                log.LogInformation("Validating the subscription");            
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation($"Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }    
    ```
2. **Selecione Guarde** na barra de ferramentas para guardar o código para a função.

    ![Guardar código de função](./media/service-bus-to-event-grid-integration-example/save-function-code.png)
3. Selecione **Teste/Execução** na barra de ferramentas e faça os seguintes passos: 
    1. Introduza o seguinte JSON no **corpo**.

        ```json
        [{
          "id": "64ba80ae-9f8e-425f-8bd7-d88d2c0ba3e3",
          "topic": "/subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/spegridsbusrg/providers/Microsoft.ServiceBus/namespaces/spegridsbusns",
          "subject": "",
          "data": {
            "validationCode": "D7D825D4-BD04-4F73-BDE3-70666B149857",
            "validationUrl": "https://rp-eastus.eventgrid.azure.net:553/eventsubscriptions/spsbusegridsubscription/validate?id=D7D825D4-BD04-4F73-BDE3-70666B149857&t=2020-06-09T18:28:51.5724615Z&apiVersion=2020-04-01-preview&[Hidden Credential]"
          },
          "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
          "eventTime": "2020-06-09T18:28:51.5724615Z",
          "metadataVersion": "1",
          "dataVersion": "2"
        }]
        ```    
    2. Clique **em Adicionar cabeçalho**e adicione um cabeçalho com nome `aeg-event-type` e valor `SubscriptionValidation` . 
    3. Selecione **Executar**. 

        ![Teste](./media/service-bus-to-event-grid-integration-example/test-run-function.png)
    4. Confirme que vê o código de estado de devolução de **OK** e o código de validação no organismo de resposta. Consulte também as informações registadas pela função. 

        ![Teste - resposta](./media/service-bus-to-event-grid-integration-example/test-function-response.png)        
3. Selecione **Obter URL de função** e anotar o URL. 

    ![Obter URL de função](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Selecione o botão **de cópia** ao lado do texto URL.    
    ![URL de função de cópia](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

# <a name="azure-functions-v1"></a>[Funções Azure V1](#tab/v1)

1. Configure a função de utilização da versão **V1:** 
    1. Selecione a sua aplicação de função na vista da árvore e selecione **as definições da aplicação Função**. 
    2. Selecione **~1** para **a versão Runtime**. 
2. Expanda **as funções** na vista da árvore e selecione a sua função. Substitua o código da função pelo seguinte código: 

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Selecione **Guardar e executar**.

    ![Saída de aplicativo de função](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. **Selecione Obter URL de função** na barra de ferramentas. 

    ![Obter URL de função](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Selecione o botão **de cópia** ao lado do texto URL.    
    ![URL de função de cópia](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Ligar a função e o espaço de nomes através do Event Grid
Nesta secção, você liga a função e o espaço de nomes do Service Bus utilizando o portal Azure. 

Para criar uma subscrição da Azure Event Grid, siga estes passos:

1. No portal Azure, vá ao seu espaço de nome e, em seguida, no painel esquerdo, selecione **Eventos**. É aberta a janela do espaço de nomes, com duas subscrições do Event Grid no painel direito. 
    
    ![Service Bus - página de eventos](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Selecione **+ Subscrição de evento** na barra de ferramentas. 
3. Na página **'Criar Subscrição de** Eventos', faça os seguintes passos:
    1. Insira um **nome** para a subscrição. 
    2. Insira um **nome** para o tópico do **sistema**. Os tópicos do sistema são tópicos criados para recursos Azure, como a conta Azure Storage e a Azure Service Bus. Para saber mais sobre tópicos do sistema, consulte [a visão geral dos tópicos do Sistema.](../event-grid/system-topics.md)
    2. Selecione **Web Hook** para **o tipo de ponto final**. 

        ![Service Bus - Subscrição da Grade de Eventos](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. **Selecione selecionar um ponto final,** cole o URL de função e, em seguida, selecione **Confirmar seleção**. 

        ![Função - selecione o ponto final](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Mude para o **separador Filtros** e faça as seguintes tarefas:
        1. **Selecione Habilitar a filtragem do assunto**
        2. Insira o nome da **primeira subscrição** do tópico Service Bus que criou anteriormente.
        3. Selecione o botão **Criar**. 

            ![Filtro de assinatura de evento](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Mude para o separador Subscrições de **Eventos** da página **Eventos** e confirme que vê a subscrição do evento na lista.

    ![Subscrição de evento na lista](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Envie mensagens para o tema do Service Bus
1. Execute a aplicação .NET C#, que envia mensagens para o tópico Service Bus. 

    ![Saída de aplicativo de consola](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na página para a sua aplicação de função Azure, mude para o **separador Monitor** a partir do separador **Código + Teste.** Deve ver uma entrada para cada mensagem publicada no tópico do Service Bus. Se não os vir, refresque a página depois de esperar alguns minutos. 

    ![Função monitor](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

    Também pode utilizar o **separador Registos** da página **Monitor** para ver as informações de registo à medida que as mensagens são enviadas. Pode atrasar-se, por isso dá-lhe alguns minutos para ver as mensagens registadas. 

## <a name="receive-messages-by-using-azure-functions"></a>Receber mensagens através das Funções do Azure
Na secção anterior, observou um cenário simples de teste e depuração e garantiu que os eventos estão a fluir. 

Nesta secção, saiba como receber e processar mensagens depois de receber um evento.

### <a name="publish-a-function-from-visual-studio"></a>Publicar uma função do Visual Studio
1. Na mesma solução visual Studio (**SBEventGridIntegration)** que abriu, selecione **ReceiveMessagesOnEvent.cs** no projeto **SBEventGridIntegration.** 
2. Introduza a sua cadeia de ligação Service Bus no seguinte código:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Descarregue o **perfil de publicação** para a função:
    1. Selecione a sua aplicação de funções. 
    2. Selecione o **separador Visão Geral** se ainda não estiver selecionado. 
    3. **Selecione Obter o perfil de publicação** na barra de ferramentas. 

        ![Obtenha o perfil de publicação para a função](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Guarde o ficheiro para a pasta do seu projeto. 
4. No Visual Studio, clique com o botão direito do rato em **SBEventGridIntegration** e, em seguida, selecione **Publicar**. 
5. Na **Publicação,** faça os seguintes passos: 
    1. Selecione **Iniciar** na página **publicar** 
    2. Para o **Alvo**, selecione **Perfil de Importação**. 
    3. Selecione **Seguinte**. 

        ![Estúdio Visual - Botão de Perfil de Importação](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Selecione o **ficheiro de perfil de publicação** que descarregou anteriormente e selecione **Terminar**.

    ![Selecione o perfil de publicação](./media/service-bus-to-event-grid-integration-example/select-publish-profile.png)
8. Selecione **Publicar** na página **publicar.** 

    ![Estúdio Visual - Publicar](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Confirme que vê a nova função Azure **ReceiveMessagesOnEvent**. Refresque a página se necessário. 

    ![Confirme que a nova função é criada](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Faça o URL para a nova função e note-o para baixo. 

### <a name="event-grid-subscription"></a>Assinatura da Grelha de Eventos

1. Eliminar a subscrição existente da Grade de Eventos:
    1. Na página **Service Bus Namespace,** selecione **Eventos** no menu esquerdo. 
    2. Mude para o separador **Subscrições de Eventos.** 
    2. Selecione a subscrição do evento existente. 

        ![Selecione subscrição de eventos](./media/service-bus-to-event-grid-integration-example/select-event-subscription.png)
    3. Na página **de Subscrição** de Eventos, selecione **Delete**. Selecione **Sim** para confirmar a eliminação. 
        ![Eliminar botão de subscrição de eventos](./media/service-bus-to-event-grid-integration-example/delete-subscription-button.png)
2. Siga as instruções na [secção Connect the function and namespace via Event Grid](#connect-the-function-and-namespace-via-event-grid) para criar uma subscrição de Grade de Eventos utilizando o novo URL de função.
3. Siga as instruções na secção Enviar mensagens para a secção [de tópicos do Service Bus](#send-messages-to-the-service-bus-topic) para enviar mensagens para o tema e monitorizar a função. 


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Azure Event Grid](../event-grid/index.yml).
* Saiba mais sobre as [Funções do Azure](../azure-functions/index.yml).
* Saiba mais sobre a [funcionalidade Logic Apps do Serviço de Aplicações do Azure](../logic-apps/index.yml).
* Saiba mais sobre o [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png