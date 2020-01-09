---
title: Monitorar APIs com gerenciamento de API do Azure, hubs de eventos e Moesif
titleSuffix: Azure API Management
description: Aplicativo de exemplo que demonstra a política de log para eventhub conectando o gerenciamento de API do Azure, hubs de eventos do Azure e Moesif para log e monitoramento de HTTP
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442533"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitore suas APIs com o gerenciamento de API do Azure, os hubs de eventos e o Moesif
O [serviço de gerenciamento de API](api-management-key-concepts.md) fornece muitos recursos para aprimorar o processamento de solicitações HTTP enviadas à sua API http. No entanto, a existência das solicitações e das respostas é transitória. A solicitação é feita e flui pelo serviço de gerenciamento de API para sua API de back-end. Sua API processa a solicitação e uma resposta flui de volta para o consumidor da API. O serviço de gerenciamento de API mantém algumas estatísticas importantes sobre as APIs para exibição no painel de portal do Azure, mas, além disso, os detalhes foram resumidos.

Usando a política de log para eventhub no serviço de gerenciamento de API, você pode enviar todos os detalhes da solicitação e da resposta para um [Hub de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Há uma variedade de motivos pelos quais você pode querer gerar eventos de mensagens HTTP que estão sendo enviadas para suas APIs. Alguns exemplos incluem trilha de auditoria de atualizações, análise de uso, alertas de exceção e integrações de terceiros.

Este artigo demonstra como capturar a mensagem de resposta e solicitação HTTP inteira, enviá-la a um hub de eventos e, em seguida, retransmitir essa mensagem para um serviço de terceiros que fornece serviços de log e monitoramento de HTTP.

## <a name="why-send-from-api-management-service"></a>Por que enviar do serviço de gerenciamento de API?
É possível escrever middleware HTTP que pode ser conectado a estruturas de API HTTP para capturar solicitações e respostas HTTP e alimentar os sistemas de registro em log e monitoramento. A desvantagem dessa abordagem é que o middleware HTTP precisa ser integrado à API de back-end e deve corresponder à plataforma da API. Se houver várias APIs, cada uma delas deverá implantar o middleware. Geralmente, há razões pelas quais as APIs de back-end não podem ser atualizadas.

Usar o serviço de gerenciamento de API do Azure para integrar com a infraestrutura de log fornece uma solução centralizada e independente de plataforma. Ele também é escalonável, em parte devido aos recursos de [replicação geográfica](api-management-howto-deploy-multi-region.md) do gerenciamento de API do Azure.

## <a name="why-send-to-an-azure-event-hub"></a>Por que enviar para um hub de eventos do Azure?
É razoável perguntar, por que criar uma política específica para os hubs de eventos do Azure? Há muitos locais diferentes em que eu poderia querer registrar minhas solicitações. Por que não apenas enviar as solicitações diretamente para o destino final?  Essa é uma opção. No entanto, ao fazer solicitações de registro em log de um serviço de gerenciamento de API, é necessário considerar como as mensagens de log afetam o desempenho da API. Aumentos graduais na carga podem ser tratados aumentando as instâncias disponíveis de componentes do sistema ou aproveitando a replicação geográfica. No entanto, picos curtos no tráfego podem fazer com que as solicitações sejam atrasadas se as solicitações de log de infraestrutura começarem a ser lentas sob carga.

Os hubs de eventos do Azure foram projetados para ingressar em grandes volumes de dados, com capacidade para lidar com um número muito maior de eventos do que o número de solicitações HTTP processadas pela maioria das APIs. O Hub de eventos atua como um tipo de buffer sofisticado entre o serviço de gerenciamento de API e a infraestrutura que armazena e processa as mensagens. Isso garante que o desempenho da API não será afetado devido à infraestrutura de registro em log.

Depois que os dados tiverem sido passados para um hub de eventos, eles serão persistidos e aguardarão que os consumidores do hub de eventos o processem. O Hub de eventos não se preocupa com a forma como ele é processado, ele apenas se preocupa em verificar se a mensagem será entregue com êxito.

Os hubs de eventos têm a capacidade de transmitir eventos para vários grupos de consumidores. Isso permite que os eventos sejam processados por diferentes sistemas. Isso permite o suporte a muitos cenários de integração sem colocar atrasos de adição no processamento da solicitação de API dentro do serviço de gerenciamento de API, pois apenas um evento precisa ser gerado.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Uma política para enviar mensagens de aplicativo/http
Um hub de eventos aceita dados de eventos como uma cadeia de caracteres simples. O conteúdo dessa cadeia de caracteres cabe a você. Para poder empacotar uma solicitação HTTP e enviá-la para os hubs de eventos, precisamos Formatar a cadeia de caracteres com as informações de solicitação ou resposta. Em situações como essa, se houver um formato existente que possamos reutilizar, talvez não seja necessário escrever nosso próprio código de análise. Inicialmente, considerei usar o [Har](http://www.softwareishard.com/blog/har-12-spec/) para enviar solicitações e respostas http. No entanto, esse formato é otimizado para armazenar uma sequência de solicitações HTTP em um formato baseado em JSON. Ele continha vários elementos obrigatórios que adicionaram complexidade desnecessária para o cenário de passar a mensagem HTTP pela conexão.

Uma opção alternativa era usar o tipo de mídia `application/http` conforme descrito na especificação HTTP [RFC 7230](https://tools.ietf.org/html/rfc7230). Esse tipo de mídia usa exatamente o mesmo formato usado para realmente enviar mensagens HTTP pela conexão, mas toda a mensagem pode ser colocada no corpo de outra solicitação HTTP. Em nosso caso, vamos usar o corpo como nossa mensagem para enviar aos hubs de eventos. Convenientemente, há um analisador que existe em bibliotecas de [cliente Microsoft ASP.NET API Web 2,2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) que podem analisar esse formato e convertê-lo nos objetos `HttpRequestMessage` e `HttpResponseMessage` nativos.

Para poder criar essa mensagem, precisamos aproveitar as expressões de C# [política](/azure/api-management/api-management-policy-expressions) com base no gerenciamento de API do Azure. Aqui está a política, que envia uma mensagem de solicitação HTTP para os hubs de eventos do Azure.

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
Algumas coisas específicas que valem a pena mencionar essa expressão de política. A política de log para eventhub tem um atributo chamado logger-ID, que se refere ao nome do agente que foi criado no serviço de gerenciamento de API. Os detalhes de como configurar um agente do hub de eventos no serviço de gerenciamento de API podem ser encontrados no documento [como registrar em log eventos nos hubs de eventos do Azure no gerenciamento de API do Azure](api-management-howto-log-event-hubs.md). O segundo atributo é um parâmetro opcional que instrui os hubs de eventos em qual partição armazenar a mensagem. Os hubs de eventos usam partições para habilitar a escalabilidade e exigem, no mínimo, dois. A entrega ordenada de mensagens só é garantida dentro de uma partição. Se não instruirmos o Hub de eventos em qual partição Coloque a mensagem, ela usará um algoritmo Round Robin para distribuir a carga. No entanto, isso pode fazer com que algumas de nossas mensagens sejam processadas fora de ordem.

### <a name="partitions"></a>Partições
Para garantir que nossas mensagens sejam entregues aos consumidores em ordem e aproveitem o recurso de distribuição de carga das partições, optei por enviar mensagens de solicitação HTTP para uma partição e mensagens de resposta HTTP para uma segunda partição. Isso garante uma distribuição uniforme de carga e podemos garantir que todas as solicitações serão consumidas na ordem e todas as respostas sejam consumidas na ordem. É possível que uma resposta seja consumida antes da solicitação correspondente, mas como isso não é um problema, pois temos um mecanismo diferente para correlacionar solicitações a respostas e sabemos que as solicitações sempre vêm antes das respostas.

### <a name="http-payloads"></a>Cargas HTTP
Depois de criar o `requestLine`, verificamos se o corpo da solicitação deve ser truncado. O corpo da solicitação é truncado para apenas 1024. Isso pode ser aumentado, no entanto, as mensagens individuais do hub de eventos são limitadas a 256 KB, portanto, é provável que alguns corpos de mensagens HTTP não caibam em uma única mensagem. Ao fazer o log e a análise, uma quantidade significativa de informações pode ser derivada apenas da linha de solicitação HTTP e dos cabeçalhos. Além disso, muitas solicitações de APIs só retornam corpos pequenos e, portanto, a perda de valor das informações truncando corpos grandes é relativamente mínima em comparação com a redução nos custos de transferência, processamento e armazenamento para manter todo o conteúdo do corpo. Uma observação final sobre o processamento do corpo é que precisamos passar `true` para o método `As<string>()` porque estamos lendo o conteúdo do corpo, mas também queria que a API de back-end pudesse ler o corpo. Ao passar true para esse método, fazemos com que o corpo seja armazenado em buffer para que possa ser lido uma segunda vez. É importante estar atento se você tiver uma API que faça o upload de arquivos grandes ou use sondagem longa. Nesses casos, seria melhor evitar a leitura do corpo.

### <a name="http-headers"></a>Cabeçalhos HTTP
Os cabeçalhos HTTP podem ser transferidos para o formato da mensagem em um formato de par chave/valor simples. Optamos por retirar certos campos sensíveis à segurança, para evitar vazamento desnecessariamente de informações de credenciais. É improvável que as chaves de API e outras credenciais sejam usadas para fins de análise. Se quisermos fazer a análise sobre o usuário e o produto específico que eles estão usando, poderíamos obtê-lo do objeto `context` e adicioná-lo à mensagem.

### <a name="message-metadata"></a>Metadados da mensagem
Ao criar a mensagem completa para enviar para o Hub de eventos, a primeira linha não é realmente parte da mensagem de `application/http`. A primeira linha são metadados adicionais que consistem em se a mensagem é uma mensagem de solicitação ou resposta e uma ID de mensagem, que é usada para correlacionar solicitações a respostas. A ID da mensagem é criada usando outra política parecida com esta:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Poderíamos ter criado a mensagem de solicitação, armazená-la em uma variável até que a resposta fosse retornada e, em seguida, enviada a solicitação e a resposta como uma única mensagem. No entanto, ao enviar a solicitação e a resposta de forma independente e usar uma ID de mensagem para correlacionar as duas, obtemos um pouco mais de flexibilidade no tamanho da mensagem, a capacidade de tirar proveito de várias partições durante a manutenção da ordem da mensagem e a solicitação será exibida em nosso painel de registro em log mais cedo. Também pode haver alguns cenários em que uma resposta válida nunca é enviada para o Hub de eventos, possivelmente devido a um erro fatal de solicitação no serviço de gerenciamento de API, mas ainda temos um registro da solicitação.

A política para enviar a mensagem HTTP de resposta é semelhante à solicitação e, portanto, a configuração da política completa é parecida com esta:

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

A política de `set-variable` cria um valor que é acessível pela política de `log-to-eventhub` na seção `<inbound>` e a seção `<outbound>`.

## <a name="receiving-events-from-event-hubs"></a>Recebendo eventos dos hubs de eventos
Os eventos do hub de eventos do Azure são recebidos usando o [protocolo AMQP](https://www.amqp.org/). A equipe do barramento de serviço da Microsoft tornou as bibliotecas de cliente disponíveis para facilitar o consumo dos eventos. Há duas abordagens diferentes com suporte, uma é um *consumidor direto* e a outra é usar a classe `EventProcessorHost`. Exemplos dessas duas abordagens podem ser encontrados no guia de [programação dos hubs de eventos](../event-hubs/event-hubs-programming-guide.md). A versão curta das diferenças é, `Direct Consumer` dá a você controle completo e o `EventProcessorHost` faz parte do trabalho de encanamento para você, mas faz algumas suposições sobre como você processa esses eventos.

### <a name="eventprocessorhost"></a>EventProcessorHost
Neste exemplo, usamos o `EventProcessorHost` para simplificar, mas talvez não seja a melhor opção para esse cenário específico. `EventProcessorHost` faz o trabalho árduo de garantir que você não precise se preocupar com problemas de Threading dentro de uma classe de processador de eventos específica. No entanto, em nosso cenário, estamos simplesmente convertendo a mensagem em outro formato e passando-a para outro serviço usando um método assíncrono. Não há necessidade de atualizar o estado compartilhado e, portanto, nenhum risco de problemas de Threading. Na maioria dos cenários, `EventProcessorHost` é provavelmente a melhor opção e, certamente, é a opção mais fácil.

### <a name="ieventprocessor"></a>IEventProcessor
O conceito central ao usar o `EventProcessorHost` é criar uma implementação da interface `IEventProcessor`, que contém o método `ProcessEventAsync`. A essência desse método é mostrada aqui:

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

Uma lista de objetos EventData é passada para o método e iteramos sobre essa lista. Os bytes de cada método são analisados em um objeto HttpMessage e esse objeto é passado para uma instância de IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
A instância de `HttpMessage` contém três partes de dados:

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

A instância de `HttpMessage` contém um GUID de `MessageId` que nos permite conectar a solicitação HTTP à resposta HTTP correspondente e um valor booliano que identifica se o objeto contém uma instância de um HttpRequestMessage e HttpResponseMessage. Ao usar as classes HTTP internas do `System.Net.Http`, consegui aproveitar o código de análise de `application/http` que está incluído no `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
A instância de `HttpMessage` é então encaminhada para a implementação de `IHttpMessageProcessor`, que é uma interface que criei para desacoplar o recebimento e a interpretação do evento do hub de eventos do Azure e o processamento real dele.

## <a name="forwarding-the-http-message"></a>Encaminhando a mensagem HTTP
Para este exemplo, decidi que seria interessante enviar por push a solicitação HTTP para a [análise de API do Moesif](https://www.moesif.com). O Moesif é um serviço baseado em nuvem especializado em análise e depuração de HTTP. Eles têm uma camada gratuita, portanto, é fácil experimentá-los e nos permite ver as solicitações HTTP em tempo real em meio ao nosso serviço de gerenciamento de API.

A implementação de `IHttpMessageProcessor` é parecida com esta,

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

O `MoesifHttpMessageProcessor` aproveita uma [ C# biblioteca de API para Moesif](https://www.moesif.com/docs/api?csharp#events) que facilita o envio de dados de evento http para seus serviços. Para enviar dados HTTP para a API do coletor do Moesif, você precisa de uma conta e uma ID do aplicativo. Você Obtém uma ID do aplicativo Moesif criando uma conta no [site do Moesif](https://www.moesif.com) e, em seguida, acesse o _Menu superior direito_ -> _configuração do aplicativo_.

## <a name="complete-sample"></a>Exemplo completo
O [código-fonte](https://github.com/dgilling/ApimEventProcessor) e os testes do exemplo estão no github. Você precisa de um [serviço de gerenciamento de API](get-started-create-service-instance.md), [de um hub de eventos conectado](api-management-howto-log-event-hubs.md)e de uma [conta de armazenamento](../storage/common/storage-create-storage-account.md) para executar o exemplo.   

O exemplo é apenas um aplicativo de console simples que escuta eventos provenientes do hub de eventos, converte-os em um Moesif `EventRequestModel` e `EventResponseModel` objetos e, em seguida, encaminha-os para a API do coletor do Moesif.

Na imagem animada a seguir, você pode ver uma solicitação sendo feita a uma API no portal do desenvolvedor, o aplicativo de console que mostra a mensagem sendo recebida, processada e encaminhada e, em seguida, a solicitação e a resposta exibidas no fluxo de eventos.

![Demonstração da solicitação sendo encaminhada ao Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Resumo
O serviço de gerenciamento de API do Azure fornece um lugar ideal para capturar o tráfego HTTP que viaja de e para suas APIs. Os hubs de eventos do Azure são uma solução altamente escalonável e de baixo custo para capturar esse tráfego e enalimenta-lo em sistemas de processamento secundários para registro em log, monitoramento e outras análises sofisticadas. Conectar-se a sistemas de monitoramento de tráfego de terceiros como o Moesif é tão simples quanto algumas dúzias de linhas de código.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os hubs de eventos do Azure
  * [Introdução aos hubs de eventos do Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração de gerenciamento de API e hubs de eventos
  * [Como registrar eventos em log nos hubs de eventos do Azure no gerenciamento de API do Azure](api-management-howto-log-event-hubs.md)
  * [Referência de entidade de agente](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referência de política de log para eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
