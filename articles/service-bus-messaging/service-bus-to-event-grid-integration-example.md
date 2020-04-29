---
title: 'Tutorial: Ônibus de serviço azure para exemplos de integração da Rede de Eventos'
description: 'Tutorial: Este artigo fornece exemplos de mensagens de ônibus de serviço e integração da Rede de Eventos.'
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
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: fef325b67c38eda09a05dac9d74bd5b97df164cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067773"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Tutorial: Responda aos eventos de ônibus de serviço azure recebidos via Azure Event Grid usando funções Azure e aplicações lógicas azure
Neste tutorial, você aprende a responder a eventos azure service bus que são recebidos via Azure Event Grid usando funções Azure e Aplicações lógicas Azure. 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes do Service Bus
> * Preparar um pedido de amostra para enviar mensagens
> * Configurar uma função de teste no Azure
> * Ligar a função e o espaço de nomes através do Event Grid
> * Envie mensagens para o tópico do Ônibus de serviço
> * Receber mensagens através das Funções do Azure
> * Receber mensagens através do Logic Apps

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem instalada:

- [Visual Studio 2017 Atualização 3 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
- [SDK NET Core](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus
Siga as instruções neste tutorial: [Quickstart: Use o portal Azure para criar um tópico de ônibus de serviço e subscrições para o tópico](service-bus-quickstart-topics-subscriptions-portal.md) para fazer as seguintes tarefas:

- Crie **um** espaço de nome premium Service Bus. 
- Pegue a corda de ligação. 
- Crie um tópico de ônibus de serviço.
- Crie duas assinaturas para o tópico. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Preparar um pedido de amostra para enviar mensagens
Pode utilizar qualquer método para enviar uma mensagem para o tópico do Service Bus. O código da amostra no final deste procedimento pressupõe que está a usar o Visual Studio 2017.

1. Clone [o repositório azure-service-bus do GitHub](https://github.com/Azure/azure-service-bus/).
2. No Visual Studio, aceda à pasta *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* e abra o ficheiro *SBEventGridIntegration.sln*.
3. Aceda ao projeto **MessageSender** e selecione **Program.cs**.
4. Preencha o nome tópico do Bus de Serviço e a corda de ligação que obteve do passo anterior:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Construa e execute o programa para enviar mensagens de teste para o tópico do Ônibus de serviço. 

## <a name="set-up-a-test-function-on-azure"></a>Configurar uma função de teste no Azure 
Antes de trabalhar em todo o cenário, instale pelo menos uma pequena função de teste, que pode usar para depurar e observar os eventos que estão a fluir. Siga as instruções no [Create your first function in the Azure portal](../azure-functions/functions-create-first-azure-function.md) article to do dispor: 

1. Crie uma aplicação de função.
2. Criar uma função ativada em HTTP. 

Em seguida, faça os seguintes passos: 


# <a name="azure-functions-v2"></a>[Funções Azure V2](#tab/v2)

1. Expandir **funções** na vista da árvore e selecionar a sua função. Substitua o código pela função com o seguinte código: 

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

    ![Saída de aplicativo de função](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Selecione **Obter URL de função** e note o URL. 

    ![Obter URL de função](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1"></a>[Funções Azure V1](#tab/v1)

1. Configure a função para utilizar a versão **V1:** 
    1. Selecione a sua aplicação de função na vista para a árvore e selecione as definições da **aplicação Função**. 

        ![Definições da Aplicação de funções]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Selecione **~1** para **versão Runtime**. 
2. Expandir **funções** na vista da árvore e selecionar a sua função. Substitua o código pela função com o seguinte código: 

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
4. Selecione **Obter URL de função** e note o URL. 

    ![Obter URL de função](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Ligar a função e o espaço de nomes através do Event Grid
Nesta secção, une a função e o espaço de nome do Autocarro de Serviço utilizando o portal Azure. 

Para criar uma subscrição da Rede de Eventos Azure, siga estes passos:

1. No portal Azure, vá ao seu espaço de nome e, em seguida, no painel esquerdo, selecione **Eventos**. É aberta a janela do espaço de nomes, com duas subscrições do Event Grid no painel direito. 
    
    ![Service Bus - página de eventos](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Selecione **+ Subscrição de eventos** na barra de ferramentas. 
3. Na página de Subscrição de **Eventos Create,** faça os seguintes passos:
    1. Insira um **nome** para a subscrição. 
    2. Selecione **web hook** para **endpoint type**. 

        ![Ônibus de serviço - assinatura da Rede de Eventos](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Escolha **Selecione um ponto final,** colhe o URL da função e, em seguida, selecione **confirmar a seleção**. 

        ![Função - selecione o ponto final](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Mude para o separador **Filtros,** introduza o nome da **primeira subscrição** do tópico do Bus de Serviço que criou anteriormente e, em seguida, selecione o botão **Criar.** 

        ![Filtro de subscrição de eventos](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Confirme que vê a subscrição do evento na lista.

    ![Assinatura de evento na lista](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Envie mensagens para o tópico do Ônibus de serviço
1. Executar a aplicação .NET C#, que envia mensagens para o tópico do Ônibus de serviço. 

    ![Saída de aplicativo de consola](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na página da sua aplicação de função Azure, expanda **funções,** expanda a sua **função**e selecione **Monitor**. 

    ![Função de monitor](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Receber mensagens através das Funções do Azure
Na secção anterior, observou um cenário simples de teste e depuração e garantiu que os eventos estão a fluir. 

Nesta secção, saiba como receber e processar mensagens depois de receber um evento.

### <a name="publish-a-function-from-visual-studio"></a>Publique uma função do Estúdio Visual
1. Na mesma solução de Estúdio Visual **(SBEventGridIntegration)** que abriu, selecione **ReceiveMessagesOnEvent.cs** no projeto **SBEventGridIntegration.** 
2. Introduza a sua cadeia de ligação de ônibus de serviço no seguinte código:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Descarregue o perfil de **publicação** para a função:
    1. Selecione a sua aplicação de funções. 
    2. Selecione o separador **'Visão Geral'** se ainda não estiver selecionado. 
    3. Selecione **Obter perfil de publicação** na barra de ferramentas. 

        ![Obtenha o perfil de publicação para a função](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Guarde o ficheiro para a pasta do seu projeto. 
4. No Visual Studio, clique com o botão direito do rato em **SBEventGridIntegration** e, em seguida, selecione **Publicar**. 
5. Selecione **Iniciar** na página **Publicar.** 
6. Na **página-alvo pick a publish,** faça os seguintes passos, selecione **Import Profile**. 

    ![Estúdio Visual - Botão de Perfil de Importação](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Selecione o ficheiro de perfil de **publicação** que descarregou anteriormente. 
8. Selecione **Publicar** na página **Publicar.** 

    ![Estúdio Visual - Publicar](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Confirme que vê a nova função Azure **ReceiveMessagesOnEvent**. Refresque a página, se necessário. 

    ![Confirme que a nova função é criada](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Faça o URL para a nova função e note-o. 

### <a name="event-grid-subscription"></a>Assinatura da Grelha de Eventos

1. Eliminar a subscrição da Grelha de Eventos existente:
    1. Na página **Service Bus Namespace,** selecione **Eventos** no menu esquerdo. 
    2. Selecione a subscrição do evento existente. 
    3. Na página **de Subscrição** do Evento, selecione **Eliminar**.
2. Siga as instruções na [Ligação da função e](#connect-the-function-and-namespace-via-event-grid) do espaço de nome através da secção Rede de Eventos para criar uma subscrição da Grelha de Eventos utilizando o URL da nova função.
3. Siga as instruções na secção [enviar mensagens para a](#send-messages-to-the-service-bus-topic) secção tópico do Ônibus de serviço para enviar mensagens para o tópico e monitorizar a função. 

## <a name="receive-messages-by-using-logic-apps"></a>Receber mensagens através do Logic Apps
Ligue uma aplicação lógica com o Azure Service Bus e a Azure Event Grid seguindo estes passos:

1. Crie uma aplicação lógica no portal Azure.
    1. Selecione **+ Criar um recurso,** selecione **Integração,** e depois selecione **Logic App**. 
    2. Na **Aplicação Lógica - Criar** página, insira um **nome** para a aplicação lógica.
    3. Selecione a sua **subscrição** do Azure. 
    4. Selecione **Use existente** para o **grupo Recursos**, e selecione o grupo de recursos que utilizou para outros recursos (como função Azure, espaço de nome service Bus) que criou anteriormente. 
    5. Selecione o **Local** para a aplicação lógica. 
    6. Selecione **Criar** para criar a aplicação lógica. 
2. Na página de Designer de **Aplicações Lógicas,** selecione **App lógica em branco** em **modelos**. 
3. No designer, faça os seguintes passos:
    1. Pesquisa por **Grelha de Eventos**. 
    2. Selecione Quando ocorrer um evento de **recurso (pré-visualização) - Grelha de Eventos Azure**. 

        ![Logic Apps Designer - selecione Trigger da Grelha de Eventos](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecione **Iniciar sessão,** insira as suas credenciais Azure e selecione **Permitir o Acesso**. 
5. Na página Quando ocorre um evento de recursos, faça os **seguintes** passos:
    1. Selecione a sua subscrição do Azure. 
    2. Para **o Tipo de Recursos,** selecione **Microsoft.ServiceBus.Namespaces**. 
    3. Para **nome de recurso,** selecione o seu espaço de nome do Ônibus de serviço. 
    4. **Selecione Adicionar novo parâmetro,** e selecione **Filtro sufixo**. 
    5. Para filtro **sufixo,** insira o nome da sua segunda subscrição de tópico de ônibus de serviço. 
        ![Logic Apps Designer - evento de configuração](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecione **+ Novo Passo** no designer e faça os seguintes passos:
    1. Pesquisa por **Ônibus de serviço**.
    2. Selecione **Service Bus** na lista. 
    3. Selecione para **Obter mensagens** na lista **de Ações.** 
    4. Selecione **Obter mensagens a partir de uma subscrição de tópicos (peek-lock)**. 

        ![Logic Apps Designer - obtenha ação de mensagens](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Introduza um **nome para a ligação**. Por exemplo: **Obtenha mensagens da subscrição do tópico**e selecione o espaço de nome do Ônibus de serviço. 

        ![Logic Apps Designer - selecione o espaço de nome do Ônibus de serviço](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selecione **RootManageSharedAccessKey**.

        ![Logic Apps Designer - selecione a chave de acesso partilhado](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Selecione **Criar**. 
    8. Selecione o seu tópico e subscrição. 
    
        ![Logic Apps Designer - selecione o tópico e subscrição do seu Ônibus de serviço](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecione **+ Novo passo,** e faça os seguintes passos: 
    1. Selecione **Service Bus**.
    2. Selecione **Complete a mensagem numa subscrição** de tópico sonâmlo da lista de ações. 
    3. Selecione o **tópico**do ônibus de serviço .
    4. Selecione a segunda **subscrição** do tópico.
    5. Para **bloquear o símbolo da mensagem,** selecione Lock **Token** do **conteúdo Dinâmico**. 

        ![Logic Apps Designer - selecione o tópico e subscrição do seu Ônibus de serviço](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Selecione **Guardar** na barra de ferramentas no Logic Apps Designer para salvar a aplicação lógica. 
9. Siga as instruções na secção enviar mensagens para a secção de [tópicos do Ônibus de serviço](#send-messages-to-the-service-bus-topic) para enviar mensagens para o tópico. 
10. Mude para a página **de visão geral** da sua aplicação lógica. Vê-se a aplicação lógica que corre na história dos **Runs** para as mensagens enviadas.

    ![Logic Apps Designer - aplicação lógica funciona](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Passos seguintes

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
