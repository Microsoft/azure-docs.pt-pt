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
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: spelluru
ms.openlocfilehash: 4e1ea3d822c8b032617b7f202f1c176aeb966210
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436783"
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Exemplos de integração do Azure Service Bus no Azure Event Grid

Neste artigo, saiba como configurar uma função do Azure e uma aplicação lógica que recebem mensagens com base na receção de eventos do Azure Event Grid. Terá de efetuar o seguinte:
 
* Crie uma função do Azure de teste simple para depurar e ver o fluxo inicial de eventos do Event Grid. Efetue este passo, independentemente de executar os restantes.
* Crie uma função do Azure para receber e processar mensagens do Service bus do Azure com base em eventos do Event Grid.
* Utilize a funcionalidade de aplicações lógicas do serviço de aplicações do Azure.

O exemplo que criar pressupõe que o tópico do Service Bus tem duas subscrições. O exemplo também pressupõe que a subscrição do Event Grid foi criada para enviar eventos apenas para uma subscrição do Service Bus. 

No exemplo, vai enviar mensagens para o tópico do Service Bus e verificar se o evento foi gerado para esta subscrição do Service Bus. A função ou aplicação lógica recebe as mensagens da subscrição do Service Bus e completa-as.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que concluiu os passos nas duas secções seguintes.

### <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus

Crie um espaço de nomes Premium do Service Bus e um tópico do Service Bus com duas subscrições.

### <a name="send-a-message-to-the-service-bus-topic"></a>Enviar uma mensagem para o tópico do Service Bus

Pode utilizar qualquer método para enviar uma mensagem para o tópico do Service Bus. O código de exemplo no final deste procedimento pressupõe que está a utilizar o Visual Studio 2017.

1. Clone [o repositório azure-service-bus do GitHub](https://github.com/Azure/azure-service-bus/).

1. No Visual Studio, aceda à pasta *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* e abra o ficheiro *SBEventGridIntegration.sln*.

1. Aceda ao projeto **MessageSender** e selecione **Program.cs**.

   ![8][]

1. Preencha o nome do tópico e a cadeia de ligação, e execute o seguinte código da aplicação da consola:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Configurar uma função de teste

Antes de trabalhar em todo o cenário, configure pelo menos uma pequena função de teste, que pode utilizar para depurar e observar que eventos estão a fluir.

1. No portal do Azure, crie uma nova aplicação de Funções do Azure. Para aprender as noções básicas das Funções do Azure, veja a [documentação das Funções do Azure](https://docs.microsoft.com/azure/azure-functions/).

1. Na função acabada de criar, selecione o sinal de adição (+) para adicionar uma função de acionador HTTP:

    ![2][]
    
    É aberta a janela **Começar a trabalhar agora com uma função pré-criada**.

    ![3][]

1. Selecione o botão **Webhook + API**, selecione **CSharp** e, em seguida, **Criar esta função**.
 
1. Na função, cole o seguinte código:

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

1. Selecione **Guardar e executar**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Ligar a função e o espaço de nomes através do Event Grid

Nesta secção, vai ligar a função ao espaço de nomes do Service Bus. Neste exemplo, utilize o portal do Azure. Para compreender como utilizar o PowerShell ou a CLI do Azure para efetuar este procedimento, veja [Descrição geral de integração do Azure Service Bus no Azure Event Grid](service-bus-to-event-grid-integration-concept.md).

Para criar uma subscrição do Azure Event Grid, efetue o seguinte:
1. No portal do Azure, aceda ao espaço de nomes e, no painel esquerdo, selecione **Event Grid**.  
    É aberta a janela do espaço de nomes, com duas subscrições do Event Grid no painel direito.

    ![20][]

1. Selecione **Subscrição de Eventos**.  
    É aberta a janela **Subscrição de Eventos**. A imagem seguinte mostra um formulário para subscrever um webhook ou uma função do Azure sem aplicar filtros.

    ![21][]

1. Preencha o formulário conforme apresentado e, na caixa **Filtro de Sufixo**, introduza o filtro relevante.

1. Selecione **Criar**.

1. Envie uma mensagem para o tópico do Service Bus, conforme mencionado na secção "Pré-requisitos" e certifique-se de que os eventos estão a fluir através da funcionalidade Monitorização de Funções do Azure.

O próximo passo é ligar a função ao espaço de nomes do Service Bus. Neste exemplo, utilize o portal do Azure. Para compreender como utilizar o PowerShell ou a CLI do Azure para efetuar este passo, veja [Descrição geral de integração do Azure Service Bus no Azure Event Grid](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Receber mensagens através das Funções do Azure

Na secção anterior, observou um cenário simples de teste e depuração e garantiu que os eventos estão a fluir. 

Nesta secção, saiba como receber e processar mensagens depois de receber um evento.

Vai adicionar uma função do Azure, conforme mostrado no exemplo seguinte, porque as funções do Service Bus nas Funções do Azure ainda não dão suporte nativo à nova integração no Event Grid.

1. Na mesma Solução do Visual Studio que abriu nos pré-requisitos, selecione **ReceiveMessagesOnEvent.cs**. 

    ![10][]

1. Introduza a cadeia de ligação no seguinte código:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

1. No portal do Azure, transfira o perfil de publicação da função do Azure que criou na secção "Configurar uma função de teste".

    ![11][]

1. No Visual Studio, clique com o botão direito do rato em **SBEventGridIntegration** e, em seguida, selecione **Publicar**. 

1. No painel **Publicar** do perfil de publicação que transferiu anteriormente, selecione **Importar perfil** e, em seguida, selecione **Publicar**.

    ![12][]

1. Após publicar a nova função do Azure, crie uma nova subscrição do Azure Event Grid que aponte para a nova função do Azure.  
    Na caixa **Termina em**, certifique-se de que aplica o filtro correto, que deve ser o Nome da subscrição do Service Bus.

1. Envie uma mensagem para o tópico do Azure Service Bus que criou anteriormente e monitorize o registo de Funções do Azure no portal para assegurar que os eventos estão a fluir e as mensagens estão a ser recebidas.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Receber mensagens através do Logic Apps

Ligue uma aplicação lógica ao Azure Service Bus e ao Azure Event Grid da seguinte forma:

1. Crie uma nova aplicação lógica no portal do Azure e selecione **Event Grid** como ação inicial.

    ![13][]

    É aberta a janela do estruturador do Logic Apps.

    ![14][]

1. Adicione as suas informações da seguinte forma:

    a. Na caixa **Nome do Recurso**, introduza o seu próprio nome de espaço de nomes. 

    b. Em **Opções avançadas**, na caixa **Filtro de Sufixo**, introduza o filtro para a sua subscrição.

1. Adicione uma ação de receção do Service Bus para receber mensagens de uma subscrição de tópico.  
    A ação final é mostrada na imagem seguinte:

    ![15][]

1. Adicione um evento completo, conforme mostrado na imagem seguinte:

    ![16][]

1. Guarde a aplicação lógica e envie uma mensagem para o tópico do Service Bus, conforme mencionado na secção "Pré-requisitos".  
    Observe a execução da aplicação lógica. Para ver mais dados para a execução, selecione **Descrição geral** e, em seguida, veja os dados em **Histórico de execuções**.

    ![17][]

    ![18][]

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
