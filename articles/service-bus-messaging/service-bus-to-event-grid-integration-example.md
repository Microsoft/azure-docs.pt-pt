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
ms.openlocfilehash: f31e014cf242675577bedd29a3a79332ede32bf5
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304245"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Responder aos eventos do barramento de serviço do Azure recebidos por meio da grade de eventos do Azure usando Azure Functions e aplicativos lógicos do Azure
Neste tutorial, você aprenderá a responder aos eventos do barramento de serviço do Azure que são recebidos por meio da grade de eventos do Azure usando o Azure Functions e os aplicativos lógicos do Azure. Você executará as seguintes etapas:
 
- Crie um teste do Azure function para depurar e exibir o fluxo inicial de eventos da grade de eventos.
- Crie uma função do Azure para receber e processar mensagens do barramento de serviço do Azure com base em eventos de grade de eventos.
- Criar um aplicativo lógico para responder a eventos da grade de eventos

Depois de criar os artefatos do barramento de serviço, da grade de eventos, do Azure Functions e dos aplicativos lógicos, você executa as seguintes ações: 

1. Enviar mensagens para um tópico do barramento de serviço. 
2. Verifique se as assinaturas para o tópico receberam essas mensagens
3. Verifique se a função ou o aplicativo lógico que assinou o evento recebeu o evento. 

## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus
Siga as instruções neste tutorial: [Quickstart: Use o portal do Azure para criar um tópico e assinaturas do barramento de serviço para o tópico](service-bus-quickstart-topics-subscriptions-portal.md) para realizar as seguintes tarefas:

- Crie um namespace do barramento de serviço **Premium** . 
- Obter a cadeia de conexão. 
- Criar um tópico do barramento de serviço.
- Crie duas assinaturas para o tópico. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Preparar um aplicativo de exemplo para enviar mensagens
Pode utilizar qualquer método para enviar uma mensagem para o tópico do Service Bus. O código de exemplo no final deste procedimento pressupõe que você esteja usando o Visual Studio 2017.

1. Clone [o repositório azure-service-bus do GitHub](https://github.com/Azure/azure-service-bus/).
2. No Visual Studio, aceda à pasta *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* e abra o ficheiro *SBEventGridIntegration.sln*.
3. Aceda ao projeto **MessageSender** e selecione **Program.cs**.
4. Preencha o nome do tópico do barramento de serviço e a cadeia de conexão obtida da etapa anterior:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Compile e execute o programa para enviar mensagens de teste para o tópico do barramento de serviço. 

## <a name="set-up-a-test-function-on-azure"></a>Configurar uma função de teste no Azure 
Antes de trabalhar em todo o cenário, configure pelo menos uma função de teste pequena, que pode ser usada para depurar e observar os eventos que estão fluindo. Siga as instruções em [criar sua primeira função no artigo portal do Azure](../azure-functions/functions-create-first-azure-function.md) para executar as seguintes tarefas: 

1. Crie um aplicativo de funções.
2. Crie uma função disparada por HTTP. 

Em seguida, execute as seguintes etapas: 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions v2](#tab/v2)

1. Expanda **funções** no modo de exibição de árvore e selecione sua função. Substitua o código da função pelo código a seguir: 

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

    ![Saída do aplicativo de funções](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Selecione **obter URL da função** e anote a URL. 

    ![Obter URL da função](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions v1](#tab/v1)

1. Configure a função para usar a versão **v1** : 
    1. Selecione seu aplicativo de funções no modo de exibição de árvore e selecione **configurações do aplicativo de funções**. 

        ![Definições da Aplicação de funções]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Selecione **~ 1** para a **versão de tempo de execução**. 
2. Expanda **funções** no modo de exibição de árvore e selecione sua função. Substitua o código da função pelo código a seguir: 

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

    ![Saída do aplicativo de funções](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Selecione **obter URL da função** e anote a URL. 

    ![Obter URL da função](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Ligar a função e o espaço de nomes através do Event Grid
Nesta seção, você une a função e o namespace do barramento de serviço usando o portal do Azure. 

Para criar uma assinatura da grade de eventos do Azure, siga estas etapas:

1. No portal do Azure, vá para o namespace e, no painel esquerdo, selecione **eventos**. É aberta a janela do espaço de nomes, com duas subscrições do Event Grid no painel direito. 
    
    ![Barramento de serviço – página eventos](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Selecione **+ assinatura de evento** na barra de ferramentas. 
3. Na página **criar assinatura de evento** , execute as seguintes etapas:
    1. Insira um **nome** para a assinatura. 
    2. Selecione o **gancho da Web** para o **tipo de ponto de extremidade**. 

        ![Barramento de serviço-assinatura de grade de eventos](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Escolha **selecionar um ponto de extremidade**, Cole a URL da função e, em seguida, selecione **confirmar seleção**. 

        ![Função – selecione o ponto de extremidade](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Alterne para a guia **filtros** , insira o nome da **primeira assinatura** para o tópico do barramento de serviço criado anteriormente e, em seguida, selecione o botão **criar** . 

        ![Filtro de assinatura de evento](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Confirme que você vê a inscrição de evento na lista.

    ![Assinatura de evento na lista](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Enviar mensagens para o tópico do barramento de serviço
1. Execute o aplicativo C# .net, que envia mensagens para o tópico do barramento de serviço. 

    ![Saída do aplicativo de console](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na página do seu aplicativo de funções do Azure, expanda **funções**, expanda sua **função**e selecione **Monitor**. 

    ![Função monitor](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Receber mensagens através das Funções do Azure
Na secção anterior, observou um cenário simples de teste e depuração e garantiu que os eventos estão a fluir. 

Nesta secção, saiba como receber e processar mensagens depois de receber um evento.

### <a name="publish-a-function-from-visual-studio"></a>Publicar uma função do Visual Studio
1. Na mesma solução do Visual Studio (**SBEventGridIntegration**) que você abriu, selecione **ReceiveMessagesOnEvent.cs** no projeto **SBEventGridIntegration** . 
2. Insira a cadeia de conexão do barramento de serviço no código a seguir:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Baixe o **perfil de publicação** para a função:
    1. Selecione seu aplicativo de funções. 
    2. Selecione a guia **visão geral** se ela ainda não estiver selecionada. 
    3. Selecione **obter perfil de publicação** na barra de ferramentas. 

        ![Obter o perfil de publicação para a função](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Salve o arquivo na pasta do projeto. 
4. No Visual Studio, clique com o botão direito do rato em **SBEventGridIntegration** e, em seguida, selecione **Publicar**. 
5. Selecione **Iniciar** na página **publicar** . 
6. Na página **escolher um destino de publicação** , siga as etapas a seguir, selecione **importar perfil**. 

    ![Visual Studio – botão importar perfil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Selecione o **arquivo de perfil de publicação** que você baixou anteriormente. 
8. Selecione **publicar** na página **publicar** . 

    ![Visual Studio – publicar](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Confirme que você vê a nova função do Azure **ReceiveMessagesOnEvent**. Atualize a página, se necessário. 

    ![Confirmar que a nova função foi criada](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Obtenha a URL para a nova função e anote-a. 

### <a name="event-grid-subscription"></a>Assinatura da grade de eventos

1. Exclua a assinatura da grade de eventos existente:
    1. Na página **namespace do barramento de serviço** , selecione **eventos** no menu à esquerda. 
    2. Selecione a assinatura de evento existente. 
    3. Na página **assinatura de evento** , selecione **excluir**.
2. Siga as instruções na seção [conectar a função e o namespace por meio da grade de eventos](#connect-the-function-and-namespace-via-event-grid) para criar uma assinatura de grade de eventos usando a nova URL da função.
3. Siga a instrução na seção [enviar mensagens para o tópico do barramento de serviço](#send-messages-to-the-service-bus-topic) para enviar mensagens para o tópico e monitorar a função. 

## <a name="receive-messages-by-using-logic-apps"></a>Receber mensagens através do Logic Apps
Conecte um aplicativo lógico com o barramento de serviço do Azure e a grade de eventos do Azure seguindo estas etapas:

1. Crie um aplicativo lógico no portal do Azure.
    1. Selecione **+ criar um recurso**, selecione **integração**e, em seguida, selecione **aplicativo lógico**. 
    2. Na página **aplicativo lógico – criar** , insira um **nome** para o aplicativo lógico.
    3. Selecione a sua **subscrição** do Azure. 
    4. Selecione **usar existente** para o **grupo de recursos**e selecione o grupo de recursos que você usou para outros recursos (como Azure Function, namespace do barramento de serviço) que você criou anteriormente. 
    5. Selecione o **local** para o aplicativo lógico. 
    6. Selecione **criar** para criar o aplicativo lógico. 
2. Na página **Designer de aplicativos lógicos** , selecione **aplicativo lógico em branco** em **modelos**. 
3. No designer, execute as seguintes etapas:
    1. Pesquise a **grade de eventos**. 
    2. Selecione **quando ocorre um evento de recurso (visualização) – grade de eventos do Azure**. 

        ![Designer de aplicativos lógicos-selecionar gatilho de grade de eventos](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecione **entrar**, insira suas credenciais do Azure e selecione **permitir acesso**. 
5. Na página **quando um evento de recurso ocorre** , execute as seguintes etapas:
    1. Selecione a sua subscrição do Azure. 
    2. Para **tipo de recurso**, selecione **Microsoft. ServiceBus. Namespaces**. 
    3. Para **nome do recurso**, selecione o namespace do barramento de serviço. 
    4. Selecione **Adicionar novo parâmetro**e selecione **filtro de sufixo**. 
    5. Para **filtro de sufixo**, insira o nome da segunda assinatura de tópico do barramento de serviço. 
        ![Designer de aplicativos lógicos – configurar evento](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecione **+ nova etapa** no designer e execute as seguintes etapas:
    1. Pesquise pelo **barramento de serviço**.
    2. Selecione **barramento de serviço** na lista. 
    3. Selecione para **obter mensagens** na lista de **ações** . 
    4. Selecione **obter mensagens de uma assinatura de tópico (Peek-Lock)** . 

        ![Designer de aplicativos lógicos – ação obter mensagens](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Insira um **nome para a conexão**. Por exemplo: **Obtenha mensagens da assinatura do tópico**e selecione o namespace do barramento de serviço. 

        ![Designer de aplicativos lógicos – selecione o namespace do barramento de serviço](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selecione **RootManageSharedAccessKey**.

        ![Designer de aplicativos lógicos – selecione a chave de acesso compartilhado](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Selecione **Criar**. 
    8. Selecione seu tópico e assinatura. 
    
        ![Designer de aplicativos lógicos-selecione o tópico e a assinatura do barramento de serviço](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecione **+ nova etapa**e execute as seguintes etapas: 
    1. Selecione **Service Bus**.
    2. Selecione **concluir a mensagem em uma assinatura de tópico** na lista de ações. 
    3. Selecione o **tópico**do barramento de serviço.
    4. Selecione a segunda **assinatura** para o tópico.
    5. Para **token de bloqueio da mensagem**, selecione **Bloquear token** do **conteúdo dinâmico**. 

        ![Designer de aplicativos lógicos-selecione o tópico e a assinatura do barramento de serviço](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Selecione **salvar** na barra de ferramentas no designer de aplicativos lógicos para salvar o aplicativo lógico. 
9. Siga a instrução na seção [enviar mensagens para o tópico do barramento de serviço](#send-messages-to-the-service-bus-topic) para enviar mensagens para o tópico. 
10. Alterne para a página **visão geral** do seu aplicativo lógico. Você vê que o aplicativo lógico é executado no **histórico** de execuções das mensagens enviadas.

    ![Designer de aplicativos lógicos-execuções de aplicativo lógico](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
