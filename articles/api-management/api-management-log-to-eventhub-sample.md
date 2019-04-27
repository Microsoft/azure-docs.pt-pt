---
title: Monitorizar as APIs com a gestão de API do Azure, os Hubs de eventos e Moesif | Documentos da Microsoft
description: Aplicação de exemplo que demonstram a política de registo para eventhub por ligação API Management do Azure, os Hubs de eventos do Azure e Moesif para HTTP, registo e monitorização
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
ms.openlocfilehash: c52a1942bda9881f8f782a227c81feaa4813722d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656750"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitorizar as suas APIs com a gestão de API do Azure, os Hubs de eventos e Moesif
O [serviço de gestão de API](api-management-key-concepts.md) oferece muitos recursos para aprimorar o processamento de pedidos HTTP enviados para a API de HTTP. No entanto, a existência das solicitações e respostas é transitória. A solicitação é feita e, flui através do serviço de gestão de API para a API de back-end. A API processa a solicitação e uma resposta flui através de volta para o consumidor de API. O serviço de gestão de API mantém algumas estatísticas importantes sobre as APIs para apresentação no dashboard do portal do Azure, mas muito mais que, os detalhes foram removidos.

Ao utilizar a política de registo para eventhub no serviço de gestão de API, pode enviar todos os detalhes de solicitação e resposta para uma [Hub de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Há uma série de motivos por que pretende gerar eventos a partir de mensagens HTTP a ser enviadas para as suas APIs. Alguns exemplos incluem trilha de auditoria de atualizações, análise de utilização, alertas de exceção e integrações de terceiros.

Este artigo demonstra como capturar a mensagem de solicitação e resposta HTTP inteira, enviá-lo para um Hub de eventos e, em seguida, a mensagem é um serviço de terceiros que fornece o HTTP, registo e a monitorização dos serviços de reencaminhamento.

## <a name="why-send-from-api-management-service"></a>Por que o envio do serviço de gestão de API?
É possível escrever o middleware HTTP que possa se conectar com as estruturas de API de HTTP para capturar as solicitações e respostas HTTP e fornecê-los no registo e sistemas de monitorização. A desvantagem dessa abordagem é o middleware HTTP tem de ser integradas no back-end da API e tem de coincidir com a plataforma da API. Se existirem várias APIs, em seguida, cada um deles tem de implementar o middleware. Muitas vezes, existem motivos por que não é possível atualizar as APIs de back-end.

Utilizar o serviço de gestão de API do Azure para integrar com a infra-estrutura de log fornece uma solução centralizada e independente de plataforma. Também é dimensionável, em parte devido ao [georreplicação](api-management-howto-deploy-multi-region.md) capacidades de gestão de API do Azure.

## <a name="why-send-to-an-azure-event-hub"></a>Por que enviar para um Hub de eventos do Azure?
É um valor razoável fazer, por que criar uma política específica para os Hubs de eventos do Azure? Existem diversos lugares em que eu deseje os meus pedidos de registo. Por que não enviar apenas os pedidos diretamente para o destino final?  Essa é uma opção. No entanto, quando são efetuados pedidos de registo de um serviço de gestão de API, é necessário considerar como mensagens de registo de afetam o desempenho da API. Graduais aumentos de carga podem ser processados ao aumentar instâncias disponíveis de componentes do sistema ou ao tirar partido da georreplicação. No entanto, curtos picos de tráfego podem causar pedidos para ser atrasada se os pedidos para a infra-estrutura de log começam a lento sob carga.

Os Hubs de eventos do Azure foi concebido para grandes volumes de entrada de dados, com capacidade para lidar com um número muito maior de eventos que o número de solicitações HTTP a maioria dos processos APIs. O Hub de eventos age como uma espécie de buffer sofisticada entre o serviço de gestão de API e a infraestrutura que armazene e processa as mensagens. Isto garante que o desempenho da sua API não seja afetado devido a infra-estrutura de log.

Depois dos dados tiverem sido passados para um Hub de eventos, ele é mantido e espera que os consumidores do Hub de eventos para processá-lo. O Hub de eventos não se importam como são processado, ele se concentra apenas nos certificar-se de que a mensagem será entregue com êxito.

Os Hubs de eventos tem a capacidade de transmitir eventos para vários grupos de consumidores. Isso permite que os eventos a ser processado por sistemas diferentes. Isto permite que suporte a muitos cenários de integração sem colocar os atrasos de adição no processamento do pedido de API no serviço de gestão de API, como apenas um evento tem de ser gerado.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Uma política para enviar mensagens de aplicativo/http
Um Hub de eventos aceita dados de eventos como uma cadeia de caracteres simples. O conteúdo dessa cadeia de caracteres é depende de. Para ser capaz de empacotar uma solicitação HTTP e enviá-los para os Hubs de eventos, precisamos de formatar a cadeia de caracteres com as informações de solicitação ou resposta. Em situações como essa, se houver um formato existente podemos reutilizar, em seguida, que pode não ter de escrever nossos próprios de análise de código. Inicialmente considerado posso utilizar o [HAR](http://www.softwareishard.com/blog/har-12-spec/) para o envio de solicitações e respostas HTTP. No entanto, esse formato é otimizado para armazenar uma seqüência de pedidos de HTTP num formato baseado em JSON. Continha um número de elementos obrigatórios que adicionou complexidade desnecessária para o cenário de passar a mensagem HTTP durante a transmissão.

Uma opção era usar a `application/http` tipo de suporte, conforme descrito na especificação de HTTP [RFC 7230](https://tools.ietf.org/html/rfc7230). Este tipo de suporte de dados utiliza o mesmo formato exato que é utilizado para efetivamente enviar mensagens HTTP durante a transmissão, mas a mensagem inteira pode ser colocado no corpo do outro pedido HTTP. No nosso caso, apenas vamos utilizar o corpo como nossa mensagem para enviar para os Hubs de eventos. Convenientemente, há um analisador que exista na [cliente do Microsoft ASP.NET Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) bibliotecas que podem analisar esse formato e convertê-los nativa `HttpRequestMessage` e `HttpResponseMessage` objetos.

Para poder criar esta mensagem, temos de tirar partido do c# com base [expressões de política](/azure/api-management/api-management-policy-expressions) na gestão de API do Azure. Segue-se a política, que envia uma mensagem de pedido HTTP para os Hubs de eventos do Azure.

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

### <a name="policy-declaration"></a>Declaração de política
Existem alguns itens específicos que vale a pena mencionar sobre essa expressão da política. A política de registo para eventhub tem um atributo chamado logger-id, que se refere ao nome do agente de log que foram criado no âmbito do serviço de gestão de API. Os detalhes de como configurar um agente de log do Hub de eventos no serviço de gestão de API podem ser encontrados no documento [como registar eventos nos Hubs de eventos do Azure na gestão de API do Azure](api-management-howto-log-event-hubs.md). O segundo atributo é um parâmetro opcional que instrui os Hubs de eventos de partição para armazenar a mensagem. Os Event Hubs utilizam as partições para ativar a escalabilidade e requer um mínimo de dois. A entrega ordenada de mensagens é garantida apenas dentro de uma partição. Se não podemos instruir o Hub de eventos em cada partição para colocar a mensagem, ele usa um algoritmo de round robin para distribuir a carga. No entanto, que pode fazer com que algumas das nossas mensagens sejam processadas fora de ordem.

### <a name="partitions"></a>Partições
Para garantir que nossas mensagens são entregues aos consumidores em ordem e aproveitam a capacidade de distribuição de carga de partições, optei por enviar mensagens de pedido HTTP para uma partição e mensagens de resposta HTTP para uma segunda partição. Isto garante que uma distribuição de carga uniforme, e podemos garantir que todos os pedidos serão consumidos por ordem e todas as respostas são consumidas por ordem. É possível que uma resposta ser consumida antes da solicitação correspondente, mas à medida que não é um problema, pois temos um mecanismo diferente para correlacionar os pedidos de respostas e sabemos que solicitações chegarem sempre antes das respostas.

### <a name="http-payloads"></a>Cargas HTTP
Depois de criar o `requestLine`, verificamos se o corpo do pedido deve ser truncado. O corpo do pedido é truncado para 1024 única. Isso pode ser aumentado, no entanto, mensagens do Hub de eventos individuais estão limitadas a 256 KB, portanto, é provável que algumas mensagens HTTP corpos irá não se encaixam numa única mensagem. Ao fazer o registo e análise de uma quantidade significativa de informações pode ser derivada da apenas a linha de solicitação HTTP e os cabeçalhos. Além disso, muitas APIs solicitar apenas retorno corpos de pequeno e por isso, a perda de valor de informações por truncar um corpo grande é bastante mínima em comparação com a redução na transferência, processamento e os custos de armazenamento para manter todo o conteúdo do corpo. Uma observação final sobre o processamento do corpo é que precisamos passar `true` para o `As<string>()` método uma vez que está a ler o conteúdo do corpo, mas era também queria que a API para conseguir ler o corpo de back-end. Ao passar valor verdadeiro para este método, podemos fazer com que o corpo para ser armazenado em buffer para que possa ser lido uma segunda vez. Isso é importante ter em consideração se tiver uma API que não o carregamento de ficheiros grandes ou usa consulta longa. Nestes casos, seria melhor evitar a ler o corpo de todo.

### <a name="http-headers"></a>Cabeçalhos HTTP
Cabeçalhos HTTP podem ser transferidos por para o formato de mensagem num formato de par chave/valor simples. Escolhemos de retirar determinados campos confidenciais de segurança, para evitar a fuga desnecessariamente informações de credenciais. É improvável que as chaves de API e outras credenciais seriam usados para fins de análise. Se o que Desejamos fazer análises sobre o utilizador e o produto em particular que estão a utilizar, então, pode obtê-lo a `context` de objeto e adicioná-la para a mensagem.

### <a name="message-metadata"></a>Metadados de mensagem
Ao criar a mensagem completa a enviar para o hub de eventos, a primeira linha não é, na verdade, parte do `application/http` mensagem. A primeira linha é constituída por se a mensagem é um pedido ou mensagem de resposta e uma mensagem ID, que é utilizado para correlacionar os pedidos de respostas de metadados adicionais. O ID da mensagem é criado utilizando outra política como esta:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Vamos poderia ter criado a mensagem de solicitação, armazenado que numa variável, até que a resposta foi devolvida e, em seguida, enviada o pedido e resposta como uma única mensagem. No entanto, ao enviar o pedido e resposta de forma independente e utilizar um id de mensagem para correlacionar os dois, obtemos um pouco mais flexibilidade no tamanho da mensagem, a capacidade para tirar partido das várias partições, lidando simultaneamente e manter a ordem da mensagem e o pedido aparecerá no nosso dashboard mais cedo de registo. Também pode haver alguns cenários em que uma resposta válida nunca é enviada para o hub de eventos, possivelmente devido a um erro fatal de pedido no serviço de gestão de API, mas ainda temos um registro do pedido.

A política para enviar a mensagem HTTP de resposta é semelhante ao pedido e por isso, a configuração da política concluída é semelhante a este:

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

O `set-variable` política cria um valor que é acessível por ambos os `log-to-eventhub` política no `<inbound>` secção e a `<outbound>` secção.

## <a name="receiving-events-from-event-hubs"></a>Receber eventos dos Hubs de eventos
Eventos do Hub de eventos do Azure forem recebidos usando o [protocolo AMQP](https://www.amqp.org/). A equipe do barramento de serviço da Microsoft tornou cliente bibliotecas disponíveis para facilitar os consumo de eventos. Existem duas abordagens diferentes suportadas, uma está a ser um *consumidor direto* e o outro está a utilizar o `EventProcessorHost` classe. Exemplos dessas duas abordagens podem ser encontrados no [guia de programação dos Hubs de eventos](../event-hubs/event-hubs-programming-guide.md). O resumo das diferenças é, `Direct Consumer` dá-lhe controlo total e o `EventProcessorHost` faz parte do trabalho detalhes técnicos para faz, mas determinadas suposições sobre como processar esses eventos.

### <a name="eventprocessorhost"></a>EventProcessorHost
Neste exemplo, utilizamos o `EventProcessorHost` para simplificar, no entanto, poderá não a melhor opção para esse cenário específico. `EventProcessorHost` faz o trabalho pesado de certificar-se de que não precisa se preocupar sobre threading problemas dentro de uma classe de processador de eventos específico. No entanto, em nosso cenário, estamos simplesmente converter a mensagem para outro formato e passá-la ao longo para outro serviço usando um método async. Não é necessário para atualizar o estado compartilhado e, portanto, sem risco de problemas de threading. Na maioria dos cenários, `EventProcessorHost` é provavelmente a melhor opção e, certamente, é a opção mais fácil.

### <a name="ieventprocessor"></a>IEventProcessor
O conceito central ao usar `EventProcessorHost` consiste em criar uma implementação do `IEventProcessor` interface, que contém o método `ProcessEventAsync`. A essência desse método é mostrada aqui:

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

Uma lista de objetos de EventData são passados para o método e iteramos nessa lista. Os bytes de cada método são analisados num objeto de HttpMessage e esse objeto é passado para uma instância de IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
O `HttpMessage` instância contém três partes de dados:

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

O `HttpMessage` instância contém um `MessageId` GUID que permite ligar-se a solicitação HTTP para a resposta HTTP correspondente e um valor booleano que identifica se o objeto contém uma instância de um HttpRequestMessage e HttpResponseMessage. Utilizando o HTTP interno, classes de `System.Net.Http`, fui capaz de aproveitar o `application/http` análise de código que está incluído no `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
O `HttpMessage` instância, em seguida, é reencaminhada para a implementação de `IHttpMessageProcessor`, que é uma interface que eu criei para desacoplar a receber e a interpretação do evento do Hub de eventos do Azure e o processamento real do mesmo.

## <a name="forwarding-the-http-message"></a>Encaminhar a mensagem HTTP
Para este exemplo, decidi que seria interessante enviar o pedido de HTTP ao longo para [Moesif API Analytics](https://www.moesif.com). Moesif é um serviço de com base na cloud que é especialista em análise HTTP e depuração. Eles têm um escalão gratuito, pelo que é fácil experimentar e permite-nos ver os pedidos HTTP em fluxo em tempo real através do nosso serviço de gestão de API.

O `IHttpMessageProcessor` implementação fica assim,

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

O `MoesifHttpMessageProcessor` tira partido de um [ C# biblioteca de API para Moesif](https://www.moesif.com/docs/api?csharp#events) torna mais fácil para os dados de eventos de push HTTP para o seu serviço. Para enviar dados HTTP para a API de Recoletor Moesif, terá de uma conta e uma ID da aplicação. Obter um Id de aplicação Moesif através da criação de uma conta no [site do Moesif](https://www.moesif.com) e, em seguida, vá para o _parte superior direita Menu_ -> _programa de configuração de aplicação_.

## <a name="complete-sample"></a>Exemplo completo
O [código-fonte](https://github.com/dgilling/ApimEventProcessor) e testes para o exemplo estão disponíveis no GitHub. É necessário um [serviço de gestão de API](get-started-create-service-instance.md), [um Hub de eventos ligado](api-management-howto-log-event-hubs.md)e um [conta de armazenamento](../storage/common/storage-create-storage-account.md) para executar o exemplo mesmo.   

O exemplo é apenas uma simple aplicação de consola que fica à escuta de eventos provenientes do Hub de eventos, converte-os num Moesif `EventRequestModel` e `EventResponseModel` objetos e, em seguida, encaminha-os para a API de Recoletor Moesif.

Na imagem seguinte animada, pode ver um pedido que está a ser feito para uma API no Portal do programador, a aplicação de consola que mostra a mensagem a ser recebida, processada e reencaminhada e, em seguida, a solicitação e resposta aparecer de evento Stream.

![Demonstração de solicitação a ser reencaminhada para Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Resumo
O serviço de gestão de API do Azure fornece um local ideal para capturar o tráfego HTTP em viagens no sentido de e para as suas APIs. Os Hubs de eventos do Azure é uma solução altamente dimensionável e de baixo custo para que o tráfego a capturar e alimentá-los no sistemas de processamento secundário para registro em log, monitorização e outras análises sofisticadas. Ligar a sistemas como Moesif é tão simples como algumas dezenas de linhas de código de monitorização de tráfego de terceiros.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre os Hubs de eventos do Azure
  * [Introdução aos Hubs de eventos do Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com o EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração de gestão de API e os Hubs de eventos
  * [Como registar eventos nos Hubs de eventos do Azure na gestão de API do Azure](api-management-howto-log-event-hubs.md)
  * [Referência de entidade Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referência de política de registo para eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
