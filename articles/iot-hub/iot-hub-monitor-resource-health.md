---
title: Monitorar a integridade do seu hub IoT do Azure | Microsoft Docs
description: Use Azure Monitor e Azure Resource Health para monitorar o Hub IoT e diagnosticar problemas rapidamente
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929697"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorar a integridade do Hub IoT do Azure e diagnosticar problemas rapidamente

As empresas que implementam o Hub IoT do Azure esperam um desempenho confiável de seus recursos. Para ajudá-lo a manter uma observação próxima sobre suas operações, o Hub IoT é totalmente integrado com [Azure monitor](../azure-monitor/index.yml) e [Azure Resource Health](../service-health/resource-health-overview.md). Esses dois serviços funcionam para fornecer os dados necessários para manter suas soluções de IoT em funcionamento em um estado íntegro.

Azure Monitor é uma única fonte de monitoramento e registro em log para todos os seus serviços do Azure. Você pode enviar os logs de diagnóstico que Azure Monitor gera para Azure Monitor logs, hubs de eventos ou armazenamento do Azure para processamento personalizado. As configurações de diagnóstico e métricas do Azure Monitor fornecem visibilidade do desempenho de seus recursos. Continue lendo este artigo para saber como [usar Azure monitor](#use-azure-monitor) com o Hub IOT. 

> [!IMPORTANT]
> Os eventos emitidos pelo serviço de Hub IoT usando Azure Monitor logs de diagnóstico não têm garantia de serem confiáveis ou ordenados. Alguns eventos podem ser perdidos ou entregues fora de ordem. Os logs de diagnóstico também não devem ser feitos em tempo real e podem levar vários minutos para que os eventos sejam registrados em sua escolha de destino.

Azure Resource Health ajuda a diagnosticar e obter suporte quando um problema do Azure impacta seus recursos. Um painel fornece o status de integridade atual e anterior para cada um dos seus hubs IoT. Continue na seção na parte inferior deste artigo para saber como [usar Azure Resource Health](#use-azure-resource-health) com o Hub IOT. 

O Hub IoT também fornece suas próprias métricas que você pode usar para entender o estado de seus recursos de IoT. Para saber mais, confira [entender as métricas do Hub IOT](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Utilizar o Azure Monitor

Azure Monitor fornece informações de diagnóstico para recursos do Azure, o que significa que você pode monitorar as operações que ocorrem no Hub IoT.

As configurações de diagnóstico do Azure Monitor substituem o monitor de operações do Hub IoT. Se você usa atualmente o monitoramento de operações, você deve migrar seus fluxos de trabalho. Para obter mais informações, consulte [migrar do monitoramento de operações para as configurações de diagnóstico](iot-hub-migrate-to-diagnostics-settings.md).

Para saber mais sobre as métricas e eventos específicos que Azure Monitor inspeções, consulte [métricas com suporte com Azure monitor](../azure-monitor/platform/metrics-supported.md) e [serviços, esquemas e categorias com suporte para logs de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Entender os logs

Azure Monitor rastreia diferentes operações que ocorrem no Hub IoT. Cada categoria tem um esquema que define como os eventos nessa categoria são relatados.

#### <a name="connections"></a>Ligações

A categoria de conexões rastreia eventos de conexão e desconexão de dispositivos de um hub IoT, bem como erros. Essa categoria é útil para identificar tentativas de conexão não autorizadas e alertas quando você perde a conexão com os dispositivos.

> [!NOTE]
> Para status de conexão confiável de dispositivos, verifique a [pulsação do dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Comandos da nuvem para o dispositivo

A categoria de comandos da nuvem para o dispositivo rastreia os erros que ocorrem no Hub IoT e estão relacionados ao pipeline de mensagens da nuvem para o dispositivo. Essa categoria inclui erros que ocorrem em:

* Enviando mensagens da nuvem para o dispositivo (como erros de remetente não autorizados),
* O recebimento de mensagens da nuvem para o dispositivo (como a contagem de entrega excedeu erros) e
* Recebendo comentários de mensagem da nuvem para o dispositivo (como erros de comentários expirados).

Essa categoria não captura erros quando a mensagem da nuvem para o dispositivo é entregue com êxito, mas, em seguida, manipulada incorretamente pelo dispositivo.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Operações de identidade do dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando você tenta criar, atualizar ou excluir uma entrada no registro de identidade do Hub IoT. Controlar essa categoria é útil para cenários de provisionamento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Rotas

A categoria roteamento de mensagens rastreia erros que ocorrem durante a avaliação de rota de mensagem e integridade do ponto de extremidade, conforme percebido pelo Hub IoT. Essa categoria inclui eventos como:

* Uma regra é avaliada como "indefinido",
* O Hub IoT marca um ponto de extremidade como inoperante ou
* Todos os erros recebidos de um ponto de extremidade. 

Essa categoria não inclui erros específicos sobre as mensagens em si (como erros de limitação de dispositivo), que são relatados na categoria "telemetria do dispositivo".

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Telemetria do dispositivo

A categoria de telemetria do dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline de telemetria. Essa categoria inclui erros que ocorrem durante o envio de eventos de telemetria (como limitação) e o recebimento de eventos de telemetria (como o leitor não autorizado). Essa categoria não pode capturar erros causados pelo código em execução no próprio dispositivo.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Operações de carregamento de ficheiros

A categoria de upload de arquivo rastreia os erros que ocorrem no Hub IoT e estão relacionados à funcionalidade de carregamento de arquivo. Essa categoria inclui:

* Erros que ocorrem com o URI de SAS, como quando ele expira antes que um dispositivo notifique o Hub de um upload concluído.

* Carregamentos com falha relatados pelo dispositivo.

* Erros que ocorrem quando um arquivo não é encontrado no armazenamento durante a criação da mensagem de notificação do Hub IoT.

Essa categoria não pode capturar erros que ocorrem diretamente enquanto o dispositivo está carregando um arquivo para armazenamento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operações de entrelaçamento da nuvem para o dispositivo

A categoria de operações de entrelaçamento da nuvem para o dispositivo rastreia eventos iniciados pelo serviço no dispositivo gêmeos. Essas operações podem incluir obter, atualizar ou substituir marcas e atualizar ou substituir as propriedades desejadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operações de entrelaçamento do dispositivo para a nuvem

A categoria de operações de entrelaçamento do dispositivo para a nuvem controla eventos iniciados pelo dispositivo no dispositivo gêmeos. Essas operações podem incluir Get entrelaçar, atualizar Propriedades relatadas e assinar as propriedades desejadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Consultas de entrelaçamento

A categoria de consultas de entrelaçamento relata sobre solicitações de consulta para dispositivos gêmeos que são iniciados na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Operações de tarefas

A categoria de operações de trabalhos relata as solicitações de trabalho para atualizar dispositivos gêmeos ou invocar métodos diretos em vários dispositivos. Essas solicitações são iniciadas na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Métodos diretos

A categoria métodos diretos rastreia as interações de solicitação-resposta enviadas a dispositivos individuais. Essas solicitações são iniciadas na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Rastreamento distribuído (visualização)

A categoria de rastreamento distribuído controla as IDs de correlação para mensagens que carregam o cabeçalho de contexto de rastreamento. Para habilitar esses logs totalmente, o código do lado do cliente deve ser atualizado ao seguir [analisar e diagnosticar aplicativos IOT de ponta a ponta com o rastreamento distribuído do Hub IOT (versão prévia)](iot-hub-distributed-tracing.md).

Observe que `correlationId` está em conformidade com a proposta de [contexto de rastreamento W3C](https://github.com/w3c/trace-context) , onde ele contém uma `trace-id`, bem como uma `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Logs de D2C do Hub IoT (dispositivo para nuvem)

O Hub IoT registra esse log quando uma mensagem contendo propriedades de rastreamento válidas chega ao Hub IoT.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Aqui, `durationMs` não é calculada, pois o relógio do Hub IoT pode não estar sincronizado com o relógio do dispositivo e, portanto, um cálculo de duração pode ser enganoso. É recomendável escrever a lógica usando os carimbos de data/hora na seção `properties` para capturar picos na latência do dispositivo para a nuvem.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **Mensagens** | Número inteiro | O tamanho da mensagem do dispositivo para a nuvem em bytes |
| **deviceId** | Cadeia de caracteres alfanuméricos ASCII de 7 bits | A identidade do dispositivo |
| **callerLocalTimeUtc** | Carimbo de data/hora UTC | A hora de criação da mensagem, conforme relatado pelo relógio local do dispositivo |
| **calleeLocalTimeUtc** | Carimbo de data/hora UTC | A hora da chegada da mensagem no gateway do Hub IoT conforme relatado pelo relógio do lado do serviço do Hub IoT |

##### <a name="iot-hub-ingress-logs"></a>Logs de entrada do Hub IoT

O Hub IoT registra esse log quando a mensagem que contém as propriedades de rastreamento válidas é gravada no Hub de eventos internos ou interno.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Na seção `properties`, esse log contém informações adicionais sobre a entrada da mensagem.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Cadeia | True ou false, indica se o roteamento de mensagens está habilitado ou não no Hub IoT |
| **parentSpanId** | Cadeia | O [span-ID](https://w3c.github.io/trace-context/#parent-id) da mensagem pai, que seria o rastreamento de mensagem D2C nesse caso |

##### <a name="iot-hub-egress-logs"></a>Logs de egresso do Hub IoT

O Hub IoT registra esse log quando o [Roteamento](iot-hub-devguide-messages-d2c.md) está habilitado e a mensagem é gravada em um [ponto de extremidade](iot-hub-devguide-endpoints.md). Se o roteamento não estiver habilitado, o Hub IoT não registrará esse log.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Na seção `properties`, esse log contém informações adicionais sobre a entrada da mensagem.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | Cadeia | O nome do ponto de extremidade de roteamento |
| **endpointType** | Cadeia | O tipo do ponto de extremidade de roteamento |
| **parentSpanId** | Cadeia | O [span-ID](https://w3c.github.io/trace-context/#parent-id) da mensagem pai, que seria o rastreamento de mensagem de entrada do Hub IOT nesse caso |

#### <a name="configurations"></a>Configurações

Os logs de configuração do Hub IoT rastreia eventos e erros para o conjunto de recursos de gerenciamento automático de dispositivos.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Fluxos de dispositivo (visualização)

A categoria fluxos de dispositivo rastreia as interações de solicitação-resposta enviadas a dispositivos individuais.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Ler logs de hubs de eventos do Azure

Depois de configurar o log de eventos por meio de configurações de diagnóstico, você pode criar aplicativos que leem os logs para que você possa tomar medidas com base nas informações contidas neles. Este código de exemplo recupera logs de um hub de eventos:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Usar Azure Resource Health

Use Azure Resource Health para monitorar se o Hub IoT está ativo e em execução. Você também pode saber se uma interrupção regional está afetando a integridade do Hub IoT. Para entender detalhes específicos sobre o estado de integridade do Hub IoT do Azure, recomendamos que você [Use Azure monitor](#use-azure-monitor).

O Hub IoT do Azure indica a integridade em um nível regional. Se uma interrupção regional impactar o Hub IoT, o status de integridade aparecerá como **desconhecido**. Para saber mais, consulte [tipos de recursos e verificações de integridade no Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

Para verificar a integridade de seus hubs IoT, siga estas etapas:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

2. Navegue até **integridade do serviço** > **Resource Health**.

3. Nas caixas suspensas, selecione sua assinatura e selecione **Hub IOT** como o tipo de recurso.

Para saber mais sobre como interpretar os dados de integridade, confira [visão geral do Azure Resource Health](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Passos seguintes

* [Entender as métricas do Hub IoT](iot-hub-metrics.md)
* [Monitoramento remoto de IoT e notificações com aplicativos lógicos do Azure conectando seu hub IoT e caixa de correio](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
