---
title: Monitorize a saúde do seu Azure IoT Hub Microsoft Docs
description: Use o Azure Monitor e a Azure Resource Health para monitorizar o seu Hub IoT e diagnosticar rapidamente problemas
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 100f87b8a13fb424706c3b5ec13268cd3ba42bbe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89438406"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorizar o estado de funcionamento do Hub IoT do Azure e diagnosticar problemas rapidamente

As empresas que implementam o Azure IoT Hub esperam um desempenho fiável dos seus recursos. Para ajudá-lo a manter uma vigilância atenta sobre as suas operações, o IoT Hub está totalmente integrado com [o Azure Monitor](../azure-monitor/index.yml) e [a Azure Resource Health](../service-health/resource-health-overview.md). Estes dois serviços funcionam para lhe fornecer os dados necessários para manter as suas soluções IoT em funcionamento em estado saudável.

O Azure Monitor é uma única fonte de monitorização e registo de todos os seus serviços Azure. Pode enviar os registos de diagnóstico que o Azure Monitor gera para registos do Monitor Azure, Centros de Eventos ou Armazenamento Azure para processamento personalizado. As métricas e as definições de diagnóstico do Azure Monitor dão-lhe visibilidade no desempenho dos seus recursos. Continuar a ler Este artigo para aprender a [usar o Azure Monitor](#use-azure-monitor) com o seu hub IoT. 

> [!IMPORTANT]
> Os eventos emitidos pelo serviço IoT Hub utilizando registos de diagnóstico Azure Monitor não são garantidos como fiáveis ou ordenados. Alguns eventos podem ser perdidos ou entregues fora de ordem. Os registos de diagnóstico também não são feitos para ser em tempo real, e pode levar vários minutos para que os eventos sejam registados à sua escolha de destino.

A Azure Resource Health ajuda-o a diagnosticar e a obter apoio quando um problema do Azure impacta os seus recursos. Um dashboard fornece o estado de saúde atual e passado para cada um dos seus centros IoT. Continue na secção na parte inferior deste artigo para aprender a usar a [Azure Resource Health](#use-azure-resource-health) com o seu hub IoT. 

O IoT Hub também fornece as suas próprias métricas que você pode usar para entender o estado dos seus recursos IoT. Para saber mais, consulte [as métricas do IoT Hub.](iot-hub-metrics.md)

## <a name="use-azure-monitor"></a>Utilizar o Azure Monitor

O Azure Monitor fornece informações de diagnóstico para recursos Azure, o que significa que você pode monitorizar as operações que ocorrem dentro do seu hub IoT.

Para saber mais sobre as métricas e eventos específicos que o Azure Monitor observa, consulte [métricas suportadas com a azure Monitor](../azure-monitor/platform/metrics-supported.md) e [serviços apoiados, esquemas e categorias para Registos de Diagnóstico Azure.](../azure-monitor/platform/diagnostic-logs-schema.md)

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Entender os registos

O Azure Monitor rastreia diferentes operações que ocorrem no Hub IoT. Cada categoria tem um esquema que define como os eventos nessa categoria são relatados.

#### <a name="connections"></a>Ligações

A categoria de ligações rastreia o dispositivo conectar e desligar eventos de um hub IoT, bem como erros. Esta categoria é útil para identificar tentativas de ligação não autorizadas e ou alertar quando perde a ligação aos dispositivos.

> [!NOTE]
> Para obter uma ligação fiável dos dispositivos, verifique [o batimento cardíaco do dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).

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
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Comandos nuvem-a-dispositivo

A categoria de comandos cloud-to-device rastreia erros que ocorrem no hub IoT e estão relacionados com o pipeline de mensagens nuvem-para-dispositivo. Esta categoria inclui erros que ocorrem a partir de:

* Envio de mensagens em nuvem para dispositivo (como erros não autorizados de remetente),
* Receber mensagens nuvem-dispositivo (como a contagem de entregas excedeu erros), e
* Receber feedback de mensagens nuvem-a-dispositivo (como erros expirados).

Esta categoria não apanha erros quando a mensagem nuvem-a-dispositivo é entregue com sucesso, mas depois manuseada indevidamente pelo dispositivo.

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

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando tenta criar, atualizar ou apagar uma entrada no registo de identidade do seu hub IoT. Rastrear esta categoria é útil para cenários de provisionamento.

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

A categoria [de encaminhamento de mensagens](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) rastreia erros que ocorrem durante a avaliação da rota da mensagem e saúde do ponto final como percebido pelo IoT Hub. Esta categoria inclui eventos como:

* Uma regra avalia a "indefinida",
* IoT Hub marca um ponto final como morto, ou
* Quaisquer erros recebidos de um ponto final.

Esta categoria não inclui erros específicos sobre as próprias mensagens (como erros de estrangulamento do dispositivo), que são relatados na categoria "telemetria do dispositivo".

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Aqui estão mais detalhes sobre registos de diagnóstico de encaminhamento:

* [Lista de códigos de erro de registo de diagnóstico de encaminhamento](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lista de operação de registos de diagnóstico de encaminhamentoNames](troubleshoot-message-routing.md#diagnostics-operation-names)

#### <a name="device-telemetry"></a>Telemetria do dispositivo

A categoria de telemetria do dispositivo rastreia erros que ocorrem no hub IoT e estão relacionados com o gasoduto de telemetria. Esta categoria inclui erros que ocorrem ao enviar eventos de telemetria (como estrangulamento) e receber eventos de telemetria (como leitor não autorizado). Esta categoria não pode apanhar erros causados pelo código em funcionamento no próprio dispositivo.

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

A categoria de upload de ficheiros rastreia os erros que ocorrem no hub IoT e estão relacionados com a funcionalidade de upload de ficheiros. Esta categoria inclui:

* Erros que ocorrem com o SAS URI, como quando expira antes de um dispositivo notificar o centro de um upload concluído.

* Uploads falhados reportados pelo dispositivo.

* Erros que ocorrem quando um ficheiro não é encontrado no armazenamento durante a criação de mensagens de notificação IoT Hub.

Esta categoria não pode apanhar erros que ocorrem diretamente enquanto o dispositivo está a carregar um ficheiro para armazenamento.

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

#### <a name="cloud-to-device-twin-operations"></a>Operações gémeas nuvem-para-dispositivo

A categoria de operações gémeas nuvem-a-dispositivo rastreia eventos iniciados pelo serviço em gémeos de dispositivos. Estas operações podem incluir obter etiquetas gémeas, atualizar ou substituir, e atualizar ou substituir as propriedades desejadas.

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

#### <a name="device-to-cloud-twin-operations"></a>Operações gémeas dispositivo-a-nuvem

A categoria de operações gémeas dispositivo-a-nuvem rastreia eventos iniciados pelo dispositivo em gémeos dispositivos. Estas operações podem incluir obter twin, atualizar propriedades reportadas e subscrever as propriedades desejadas.

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

#### <a name="twin-queries"></a>Consultas gémeas

A categoria de consultas gémeas relata pedidos de consulta para gémeos de dispositivo que são iniciados na nuvem.

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

A categoria de operações de emprego reporta pedidos de emprego para atualizar gémeos de dispositivos ou invocar métodos diretos em vários dispositivos. Estes pedidos são iniciados na nuvem.

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

#### <a name="direct-methods"></a>Métodos Diretos

A categoria de métodos diretos acompanha as interações de resposta de pedido enviadas a dispositivos individuais. Estes pedidos são iniciados na nuvem.

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

#### <a name="distributed-tracing-preview"></a>Rastreio distribuído (pré-visualização)

A categoria de rastreio distribuído rastreia os IDs de correlação para mensagens que transportam o cabeçalho de contexto de traços. Para ativar totalmente estes registos, o código do lado do cliente deve ser atualizado seguindo [as aplicações IoT de ponta a ponta com o rastreio distribuído do IoT Hub (pré-visualização)](iot-hub-distributed-tracing.md).

Note que `correlationId` está em conformidade com a proposta [W3C Trace Context,](https://github.com/w3c/trace-context) onde contém `trace-id` um bem como um `span-id` .

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Troncos IoT Hub D2C (dispositivo-nuvem)

O IoT Hub regista este registo quando uma mensagem que contém propriedades de vestígios válidos chega ao IoT Hub.

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

Aqui, `durationMs` não é calculado como o relógio do IoT Hub pode não estar sincronizado com o relógio do dispositivo, e assim um cálculo de duração pode ser enganador. Recomendamos escrever lógica usando os tempos na `properties` secção para capturar picos na latência dispositivo-nuvem.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **mensagemSize** | Número inteiro | O tamanho da mensagem dispositivo-a-nuvem em bytes |
| **deviceId** | Cadeia de caracteres alfanuméricos ASCII de 7 bits | A identidade do dispositivo |
| **chamadorLocalTimeUtc** | Calendário UTC | O tempo de criação da mensagem como relatado pelo relógio local do dispositivo |
| **calleeLocalTimeUtc** | Calendário UTC | A hora da chegada da mensagem à porta de entrada do IoT Hub, conforme relatado pelo relógio lateral de serviço IoT Hub |

##### <a name="iot-hub-ingress-logs"></a>Troncos de entrada do IoT Hub

O IoT Hub grava este registo quando a mensagem que contém propriedades de vestígios válidos escreve para o Centro de Eventos interno ou incorporado.

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

Na `properties` secção, este registo contém informações adicionais sobre a entrada de mensagens.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Cadeia | Verdadeiro ou falso, indica se o encaminhamento de mensagens está ou não ativado no Hub IoT |
| **parentSpanId** | Cadeia | O [span-id](https://w3c.github.io/trace-context/#parent-id) da mensagem principal, que seria o traço de mensagem D2C neste caso |

##### <a name="iot-hub-egress-logs"></a>Troncos de saída do IoT Hub

IoT Hub grava este registo quando [o encaminhamento](iot-hub-devguide-messages-d2c.md) está ativado e a mensagem é escrita para um [ponto final](iot-hub-devguide-endpoints.md). Se o encaminhamento não estiver ativado, o IoT Hub não grava este registo.

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

Na `properties` secção, este registo contém informações adicionais sobre a entrada de mensagens.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | Cadeia | O nome do ponto final de encaminhamento |
| **endpointType** | Cadeia | O tipo de ponto final de encaminhamento |
| **parentSpanId** | Cadeia | O [span-id](https://w3c.github.io/trace-context/#parent-id) da mensagem dos pais, que seria o traço de mensagem de entrada IoT Hub neste caso |

#### <a name="configurations"></a>Configurações

Os registos de configuração do IoT Hub rastreiam eventos e erros para o conjunto de funções de gestão automática de dispositivos.

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

### <a name="device-streams-preview"></a>Streams de dispositivos (pré-visualização)

A categoria de streams de dispositivos rastreia as interações de resposta de pedido enviadas para dispositivos individuais.

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

### <a name="sdk-version"></a>Versão do SDK

Algumas operações devolvem uma `sdkVersion` propriedade no `properties` objeto. Para estas operações, quando um dispositivo ou app backend está a usar um dos SDKs Azure IoT, esta propriedade contém informações sobre o SDK que está a ser utilizado, a versão SDK e a plataforma em que o SDK está em execução. O exemplo a seguir mostra a `sdkVersion` propriedade emitida para uma `deviceConnect` operação quando utiliza o dispositivo de Node.js SDK: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Aqui está um exemplo do valor emitido para o .NET (C#) SDK: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

A tabela a seguir mostra o nome SDK usado para diferentes SDK Azure IoT:

| Nome SDK na propriedade SdkVersion | Idioma |
|----------|----------|
| .NET | .NET (C#) |
| microsoft.azure.dispositivos | .NET (C#) serviço SDK |
| microsoft.azure.devices.client | .NET (C#) dispositivo SDK |
| iothubclient | Dispositivo C ou Python v1 (preceded) SDK |
| iothubserviceclient | Serviço C ou Python v1 (preceded) SDK |
| azure-iot-device-iothub-py | Dispositivo python SDK |
| azure-iot-device | Node.js dispositivo SDK |
| azure-iothub | serviço Node.js SDK |
| com.microsoft.azure.iothub-java-cliente | Dispositivo Java SDK |
| com.microsoft.azure.iothub.service.sdk | Serviço Java SDK |
| com.microsoft.azure.sdk.iot.iot-device-client | Dispositivo Java SDK |
| com.microsoft.azure.sdk.iot.iot-service-client | Serviço Java SDK |
| C | Incorporado C |
| C + (OSSimplificado = Azure RTOS) | Azure RTOS |

Pode extrair a propriedade da versão SDK quando efetuar consultas contra registos de diagnóstico. A seguinte consulta extrai a propriedade da versão SDK (e iD do dispositivo) das propriedades devolvidas por eventos de Conexões. Estas duas propriedades são escritas para os resultados juntamente com a hora do evento e o ID de recursos do hub IoT a que o dispositivo está a ligar.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="read-logs-from-azure-event-hubs"></a>Leia registos dos Hubs de Eventos Azure

Depois de configurar o registo de eventos através de definições de diagnóstico, pode criar aplicações que leiam os registos para que possa tomar medidas com base nas informações neles. Este código de amostra obtém registos de um centro de eventos:

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

## <a name="use-azure-resource-health"></a>Utilizar a Azure Resource Health

Utilize a Azure Resource Health para monitorizar se o seu hub IoT está a funcionar. Também pode aprender se uma paragem regional está a afetar a saúde do seu hub IoT. Para compreender detalhes específicos sobre o estado de saúde do seu Azure IoT Hub, recomendamos que [utilize o Azure Monitor](#use-azure-monitor).

O Azure IoT Hub indica saúde a nível regional. Se uma paralisação regional afetar o seu hub de IoT, o estado de saúde mostra como **Desconhecido**. Para saber mais, consulte [tipos de recursos e verificações de saúde na saúde dos recursos Azure.](../service-health/resource-health-checks-resource-types.md)

Para verificar a saúde dos seus centros de IoT, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegar para a saúde do **Recurso de Saúde do Serviço.**  >  **Resource health**

3. A partir das caixas de entrega, selecione a sua subscrição e selecione **O Hub IoT** como o tipo de recurso.

Para saber mais sobre como interpretar dados de saúde, consulte [a visão geral da saúde dos recursos Azure](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Passos seguintes

* [Compreender as métricas do IoT Hub](iot-hub-metrics.md)
* [Monitorização remota IoT e notificações com Azure Logic Apps que ligam o seu hub IoT e caixa de correio](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
