---
title: Monitor APIs com Azure API Management, Event Hubs e Moesif
titleSuffix: Azure API Management
description: Aplicação de amostra que demonstra a política de log-to-eventhub ligando a Azure API Management, Azure Event Hubs e Moesif para registo e monitorização HTTP
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
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: abb9cbb73f8957cec2cb3240bbf186623b9b2ef9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88205505"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitorize as suas APIs com Azure API Management, Event Hubs e Moesif
O [serviço de Gestão API](api-management-key-concepts.md) oferece muitas capacidades para melhorar o processamento de pedidos HTTP enviados para a sua API HTTP. No entanto, a existência dos pedidos e respostas é transitória. O pedido é feito e flui através do serviço de Gestão da API para a sua API backend. A sua API processa o pedido e uma resposta flui para o consumidor da API. O serviço de Gestão da API mantém algumas estatísticas importantes sobre as APIs para exibição no painel do portal Azure, mas além disso, os detalhes desapareceram.

Ao utilizar a política de log-to-eventhub no serviço de Gestão API, pode enviar quaisquer detalhes do pedido e resposta a um [Azure Event Hub](../event-hubs/event-hubs-about.md). Existem uma variedade de razões pelas quais poderá querer gerar eventos a partir de mensagens HTTP que estão a ser enviadas para as suas APIs. Alguns exemplos incluem o rasto de auditoria de atualizações, análise de utilização, alerta de exceções e integrações de terceiros.

Este artigo demonstra como capturar toda a mensagem de pedido e resposta HTTP, enviá-la para um Centro de Eventos e, em seguida, transmitir essa mensagem a um serviço de terceiros que fornece serviços de registo e monitorização HTTP.

## <a name="why-send-from-api-management-service"></a>Porquê enviar do Serviço de Gestão da API?
É possível escrever middleware HTTP que pode ligar-se a quadros http API para capturar pedidos e respostas HTTP e alimentá-los em sistemas de registo e monitorização. O lado negativo desta abordagem é que o middleware HTTP precisa de ser integrado na API de backend e deve corresponder à plataforma da API. Se houver várias APIs, cada um deve implantar o middleware. Muitas vezes existem razões pelas quais as APIs de backend não podem ser atualizadas.

A utilização do serviço de Gestão API da Azure para integrar-se com a infraestrutura de exploração madeireira proporciona uma solução centralizada e independente da plataforma. É também escalável, em parte devido às capacidades [de geo-replicação](api-management-howto-deploy-multi-region.md) da Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Por que enviar para um Azure Event Hub?
É razoável perguntar, por que criar uma política específica para a Azure Event Hubs? Há muitos lugares diferentes onde eu gostaria de registar os meus pedidos. Por que não enviar os pedidos diretamente para o destino final?  É uma opção. No entanto, ao fazer pedidos de registo de um serviço de gestão da API, é necessário considerar como as mensagens de registo impactam o desempenho da API. Os aumentos graduais da carga podem ser manuseados aumentando as instâncias disponíveis de componentes do sistema ou tirando partido da geo-replicação. No entanto, os curtos picos de tráfego podem fazer com que os pedidos de registo de infraestruturas comecem a abrandar.

O Azure Event Hubs foi projetado para ingressar enormes volumes de dados, com capacidade para lidar com um número muito maior de eventos do que o número de pedidos HTTP mais processo APIs. O Centro de Eventos funciona como uma espécie de tampão sofisticado entre o seu serviço de gestão API e a infraestrutura que armazena e processa as mensagens. Isto garante que o seu desempenho da API não sofrerá devido à infraestrutura de exploração madeireira.

Uma vez que os dados foram passados para um Centro de Eventos, este é persistido e irá esperar que os consumidores do Event Hub o processem. O Centro de Eventos não se importa com a forma como é processado, apenas se preocupa em garantir que a mensagem será entregue com sucesso.

O Event Hubs tem a capacidade de transmitir eventos a vários grupos de consumidores. Isto permite que os eventos sejam processados por diferentes sistemas. Isto permite apoiar muitos cenários de integração sem colocar atrasos no processamento do pedido de API no âmbito do serviço de Gestão da API, uma vez que apenas um evento precisa de ser gerado.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Uma política para enviar mensagens de aplicação/http
Um Event Hub aceita dados de eventos como uma simples cadeia. O conteúdo dessa corda depende de si. Para poder embalar um pedido HTTP e enviá-lo para Os Centros de Eventos, precisamos de formatar a cadeia com as informações de pedido ou resposta. Em situações como esta, se houver um formato existente que possamos reutilizar, então talvez não tenhamos de escrever o nosso próprio código de análise. Inicialmente, considerei utilizar o [HAR](http://www.softwareishard.com/blog/har-12-spec/) para enviar pedidos e respostas HTTP. No entanto, este formato está otimizado para armazenar uma sequência de pedidos HTTP num formato baseado em JSON. Continha uma série de elementos obrigatórios que adicionavam complexidade desnecessária para o cenário de passar a mensagem HTTP pelo fio.

Uma opção alternativa foi utilizar o `application/http` tipo de suporte conforme descrito na especificação HTTP [RFC 7230](https://tools.ietf.org/html/rfc7230). Este tipo de mídia utiliza exatamente o mesmo formato que é realmente utilizado para enviar mensagens HTTP por fio, mas toda a mensagem pode ser colocada no corpo de outro pedido HTTP. No nosso caso, vamos usar o corpo como mensagem para enviar aos Centros de Eventos. Convenientemente, existe um parser que existe na Microsoft ASP.NET bibliotecas [de clientes Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) que podem analisar este formato e convertê-lo em `HttpRequestMessage` nativos e `HttpResponseMessage` objetos.

Para podermos criar esta mensagem, precisamos de aproveitar [as expressões](./api-management-policy-expressions.md) de Política C# baseadas na Azure API Management. Aqui está a política, que envia uma mensagem http solicitação para Azure Event Hubs.

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
Há algumas coisas específicas que merecem ser mencionadas sobre esta expressão política. A política log-to-eventhub tem um atributo chamado logger-id, que se refere ao nome do madeireiro que foi criado dentro do serviço de Gestão da API. Os detalhes de como configurar um contato de Event Hub no serviço de Gestão API podem ser encontrados no documento [Como registar eventos para Azure Event Hubs na Azure API Management.](api-management-howto-log-event-hubs.md) O segundo atributo é um parâmetro opcional que instrui os Centros de Eventos que se separam para armazenar a mensagem. O Event Hubs utiliza divisórias para permitir a escalabilidade e requer um mínimo de duas. A entrega ordenada de mensagens só é garantida dentro de uma partição. Se não instruírmos o Event Hub em que partição para colocar a mensagem, ele usa um algoritmo de robin redondo para distribuir a carga. No entanto, isso pode fazer com que algumas das nossas mensagens sejam processadas fora de ordem.

### <a name="partitions"></a>Partições
Para garantir que as nossas mensagens são entregues aos consumidores em ordem e tirar partido da capacidade de distribuição de carga das divisórias, optei por enviar mensagens http a uma partição e mensagens de resposta HTTP para uma segunda partilha. Isto garante uma distribuição uniforme da carga e podemos garantir que todos os pedidos serão consumidos em ordem e todas as respostas são consumidas em ordem. É possível que uma resposta seja consumida antes do pedido correspondente, mas como isso não é um problema, uma vez que temos um mecanismo diferente de correlação de pedidos de respostas e sabemos que os pedidos vêm sempre antes das respostas.

### <a name="http-payloads"></a>Cargas HTTP
Depois de construir `requestLine` o, verificamos se o corpo do pedido deve ser truncado. O corpo de pedido é truncado para apenas 1024. Isto poderia ser aumentado, no entanto as mensagens individuais do Event Hub estão limitadas a 256 KB, pelo que é provável que alguns organismos de mensagens HTTP não se encaixem numa única mensagem. Ao fazer registos e análises, uma quantidade significativa de informação pode ser obtida apenas a partir da linha de pedido HTTP e dos cabeçalhos. Além disso, muitas APIs solicitam apenas a devolução de pequenos corpos e, por isso, a perda de valor da informação através da truncar grandes corpos é relativamente mínima em comparação com a redução dos custos de transferência, processamento e armazenamento para manter todo o conteúdo corporal. Uma nota final sobre o processamento do corpo é que precisamos passar `true` para o método porque `As<string>()` estamos lendo o conteúdo do corpo, mas também era queria que a API de backend fosse capaz de ler o corpo. Ao passar fiel a este método, fazemos com que o corpo seja tamponado para que possa ser lido uma segunda vez. Isto é importante para estar ciente se você tem uma API que faz upload de grandes ficheiros ou usa sondagens longas. Nestes casos, seria melhor evitar ler o corpo.

### <a name="http-headers"></a>Cabeçalhos HTTP
Os cabeçalhos HTTP podem ser transferidos para o formato de mensagem num formato simples de par chave/valor. Optámos por desmantelar certos campos sensíveis à segurança, para evitar fugas desnecessariamente de informação credencial. É pouco provável que as chaves da API e outras credenciais sejam utilizadas para fins de análise. Se quisermos fazer análises ao utilizador e ao produto em particular que estão a usar, então podemos obtê-lo do `context` objeto e adicioná-lo à mensagem.

### <a name="message-metadata"></a>Metadados de mensagens
Ao construir a mensagem completa para enviar para o centro de eventos, a primeira linha não faz parte da `application/http` mensagem. A primeira linha é metadados adicionais que consistem em saber se a mensagem é um pedido ou mensagem de resposta e um ID de mensagem, que é usado para correlacionar pedidos de respostas. O ID da mensagem é criado usando outra política que se parece com esta:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Poderíamos ter criado a mensagem de pedido, armazenado que numa variável até que a resposta fosse devolvida e depois enviado o pedido e resposta como uma única mensagem. No entanto, ao enviar o pedido e a resposta de forma independente e usando um id de mensagem para correlacionar os dois, obtemos um pouco mais de flexibilidade no tamanho da mensagem, a capacidade de tirar partido de múltiplas divisórias, mantendo a ordem de mensagem e o pedido aparecerá no nosso painel de registo mais cedo. Também pode haver alguns cenários em que uma resposta válida nunca é enviada para o centro de eventos, possivelmente devido a um erro de pedido fatal no serviço de Gestão da API, mas ainda temos um registo do pedido.

A política de envio da mensagem HTTP de resposta é semelhante ao pedido e assim a configuração completa da política é semelhante:

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

A `set-variable` política cria um valor acessível tanto pela política na secção como na `log-to-eventhub` `<inbound>` `<outbound>` secção.

## <a name="receiving-events-from-event-hubs"></a>Receber eventos dos Centros de Eventos
Os eventos do Azure Event Hub são recebidos usando o [protocolo AMQP](https://www.amqp.org/). A equipa da Microsoft Service Bus disponibilizou bibliotecas de clientes para facilitar os eventos de consumo. Há duas abordagens diferentes apoiadas, uma é ser *um Consumidor Direto* e a outra está a usar a `EventProcessorHost` classe. Exemplos destas duas abordagens podem ser encontrados no Guia de Programação de [Centros de Eventos.](../event-hubs/event-hubs-programming-guide.md) A versão curta das diferenças é, `Direct Consumer` dá-lhe controlo total e `EventProcessorHost` faz alguns dos trabalhos de canalização para si, mas faz certos pressupostos sobre como processa esses eventos.

### <a name="eventprocessorhost"></a>EventProcessorHost
Nesta amostra, usamos a `EventProcessorHost` simplicidade, no entanto pode não ser a melhor escolha para este cenário em particular. `EventProcessorHost` faz o trabalho árduo de garantir que não tem que se preocupar com problemas de roscar dentro de uma determinada classe de processador de eventos. No entanto, no nosso cenário, estamos simplesmente a converter a mensagem para outro formato e a passá-la para outro serviço usando um método async. Não há necessidade de atualizar o estado partilhado e, por conseguinte, não existe qualquer risco de problemas de rosca. Para a maioria dos cenários, `EventProcessorHost` é provavelmente a melhor escolha e é certamente a opção mais fácil.

### <a name="ieventprocessor"></a>IEventProcessor
O conceito central ao utilizar `EventProcessorHost` é criar uma implementação da `IEventProcessor` interface, que contém o `ProcessEventAsync` método. A essência deste método é mostrada aqui:

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

Uma lista de objetos EventData são passados para o método e nós iteramos sobre essa lista. Os bytes de cada método são analisados num objeto HttpMessage e esse objeto é passado para um exemplo de IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
O `HttpMessage` caso contém três pedaços de dados:

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

O `HttpMessage` caso contém um GUID que nos permite ligar o pedido HTTP à resposta HTTP correspondente e um valor `MessageId` booleano que identifica se o objeto contiver uma instância de um HttpRequestMessage e HttpResponseMessage. Ao utilizar as aulas HTTP `System.Net.Http` incorporadas, pude tirar partido do código de análise que está incluído em `application/http` `System.Net.Http.Formatting` .  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
O `HttpMessage` caso é então encaminhado para a implementação de `IHttpMessageProcessor` , que é uma interface que criei para dissociar a receção e interpretação do evento do Azure Event Hub e o processamento real do mesmo.

## <a name="forwarding-the-http-message"></a>Reencaminhamento da mensagem HTTP
Para esta amostra, decidi que seria interessante passar o pedido HTTP para a [Moesif API Analytics](https://www.moesif.com). Moesif é um serviço baseado em nuvem especializado em análise e depurações HTTP. Têm um nível gratuito, por isso é fácil de tentar e permite-nos ver os pedidos HTTP em tempo real a fluir através do nosso serviço de Gestão API.

A `IHttpMessageProcessor` implementação parece-se com isto,

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

Aproveita `MoesifHttpMessageProcessor` uma [biblioteca API C# para o Moesif](https://www.moesif.com/docs/api?csharp#events) que facilita a entrada dos dados do evento HTTP para o seu serviço. Para enviar dados HTTP para a API do Reif Collector, precisa de uma conta e de um ID de aplicação. Obtém um Id de Aplicação Moesif criando uma conta no [site do Moesif](https://www.moesif.com) e, em seguida, vá para a Configuração da App _do Menu Superior Direito._  ->  

## <a name="complete-sample"></a>Amostra completa
O [código-fonte](https://github.com/dgilling/ApimEventProcessor) e os testes para a amostra estão no GitHub. Você precisa de um Serviço de [Gestão API](get-started-create-service-instance.md), [um Centro de Eventos conectado](api-management-howto-log-event-hubs.md), e uma Conta de [Armazenamento](../storage/common/storage-account-create.md) para executar a amostra por si mesmo.   

A amostra é apenas uma aplicação simples para consola que ouve eventos vindos do Event Hub, converte-os num Moesif `EventRequestModel` e `EventResponseModel` objetos e, em seguida, reencaminha-os para a API do Colecionador Moesif.

Na imagem animada que se segue, pode ver-se um pedido a ser feito a uma API no Portal do Desenvolvedor, a aplicação Consola mostrando a mensagem a ser recebida, processada e reencaminhada e, em seguida, o pedido e resposta que aparece no Stream de Eventos.

![Demonstração do pedido que está a ser encaminhado para o Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Resumo
O serviço Azure API Management oferece um local ideal para capturar o tráfego HTTP que viaja de e para as suas APIs. O Azure Event Hubs é uma solução altamente escalável e de baixo custo para capturar esse tráfego e alimentá-lo em sistemas de processamento secundários para exploração madeireira, monitorização e outras análises sofisticadas. Ligar-se a sistemas de monitorização de tráfego de terceiros como o Moesif é tão simples como algumas dezenas de linhas de código.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os Azure Event Hubs
  * [Começa com os Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com o EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração da API Management e Do Event Hubs
  * [Como registar eventos para Azure Event Hubs na Azure API Management](api-management-howto-log-event-hubs.md)
  * [Referência da entidade logger](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referência política log-to-eventhub](./api-management-advanced-policies.md#log-to-eventhub)
