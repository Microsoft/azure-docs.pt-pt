---
title: Monitor APIs com API Management, Event Hubs e Moesif
titleSuffix: Azure API Management
description: Aplicação de amostras demonstrando a política log-to-eventhub através da ligação azure API Management, Azure Event Hubs e Moesif para login e monitorização HTTP
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: 4a0717bf7a284668af4808acae3050cc7f42f836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442533"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitorize as suas APIs com a Azure API Management, Event Hubs e Moesif
O [serviço de Gestão API](api-management-key-concepts.md) fornece muitas capacidades para melhorar o processamento de pedidos HTTP enviados para a sua HTTP API. No entanto, a existência dos pedidos e respostas é transitória. O pedido é feito e flui através do serviço de Gestão API para a sua API backend. A sua API processa o pedido e uma resposta volta para o consumidor da API. O serviço de Gestão API mantém algumas estatísticas importantes sobre as APIs para exibição no portal Azure dashboard, mas além disso, os detalhes desapareceram.

Ao utilizar a política de log-to-eventhub no serviço de Gestão API, pode enviar quaisquer detalhes do pedido e resposta a um Hub de [Eventos Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Existem uma variedade de razões pelas quais pode querer gerar eventos a partir de mensagens HTTP que são enviadas para as suas APIs. Alguns exemplos incluem o rasto de auditoria de atualizações, análise de utilização, alerta de exceção e integrações de terceiros.

Este artigo demonstra como capturar toda a mensagem de pedido e resposta http, enviá-la para um Hub de Eventos e, em seguida, retransmitir essa mensagem para um serviço de terceiros que fornece serviços de registo e monitorização HTTP.

## <a name="why-send-from-api-management-service"></a>Porquê enviar do Serviço de Gestão da API?
É possível escrever um middleware HTTP que pode ligar-se aos quadros http API para capturar pedidos e respostas HTTP e alimentá-los em sistemas de registo e monitorização. A desvantagem desta abordagem é que o ambiente HTTP precisa de ser integrado na API de backend e deve corresponder à plataforma da API. Se houver múltiplas APIs, então cada um deve implantar o middleware. Muitas vezes existem razões pelas quais as APIs de backend não podem ser atualizadas.

A utilização do serviço azure API Management para integrar com a infraestrutura de exploração madeireira fornece uma solução centralizada e independente da plataforma. É também escalável, em parte devido às capacidades de [geo-replicação](api-management-howto-deploy-multi-region.md) da Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Por que enviar para um Hub de Eventos Azure?
É razoável perguntar, por que criar uma política específica para os Hubs de Eventos Azure? Há muitos lugares diferentes onde posso querer registar os meus pedidos. Por que não enviar os pedidos diretamente para o destino final?  É uma opção. No entanto, ao fazer pedidos de registo de um serviço de gestão da API, é necessário considerar como as mensagens de registo têm impacto no desempenho da API. Os aumentos graduais da carga podem ser tratados através do aumento das instâncias disponíveis de componentes do sistema ou aproveitando a geo-replicação. No entanto, os pequenos picos de tráfego podem fazer com que os pedidos sejam adiados se os pedidos de exploração de infraestruturas começarem a abrandar sob carga.

O Azure Event Hubs foi concebido para inecer enormes volumes de dados, com capacidade para lidar com um número muito maior de eventos do que o número de pedidos de HTTP a maioria dos processos de APIs. O Event Hub funciona como uma espécie de tampão sofisticado entre o seu serviço de gestão aPI e a infraestrutura que armazena e processa as mensagens. Isto garante que o seu desempenho na API não sofrerá devido à infraestrutura de exploração madeireira.

Uma vez que os dados foram passados para um Hub de Eventos, é persistiu e aguardará que os consumidores do Event Hub os processem. O Event Hub não se importa com a forma como é processado, apenas se preocupa em garantir que a mensagem será entregue com sucesso.

O Event Hubs tem a capacidade de transmitir eventos a vários grupos de consumidores. Isto permite que os eventos sejam processados por diferentes sistemas. Isto permite apoiar muitos cenários de integração sem colocar atrasos no processamento do pedido de API dentro do serviço de Gestão API, uma vez que apenas um evento precisa ser gerado.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Uma política para enviar mensagens de aplicação/http
Um Hub de Eventos aceita os dados do evento como uma simples corda. O conteúdo dessa corda depende de si. Para poder embalar um pedido HTTP e enviá-lo para O Hubs de Eventos, precisamos formatar a cadeia com as informações de pedido ou resposta. Em situações como esta, se houver um formato existente que possamos reutilizar, então podemos não ter de escrever o nosso próprio código de análise. Inicialmente, considerei usar o [HAR](http://www.softwareishard.com/blog/har-12-spec/) para o envio de pedidos e respostas http. No entanto, este formato é otimizado para armazenar uma sequência de pedidos HTTP num formato baseado em JSON. Continha uma série de elementos obrigatórios que adicionavam complexidade desnecessária para o cenário de passar a mensagem HTTP sobre o fio.

Uma opção alternativa `application/http` era utilizar o tipo de suporte, tal como descrito na especificação HTTP [RFC 7230](https://tools.ietf.org/html/rfc7230). Este tipo de mídia utiliza exatamente o mesmo formato que é usado para realmente enviar mensagens HTTP pelo fio, mas toda a mensagem pode ser colocada no corpo de outro pedido HTTP. No nosso caso, vamos usar o corpo como mensagem para enviar aos Centros de Eventos. Convenientemente, existe um parser que existe na Microsoft ASP.NET bibliotecas [de clientes Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) que podem analisar este formato e convertê-lo em nativos `HttpRequestMessage` e `HttpResponseMessage` objetos.

Para podermos criar esta mensagem, precisamos de aproveitar as [expressões políticas baseadas](/azure/api-management/api-management-policy-expressions) em C# na Gestão da API Azure. Aqui está a política, que envia uma mensagem de pedido http para o Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Declaração política
Há algumas coisas específicas que merecem ser mencionadas sobre esta expressão política. A política log-to-eventhub tem um atributo chamado logger-id, que se refere ao nome do madeireiro que foi criado dentro do serviço de Gestão API. Os detalhes de como configurar um logger do Event Hub no serviço de Gestão API podem ser encontrados no documento [Como registar eventos para O Azure Event Hubs em Azure API Management.](api-management-howto-log-event-hubs.md) O segundo atributo é um parâmetro opcional que instrui os Centros de Eventos em que divisória para armazenar a mensagem. Os Centros de Eventos usam divisórias para permitir a escalabilidade e requerem um mínimo de dois. A entrega ordenada de mensagens só é garantida dentro de uma partição. Se não instruímos o Event Hub em que a partição colocar a mensagem, ele usa um algoritmo de robin redondo para distribuir a carga. No entanto, isso pode fazer com que algumas das nossas mensagens sejam processadas fora de ordem.

### <a name="partitions"></a>Partições
Para garantir que as nossas mensagens são entregues aos consumidores em ordem e aproveitar a capacidade de distribuição de carga das divisórias, optei por enviar mensagens de pedido http a uma partição e mensagens de resposta HTTP para uma segunda partição. Isto garante uma distribuição uniforme de carga e podemos garantir que todos os pedidos serão consumidos por ordem e todas as respostas são consumidas por ordem. É possível que uma resposta seja consumida antes do pedido correspondente, mas como isso não é um problema, uma vez que dispomos de um mecanismo diferente para correlacionar os pedidos de respostas e sabemos que os pedidos vêm sempre antes das respostas.

### <a name="http-payloads"></a>CARGAS HTTP
Depois de `requestLine`construir o , verificamos se o corpo de pedidos deve ser truncado. O corpo de pedido está truncado para apenas 1024. Isto poderia ser aumentado, no entanto as mensagens individuais do Event Hub estão limitadas a 256 KB, pelo que é provável que alguns organismos de mensagens HTTP não encaixem numa única mensagem. Ao fazer a exploração madeireira e analítica, uma quantidade significativa de informação pode ser derivada apenas da linha de pedido http e cabeçalhos. Além disso, muitas APIs solicitam apenas devolver pequenos corpos e, por isso, a perda de valor da informação por truncar grandes corpos é bastante mínima em comparação com a redução dos custos de transferência, processamento e armazenamento para manter todos os conteúdos do corpo. Uma nota final sobre o processamento do `true` corpo `As<string>()` é que precisamos passar para o método porque estamos lendo o conteúdo do corpo, mas também queria que a API de backend fosse capaz de ler o corpo. Ao passarmos fiel a este método, fazemos com que o corpo seja tamponado para que possa ser lido uma segunda vez. Isto é importante estar ciente de que tem uma API que faz upload de ficheiros grandes ou usa sondagens longas. Nestes casos, seria melhor evitar ler o corpo.

### <a name="http-headers"></a>Cabeçalhos HTTP
Os cabeçalhos HTTP podem ser transferidos para o formato de mensagem num formato simples de par de chaves/valor. Optámos por desmantelar certos campos sensíveis à segurança, para evitar fugas de informação desnecessariamente credenciais. É pouco provável que as chaves API e outras credenciais sejam utilizadas para fins de análise. Se quisermos fazer análises ao utilizador e ao produto em particular `context` que estão a utilizar, então podemos obtê-lo a partir do objeto e adicioná-lo à mensagem.

### <a name="message-metadata"></a>Metadados de mensagem
Ao construir a mensagem completa para enviar para o centro do `application/http` evento, a primeira linha não faz parte da mensagem. A primeira linha são metadados adicionais que consistem em saber se a mensagem é uma mensagem de pedido ou resposta e um ID de mensagem, que é usado para correlacionar pedidos de respostas. O ID da mensagem é criado usando outra política que se parece com esta:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Poderíamos ter criado a mensagem de pedido, armazenando-a numa variável até que a resposta fosse devolvida e depois enviado o pedido e resposta como uma única mensagem. No entanto, enviando o pedido e a resposta de forma independente e usando um id de mensagem para correlacionar os dois, obtemos um pouco mais de flexibilidade no tamanho da mensagem, a capacidade de tirar partido de várias divisórias, mantendo a ordem da mensagem e o pedido aparecerá no nosso painel de login mais cedo. Pode também haver alguns cenários em que uma resposta válida nunca é enviada para o centro do evento, possivelmente devido a um erro fatal de pedido no serviço de Gestão API, mas ainda temos um registo do pedido.

A política de enviar a mensagem http resposta parece semelhante ao pedido e, por isso, a configuração de política completa é a seguinte:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

A `set-variable` política cria um valor acessível tanto `<inbound>` pela `<outbound>` `log-to-eventhub` política da secção como pela secção.

## <a name="receiving-events-from-event-hubs"></a>Receber eventos de Centros de Eventos
Os eventos do Azure Event Hub são recebidos usando o [protocolo AMQP.](https://www.amqp.org/) A equipa de ônibus da Microsoft Service disponibilizou bibliotecas de clientes para facilitar os eventos de consumo. Há duas abordagens diferentes apoiadas, uma é ser um `EventProcessorHost` Consumidor *Direto* e a outra está a usar a classe. Exemplos destas duas abordagens podem ser encontrados no Guia de Programação de Centros de [Eventos](../event-hubs/event-hubs-programming-guide.md). A versão curta das diferenças é que `Direct Consumer` `EventProcessorHost` dá-lhe controlo total e faz parte do trabalho de canalização para si, mas faz certas suposições sobre como processa esses eventos.

### <a name="eventprocessorhost"></a>EventProcessorHost
Nesta amostra, usamos `EventProcessorHost` a para a simplicidade, no entanto pode não ser a melhor escolha para este cenário em particular. `EventProcessorHost`faz o trabalho árduo de garantir que não tem que se preocupar com questões de threading dentro de uma determinada classe de processador de eventos. No entanto, no nosso cenário, estamos simplesmente a converter a mensagem para outro formato e transmiti-la para outro serviço usando um método de asincronização. Não há necessidade de atualizar o Estado partilhado e, portanto, não há qualquer risco de problemas de enroscar. Para a maioria `EventProcessorHost` dos cenários, é provavelmente a melhor escolha e é certamente a opção mais fácil.

### <a name="ieventprocessor"></a>Processador IEvent
O conceito central `EventProcessorHost` ao utilizar é `IEventProcessor` criar uma implementação `ProcessEventAsync`da interface, que contém o método . A essência deste método é mostrada aqui:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

Uma lista de objetos EventData é passada para o método e nós iteramos sobre essa lista. Os bytes de cada método são analisados num objeto HttpMessage e esse objeto é passado para uma instância do Processador IHttpMessage.

### <a name="httpmessage"></a>HttpMessage
A `HttpMessage` instância contém três dados:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

A `HttpMessage` instância `MessageId` contém um GUID que nos permite ligar o pedido HTTP à resposta httpleana correspondente e um valor booleano que identifica se o objeto contém uma instância de httpRequestMessage e HttpResponseMessage. Ao utilizar as classes HTTP `System.Net.Http`incorporadas de , pude `application/http` tirar partido do código `System.Net.Http.Formatting`de análise que está incluído em .  

### <a name="ihttpmessageprocessor"></a>Processador IHttpMessage
A `HttpMessage` instância é então `IHttpMessageProcessor`remetida para a implementação de , que é uma interface que criei para dissociar a receção e interpretação do evento do Azure Event Hub e o processamento real do mesmo.

## <a name="forwarding-the-http-message"></a>Reencaminhamento da mensagem HTTP
Para esta amostra, decidi que seria interessante empurrar o pedido http para a [Moesif API Analytics.](https://www.moesif.com) O Moesif é um serviço baseado em nuvem especializado em análise seletiva http e depuração. Eles têm um nível livre, por isso é fácil de tentar e permite-nos ver os pedidos http em tempo real fluindo através do nosso serviço de Gestão API.

A `IHttpMessageProcessor` implementação é assim,

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

A `MoesifHttpMessageProcessor` biblioteca [C# API para o Moesif](https://www.moesif.com/docs/api?csharp#events) que facilita a entrada de dados do evento HTTP no seu serviço. Para enviar dados http para a API do Colecionador Moesif, precisa de uma conta e de um Id de aplicação. Obtém um Id de aplicação do Moesif criando uma conta no [site do Moesif](https://www.moesif.com) e depois vai para a configuração da app de menu ->  _sinuoso.__App Setup_

## <a name="complete-sample"></a>Amostra completa
O [código-fonte](https://github.com/dgilling/ApimEventProcessor) e os testes para a amostra estão no GitHub. Você precisa de um Serviço de [Gestão API,](get-started-create-service-instance.md)um Hub de [Eventos conectado,](api-management-howto-log-event-hubs.md)e uma Conta de [Armazenamento](../storage/common/storage-create-storage-account.md) para executar a amostra por si mesmo.   

A amostra é apenas uma simples aplicação consola que ouve eventos vindos do Event Hub, converte-os em moesif `EventRequestModel` e `EventResponseModel` objetos e, em seguida, encaminha-os para a API de Colecionador Moesif.

Na imagem animada seguinte, pode ver um pedido a ser feito para uma API no Portal do Desenvolvimento, a aplicação Consola mostrando a mensagem a ser recebida, processada e reencaminhada e, em seguida, o pedido e resposta que aparece no Fluxo de Eventos.

![Demonstração de pedido a ser encaminhado para Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Resumo
O serviço azure API Management fornece um local ideal para capturar o tráfego HTTP que viaja de e para as suas APIs. O Azure Event Hubs é uma solução altamente escalável e de baixo custo para capturar esse tráfego e alimentá-lo em sistemas de processamento secundáriopara abate, monitorização e outras análises sofisticadas. Ligar-se a sistemas de monitorização de tráfego de terceiros como o Moesif é tão simples como algumas dezenas de linhas de código.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os Hubs de Eventos Azure
  * [Começar com hubs de eventos Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com o EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração de Centros de Gestão e Eventos da API
  * [Como registar eventos para hubs de eventos Azure na Gestão da API Azure](api-management-howto-log-event-hubs.md)
  * [Referência da entidade logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referência política log-to-eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
