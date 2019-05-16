---
title: Exemplos de integração do Azure Service Bus para o Event Grid | Microsoft Docs
description: Este artigo fornece exemplos de mensagens do Service Bus e da integração do Event Grid.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b29798bb87b7c5c677e7d80e552e45e8d1290541
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65754834"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Responder a eventos do Azure Service Bus recebidos através do Azure Event Grid com as funções do Azure e Azure Logic Apps
Neste tutorial, saiba como responder a eventos do Azure Service Bus que são recebidos através do Azure Event Grid com as funções do Azure e Azure Logic Apps. Terá de efetuar os seguintes passos:
 
- Crie uma função do Azure de teste para depurar e ver o fluxo inicial de eventos do Event Grid.
- Crie uma função do Azure para receber e processar mensagens do Service bus do Azure com base em eventos do Event Grid.
- Criar uma aplicação lógica para responder a eventos do Event Grid

Depois de criar o Service Bus, Event Grid, as funções do Azure e os artefactos de aplicações lógicas, executar as seguintes ações: 

1. Envie mensagens para um tópico do Service Bus. 
2. Certifique-se de que as subscrições para o tópico receberam essas mensagens
3. Certifique-se de que a função ou aplicação lógica que subscrito para o evento recebeu o evento. 

## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus
Siga as instruções neste tutorial: [Quickstart: Utilizar o portal do Azure para criar um tópico do Service Bus e subscrições para o tópico](service-bus-quickstart-topics-subscriptions-portal.md) para efetuar as seguintes tarefas:

- Criar uma **premium** espaço de nomes do Service Bus. 
- Obter a cadeia de ligação. 
- Crie um tópico do Service Bus.
- Crie duas subscrições para o tópico. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Preparar um aplicativo de exemplo para enviar mensagens
Pode utilizar qualquer método para enviar uma mensagem para o tópico do Service Bus. O código de exemplo no final deste procedimento pressupõe que esteja usando o Visual Studio 2017.

1. Clone [o repositório azure-service-bus do GitHub](https://github.com/Azure/azure-service-bus/).
2. No Visual Studio, aceda à pasta *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* e abra o ficheiro *SBEventGridIntegration.sln*.
3. Aceda ao projeto **MessageSender** e selecione **Program.cs**.
4. Preencha o nome do tópico do Service Bus e a cadeia de ligação que obteve no passo anterior:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Crie e execute o programa para enviar mensagens de teste para o tópico do Service Bus. 

## <a name="set-up-a-test-function-on-azure"></a>Configurar uma função de teste no Azure 
Antes de trabalhar em todo o cenário, configure a, pelo menos, uma pequena função de teste, que pode utilizar para depurar e observar os eventos que estão a fluir. Siga as instruções no [criar a primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md) artigo para fazer as seguintes tarefas: 

1. Crie uma aplicação de funções.
2. Crie uma função acionada por HTTP. 

Em seguida, siga os passos abaixo: 


# <a name="azure-functions-v2tabv2"></a>[As funções do Azure V2](#tab/v2)

1. Expanda **funções** na árvore de ver e selecionar a sua função. Substitua o código para a função com o código a seguir: 

    ```CSharp
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
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
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
2. Selecione **Guardar e executar**.

    ![Saída da aplicação de função](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Selecione **obter URL de função** e anote o URL. 

    ![Obter URL de função](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[As funções do Azure V1](#tab/v1)

1. Configurar a função a utilizar **V1** versão: 
    1. Selecione a sua aplicação de função na vista de árvore e selecione **as definições da aplicação de função**. 

        ![Definições da aplicação de funções]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Selecione **~ 1** para **versão de Runtime**. 
2. Expanda **funções** na árvore de ver e selecionar a sua função. Substitua o código para a função com o código a seguir: 

    ```CSharp
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

    ![Saída da aplicação de função](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Selecione **obter URL de função** e anote o URL. 

    ![Obter URL de função](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Ligar a função e o espaço de nomes através do Event Grid
Nesta secção, vai ligar a função e o espaço de nomes do Service Bus com o portal do Azure. 

Para criar uma subscrição do Azure Event Grid, siga estes passos:

1. No portal do Azure, aceda ao seu espaço de nomes e, em seguida, no painel esquerdo, selecione **eventos**. É aberta a janela do espaço de nomes, com duas subscrições do Event Grid no painel direito. 
    
    ![Service Bus - página de eventos](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Selecione **+ subscrição de evento** na barra de ferramentas. 
3. Sobre o **criar subscrição de evento** página, efetue os seguintes passos:
    1. Introduza um **nome** para a subscrição. 
    2. Selecione **Hook de Web** para **tipo de ponto final**. 

        ![Service Bus - subscrição do Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Escolheu **selecione um ponto final**, cole o URL da função e, em seguida, selecione **confirmar seleção**. 

        ![Função – selecione o ponto final](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Mude para o **filtros** separador, introduza o nome da **primeira assinatura** para o tópico do Service Bus que criou anteriormente e, em seguida, selecione o **criar** botão. 

        ![Filtro de subscrição de evento](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Confirme que vê a subscrição de evento na lista.

    ![Subscrição de evento na lista](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Enviar mensagens para o tópico do Service Bus
1. Executar o .NET C# aplicativo, que envia mensagens para o tópico do Service Bus. 

    ![Resultado da aplicação de consola](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na página para a sua aplicação de função do Azure, expanda **funções**, expanda seu **função**e selecione **Monitor**. 

    ![Monitor de função](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Receber mensagens através das Funções do Azure
Na secção anterior, observou um cenário simples de teste e depuração e garantiu que os eventos estão a fluir. 

Nesta secção, saiba como receber e processar mensagens depois de receber um evento.

### <a name="publish-a-function-from-visual-studio"></a>Publicar uma função a partir do Visual Studio
1. Na mesma solução do Visual Studio (**SBEventGridIntegration**) que aberto, selecione **ReceiveMessagesOnEvent.cs** no **SBEventGridIntegration** projeto. 
2. Introduza a cadeia de ligação do Service Bus no código a seguir:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Transfira o **perfil de publicação** para a função:
    1. Selecione a sua aplicação de função. 
    2. Selecione o **descrição geral** separador se ainda não estiver selecionada. 
    3. Selecione **obter perfil de publicação** na barra de ferramentas. 

        ![Obter perfil para a função de publicação](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Guarde o ficheiro para pasta do seu projeto. 
4. No Visual Studio, clique com o botão direito do rato em **SBEventGridIntegration** e, em seguida, selecione **Publicar**. 
5. Selecione *começar** no **Publish** página. 
6. Sobre o **escolher um destino da publicação** página, siga os passos abaixo, selecione **importar perfil**. 

    ![Visual Studio – botão Importar perfil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Selecione o **publicar o ficheiro de perfil** transferiu anteriormente. 
8. Selecione **Publish** sobre o **publicar** página. 

    ![Visual Studio – publicar](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Confirme que vê a nova função do Azure **ReceiveMessagesOnEvent**. Se for necessário, atualize a página. 

    ![Confirme que a nova função é criada](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Obter o URL para a nova função e tome nota do mesmo. 

### <a name="event-grid-subscription"></a>Subscrição do Event Grid

1. Elimine a subscrição do Event Grid existente:
    1. Sobre o **espaço de nomes do Service Bus** página, selecione **eventos** no menu da esquerda. 
    2. Selecione a subscrição de evento existente. 
    3. Sobre o **subscrição de evento** página, selecione **eliminar**.
2. Siga as instruções no [ligar a função e o espaço de nomes através do Event Grid](#connect-the-function-and-namespace-via-event-grid) secção para criar uma subscrição do Event Grid com o novo URL da função.
3. Siga as instruções na [enviar mensagens para o tópico do Service Bus](#send-messages-to-the-service-bus-topic) secção para enviar mensagens para o tópico e monitorizar a função. 

## <a name="receive-messages-by-using-logic-apps"></a>Receber mensagens através do Logic Apps
Ligar uma aplicação lógica com o Azure Service Bus e Azure Event Grid através dos seguintes passos:

1. Crie uma aplicação lógica no portal do Azure.
    1. Selecione **+ criar um recurso**, selecione **integração**e, em seguida, selecione **aplicação lógica**. 
    2. Sobre o **aplicação lógica - criar** página, introduza um **nome** para a aplicação lógica.
    3. Selecione a sua **subscrição** do Azure. 
    4. Selecione **utilizar existente** para o **grupo de recursos**e selecione o grupo de recursos que utilizou para outros recursos (como a função do Azure, o espaço de nomes do Service Bus) que criou anteriormente. 
    5. Selecione o **localização** para a aplicação lógica. 
    6. Selecione **criar** para criar a aplicação lógica. 
2. Sobre o **estruturador de aplicações lógicas** página, selecione **aplicação lógica em branco** sob **modelos**. 
3. No estruturador, siga os passos abaixo:
    1. Procure **Event Grid**. 
    2. Selecione **quando um evento de recurso ocorre (pré-visualização) - Azure Event Grid**. 

        ![Estruturador de aplicações lógicas - selecionar acionador do Event Grid](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecione **iniciar sessão**, introduza as credenciais do Azure e selecione **permitir acesso**. 
5. Sobre o **quando ocorre um evento de recurso** página, efetue os seguintes passos:
    1. Selecione a sua subscrição do Azure. 
    2. Para **tipo de recurso**, selecione **Microsoft.ServiceBus.Namespaces**. 
    3. Para **nome do recurso**, selecione o seu espaço de nomes do Service Bus. 
    4. Selecione **adicione o novo parâmetro**e selecione **filtro de sufixo**. 
    5. Para **filtro de sufixo**, introduza o nome da sua subscrição de tópico do Service Bus segundo. 

        ![Estruturador de aplicações lógicas - configurar eventos](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecione **+ novo passo** no designer, e siga os passos abaixo:
    1. Procure **Service Bus**.
    2. Selecione **do Service Bus** na lista. 
    3. Selecione para **obter mensagens** no **ações** lista. 
    4. Selecione **obter mensagens de uma subscrição de tópico (bloqueio de pré-visualização)**. 

        ![Estruturador de aplicações lógicas - ação de mensagens de get](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Introduza um **nome da ligação**. Por exemplo: **Obter mensagens de subscrição de tópico**e selecione o espaço de nomes do Service Bus. 

        ![Estruturador de aplicações lógicas - selecione o espaço de nomes do Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selecione **RootManageSharedAccessKey**.

        ![Estruturador de aplicações lógicas - selecione a chave de acesso partilhado](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Selecione **Criar**. 
    8. Selecione o seu tópico e uma subscrição. 
    
        ![Estruturador de aplicações lógicas - selecione o seu tópico do Service Bus e subscrição](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecione **+ novo passo**, e siga os passos abaixo: 
    1. Selecione **Service Bus**.
    2. Selecione **concluir a mensagem numa subscrição de tópico** na lista de ações. 
    3. Selecione o Service Bus **tópico**.
    4. Selecione a segunda **subscrição** para o tópico.
    5. Para **token de bloqueio da mensagem**, selecione **Token de bloqueio** partir os **conteúdo dinâmico**. 

        ![Estruturador de aplicações lógicas - selecione o seu tópico do Service Bus e subscrição](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Selecione **guardar** na barra de ferramentas no Designer de aplicações lógicas para guardar a aplicação lógica. 
9. Siga as instruções na [enviar mensagens para o tópico do Service Bus](#send-messages-to-the-service-bus-topic) secção para enviar mensagens para o tópico. 
10. Mude para o **descrição geral** página da sua aplicação lógica. Pode ver a aplicação lógica é executado **histórico de execuções** para as mensagens enviadas.

    ![Estruturador de aplicações lógicas - execuções da aplicação lógica](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Saiba mais sobre as [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/).
* Saiba mais sobre a [funcionalidade Logic Apps do Serviço de Aplicações do Azure](https://docs.microsoft.com/azure/logic-apps/).
* Saiba mais sobre o [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).


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
