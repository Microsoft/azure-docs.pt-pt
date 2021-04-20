---
title: Gravar e baixar chamadas com a Grade de Eventos - Um quickstart dos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, você vai aprender a gravar e baixar chamadas usando a Grade de Eventos.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/14/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 493a35a627f458fe649931d9fabc175b0affc3a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730584"
---
# <a name="record-and-download-calls-with-event-grid"></a>Gravar e baixar chamadas com a Grade de Eventos

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Inicie-se com os Serviços de Comunicação Azure gravando as suas chamadas de Serviços de Comunicação utilizando a Azure Event Grid.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um recurso ativo dos Serviços de Comunicação. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md?pivots=platform-azp&tabs=windows)
- O [`Microsoft.Azure.EventGrid`](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) pacote NuGet.

## <a name="create-a-webhook-and-subscribe-to-the-recording-events"></a>Crie um webhook e subscreva os eventos de gravação
Usaremos *webhooks* e *eventos* para facilitar a gravação de chamadas e transferências de ficheiros de mídia. 

Primeiro, vamos criar um webhook. O seu recurso de Serviços de Comunicação utilizará a Grade de Eventos para notificar este webhook quando o `recording` evento for desencadeado e, novamente, quando os meios de comunicação gravados estiverem prontos para serem descarregados.

Você pode escrever seu próprio webhook personalizado para receber estas notificações de eventos. É importante que este webhook responda a mensagens de entrada com o código de validação para subscrever com sucesso o webhook para o serviço de eventos.

```
[HttpPost]
public async Task<ActionResult> PostAsync([FromBody] object request)
  {
   //Deserializing the request 
    var eventGridEvent = JsonConvert.DeserializeObject<EventGridEvent[]>(request.ToString())
        .FirstOrDefault();
    var data = eventGridEvent.Data as JObject;

    // Validate whether EventType is of "Microsoft.EventGrid.SubscriptionValidationEvent"
    if (string.Equals(eventGridEvent.EventType, EventTypes.EventGridSubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
   {
        var eventData = data.ToObject<SubscriptionValidationEventData>();
        var responseData = new SubscriptionValidationResponseData
        {
            ValidationResponse = eventData.ValidationCode
        };
        if (responseData.ValidationResponse != null)
        {
            return Ok(responseData);
        }
    }

    // Implement your logic here.
    ...
    ...
  }
```


O código acima depende do `Microsoft.Azure.EventGrid` pacote NuGet. Para saber mais sobre a validação do ponto final da Grade de Eventos, visite a [documentação de validação](https://docs.microsoft.com/azure/event-grid/receive-events#endpoint-validation) do ponto final

Em seguida, subscreveremos este webhook para o `recording` evento:

1. Selecione a `Events` lâmina do seu recurso Azure Communication Services.
2. Selecione `Event Subscription` como mostrado abaixo.
![Screenshot mostrando uI da grelha de evento](./media/call-recording/image1-event-grid.png)
3. Configure a subscrição do evento e selecione `Call Recording File Status Update` como `Event Type` . Selecione `Webhook` como `Endpoint type` .
![Criar Subscrição de Eventos](./media/call-recording/image2-create-subscription.png)
4. Insira o URL do seu webhook em `Subscriber Endpoint` .
![Subscreva o Evento](./media/call-recording/image3-subscribe-to-event.png)

O seu webhook será agora notificado sempre que o seu recurso de Serviços de Comunicação for utilizado para gravar uma chamada.

## <a name="notification-schema"></a>Esquema de notificação
Quando a gravação estiver disponível para download, o seu recurso de Serviços de Comunicação emitirá uma notificação com o seguinte esquema de evento. As identificações do documento para a gravação podem ser recolhidas `documentId` nos campos de cada um `recordingChunk` .

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}

```

## <a name="download-the-recorded-media-files"></a>Descarregue os ficheiros de mídia gravados

Assim que tivermos o documento ID para o ficheiro que queremos descarregar, ligaremos para as APIs abaixo dos Serviços de Comunicação Azure para descarregar os meios de comunicação e metadados gravados usando a autenticação HMAC.

O tamanho máximo do ficheiro de gravação é de 1,5GB. Quando este tamanho de ficheiro for ultrapassado, o gravador dividirá automaticamente os meios de comunicação gravados em vários ficheiros.

O cliente deve ser capaz de descarregar todos os ficheiros de mídia com um único pedido. Se houver algum problema, o cliente pode voltar a tentar com um cabeçalho de alcance para evitar rebacar segmentos que já foram descarregados.

Para baixar os meios de comunicação gravados: 
- Método: `GET` 
- URL: https://contoso.communication.azure.com/recording/download/{documentId}?api-version=2021-04-15-preview1

Para baixar metadados de mídia gravados: 
- Método: `GET` 
- URL: https://contoso.communication.azure.com/recording/download/{documentId}/metadata?api-version=2021-04-15-preview1


### <a name="authentication"></a>Autenticação
Para descarregar meios e metadados gravados, utilize a autenticação HMAC para autenticar o pedido contra as APIs dos Serviços de Comunicação Azure.

Crie um `HttpClient` e adicione os cabeçalhos necessários utilizando os `HmacAuthenticationUtils` abaixo fornecidos:

```
  var client = new HttpClient();

  // Set Http Method
  var method = HttpMethod.Get;
  StringContent content = null;

  // Build request
  var request = new HttpRequestMessage
  {
      Method = method, // Http GET method
      RequestUri = new Uri(<Download_Recording_Url>), // Download recording Url
      Content = content // content if required for POST methods
  };

  // Question: Why do we need to pass String.Empty to CreateContentHash() method?
  // Answer: In HMAC authentication, the hash of the content is one of the parameters used to generate the HMAC token.
  // In our case our recording download APIs are GET methods and do not have any content/body to be passed in the request. 
  // However in this case we still need the SHA256 hash for the empty content and hence we pass an empty string. 


  string serializedPayload = string.Empty;

  // Hash the content of the request.
  var contentHashed = HmacAuthenticationUtils.CreateContentHash(serializedPayload);

  // Add HAMC headers.
  HmacAuthenticationUtils.AddHmacHeaders(request, contentHashed, accessKey, method);

  // Make a request to the Azure Communication Services APIs mentioned above
  var response = await client.SendAsync(request).ConfigureAwait(false);
```

#### <a name="hmacauthenticationutils"></a>HmacAuthenticationUtils 
Os utilitários abaixo podem ser utilizados para gerir o seu fluxo de trabalho HMAC.

**Criar haxixe de conteúdo**

```
public static string CreateContentHash(string content)
{
    var alg = SHA256.Create();

    using (var memoryStream = new MemoryStream())
    using (var contentHashStream = new CryptoStream(memoryStream, alg, CryptoStreamMode.Write))
    {
        using (var swEncrypt = new StreamWriter(contentHashStream))
        {
            if (content != null)
            {
                swEncrypt.Write(content);
            }
        }
    }

    return Convert.ToBase64String(alg.Hash);
}
```

**Adicione cabeçalhos HMAC**

```
public static void AddHmacHeaders(HttpRequestMessage requestMessage, string contentHash, string accessKey)
{
    var utcNowString = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
    var uri = requestMessage.RequestUri;
    var host = uri.Authority;
    var pathAndQuery = uri.PathAndQuery;

    var stringToSign = $"{requestMessage.Method}\n{pathAndQuery}\n{utcNowString};{host};{contentHash}";
    var hmac = new HMACSHA256(Convert.FromBase64String(accessKey));
    var hash = hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign));
    var signature = Convert.ToBase64String(hash);
    var authorization = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";

    requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
    requestMessage.Headers.Add("Date", utcNowString);
    requestMessage.Headers.Add("Authorization", authorization);
}
```

## <a name="clean-up-resources"></a>Limpar os recursos
Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources)


## <a name="next-steps"></a>Próximos passos
Para obter mais informações, veja os seguintes artigos:

- Confira a nossa [amostra de chamada web](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Saiba mais [sobre as capacidades de chamar sDK](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Saiba mais sobre [como funciona a chamada](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
