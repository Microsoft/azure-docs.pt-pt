---
title: Monitorizar o estado de funcionamento do seu IoT Hub do Azure | Documentos da Microsoft
description: Utilizar o Azure Monitor e do Azure Resource Health para monitorizar o seu IoT Hub e diagnosticar problemas rapidamente
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: kgremban
ms.openlocfilehash: 0a230ff1c4d5c6bb36003f07cc1c411f7e2c3629
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241005"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorizar o estado de funcionamento do IoT Hub do Azure e diagnosticar problemas rapidamente

As empresas que implementam o IoT Hub do Azure esperam um desempenho fiável dos seus recursos. Para ajudar a manter uma veja fechar a suas operações, o IoT Hub está totalmente integrado [do Azure Monitor](../azure-monitor/index.yml) e [do Azure Resource Health](../service-health/resource-health-overview.md). Estes dois serviços trabalham para lhe fornecer os dados que necessários para manter as suas soluções de IoT de cópia de segurança e em execução em bom estado de funcionamento. 

Monitor do Azure é uma única origem de monitorização e registo para todos os seus serviços do Azure. Pode enviar os registos de diagnóstico do Azure Monitor gera registos do Azure Monitor, os Hubs de eventos ou armazenamento do Azure para processamento personalizado. Definições de métricas e diagnósticos do Monitor do Azure dão-lhe visibilidade para o desempenho dos seus recursos. Continue a ler este artigo para saber como [utilização do Azure Monitor](#use-azure-monitor) com o seu hub IoT. 

> [!IMPORTANT]
> Não são garantidos que os eventos emitidos pelo serviço do IoT Hub com os registos de diagnóstico do Azure Monitor ser ordenada ou confiável. Alguns eventos podem ser perdidos ou ser entregue fora de ordem. Os registos de diagnóstico também não devem ser em tempo real e poderá demorar alguns minutos para eventos em log à sua escolha de destino.

Estado de funcionamento de recursos do Azure ajuda-o a diagnosticar e obter suporte quando um problema do Azure afeta os seus recursos. Um dashboard fornece o estado de funcionamento atuais e anteriores para cada um dos seus hubs IoT. Avance para a secção na parte inferior deste artigo para saber como [utilização do Azure Resource Health](#use-azure-resource-health) com o seu hub IoT. 

IoT Hub também fornece seu próprio métricas que pode utilizar para compreender o estado dos seus recursos de IoT. Para obter mais informações, consulte [métricas de compreender o IoT Hub](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Utilizar o Azure Monitor

O Azure Monitor fornece informações de diagnóstico para recursos do Azure, que significa que pode monitorizar as operações que ocorrem dentro do seu hub IoT. 

Monitorizar a substitui de definições de diagnóstico do Azure Monitor as operações do IoT Hub. Se utilizar atualmente a monitorização de operações, deve migrar os seus fluxos de trabalho. Para obter mais informações, consulte [migrar de operações de definições de monitorização para o diagnóstico](iot-hub-migrate-to-diagnostics-settings.md).

Para saber mais sobre as métricas específicas e os eventos que observa o Azure Monitor, consulte [suportado métricas com o Azure Monitor](../azure-monitor/platform/metrics-supported.md) e [suportado serviços, os esquemas e categorias para os registos de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Compreender os registos

O Azure Monitor controla as operações diferentes que ocorrem no IoT Hub. Cada categoria tem um esquema que define a forma como os eventos nessa categoria são comunicados. 

#### <a name="connections"></a>Ligações

O dispositivo de faixas de categoria ligações ligar e desligar eventos a partir de um hub IoT, bem como erros. Esta categoria é útil para identificar as tentativas de ligação não autorizado e ou alertas quando a perder a ligação aos dispositivos.

> [!NOTE]
> Para obter o estado de ligação fiável de dispositivos de verificação [heartbeat de dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).


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

#### <a name="cloud-to-device-commands"></a>Comandos de cloud para dispositivo

A categoria de comandos do cloud-para-dispositivo rastreia erros ocorridos no IoT hub e estão relacionadas com o pipeline de mensagens da cloud para o dispositivo. Esta categoria inclui erros que ocorrem a partir de:

* Envio de mensagens de cloud-para-dispositivo (como erros de remetente não autenticado),
* Receber mensagens da cloud para o dispositivo (como erros de contagem de excedida de entrega), e
* Receber comentários de mensagem de cloud-para-dispositivo (como comentários expirado erros). 

Esta categoria não detectar erros quando a mensagem de cloud para o dispositivo é entregue com êxito, mas, em seguida, incorretamente, processada pelo dispositivo.

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
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Operações de identidade de dispositivo

A categoria de operações de identidade de dispositivo rastreia erros que ocorrem quando está tentando criar, atualizar ou eliminar uma entrada no registo de identidade do hub IoT. Esta categoria de controlo é útil para cenários de aprovisionamento.

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

A categoria de roteamento de mensagem rastreia erros que ocorrem durante a avaliação de rota de mensagem e o estado de funcionamento do ponto de extremidade como percebido pelo IoT Hub. Esta categoria inclui eventos como:

* Uma regra avalia como "undefined",
* IoT Hub marca um ponto de extremidade como morta, ou
* Erros recebidos de um ponto de extremidade. 

Esta categoria não inclui erros específicos sobre as mensagens propriamente ditos (como dispositivo de erros de limitação), que são apresentadas na categoria "telemetria do dispositivo".

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

A categoria de telemetria do dispositivo rastreia erros ocorridos no IoT hub e estão relacionadas com o pipeline de telemetria. Esta categoria inclui erros que ocorrem quando o envio de eventos de telemetria (por exemplo, a limitação) e a receção de eventos de telemetria (por exemplo, o leitor não autorizado). Esta categoria não pode detetar erros causados por código em execução no próprio dispositivo.

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

A categoria de carregamento do ficheiro rastreia erros ocorridos no IoT hub e estão relacionadas com a funcionalidade de carregamento do ficheiro. Esta categoria inclui:

* Erros que ocorrem ao URI de SAS, por exemplo, quando este expirar antes de um dispositivo notifica o hub de um carregamento concluído.
* Não foi possível carregamentos comunicados pelo dispositivo.
* Erros que ocorrem quando um ficheiro não foi encontrado no armazenamento durante a criação de mensagem de notificação do IoT Hub.

Esta categoria não pode detetar erros que ocorram diretamente enquanto o dispositivo está a carregar um ficheiro para o armazenamento.

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

#### <a name="cloud-to-device-twin-operations"></a>Operações de cloud para o dispositivo duplo

A categoria de operações na cloud para o dispositivo duplo regista os eventos de iniciadas pelo serviço em dispositivos duplos. Estas operações podem incluir get duplo, atualizar ou substituir marcas e atualizar ou substituir as propriedades pretendidas. 

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

#### <a name="device-to-cloud-twin-operations"></a>Operações de gémeos de dispositivo para a cloud

A categoria de operações de gémeos de dispositivo-para-cloud regista os eventos de iniciadas por dispositivos em dispositivos duplos. Estas operações podem incluir duplo de get, atualizar propriedades comunicadas e subscrever as propriedades pretendidas.

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

#### <a name="twin-queries"></a>Consultas de gémeos

A categoria de consultas de gémeos relatórios nos pedidos de consulta para dispositivos duplos, que são iniciados na cloud. 

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

A categoria de operações de tarefas relatórios nos pedidos de tarefa para atualizar dispositivos duplos ou invocar métodos diretos em vários dispositivos. Estes pedidos são iniciados na cloud. 

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

A categoria de métodos diretos controla as interações de solicitação-resposta enviadas para dispositivos individuais. Estes pedidos são iniciados na cloud. 

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

#### <a name="distributed-tracing-preview"></a>Distribuído de rastreamento (pré-visualização)

A categoria de rastreio distribuído controla IDs de correlação para as mensagens que carregam o cabeçalho de contexto de rastreio. Para ativar completamente estes registos, código do lado do cliente tem de ser atualizado seguindo [analisar e diagnosticar IoT aplicativos ponto-a-ponto com o rastreio distribuído do IoT Hub (pré-visualização)](iot-hub-distributed-tracing.md).

Tenha em atenção que `correlationId` está em conformidade com o [contexto de rastreio do W3C](https://github.com/w3c/trace-context) proposta, em que ele contém uma `trace-id` , bem como um `span-id`. 

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Registos do IoT Hub D2C (dispositivo-para-cloud)

IoT Hub regista este registo quando uma mensagem que contém as propriedades de rastreio válido chega ao IoT Hub. 

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

Aqui, `durationMs` não é calculado como o relógio do IoT Hub pode não estar sincronizado com o relógio do dispositivo e, portanto, um cálculo de duração pode ser enganoso. Recomendamos que escrever lógica usando os carimbos de data / no `properties` secção para capturar os picos de latência de dispositivo para a cloud.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Número inteiro | O tamanho de mensagem de dispositivo para a cloud em bytes |
| **deviceId** | Cadeia de caracteres de alfanuméricos ASCII de 7 bits | A identidade do dispositivo |
| **callerLocalTimeUtc** | Timestamp de UTC | Hora de criação da mensagem, conforme comunicado pelo relógio local do dispositivo |
| **calleeLocalTimeUtc** | Timestamp de UTC | A hora da chegada de mensagens ao gateway do IoT Hub, conforme comunicado pelo relógio de lado do serviço IoT Hub |

##### <a name="iot-hub-ingress-logs"></a>Registos de entrada do IoT Hub

IoT Hub regista este registo quando mensagem que contém as propriedades de rastreio válido escreve para o Hub de eventos internos ou incorporados.

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

Na `properties` secção, este registo contém informações adicionais sobre a entrada de mensagem

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | VERDADEIRO ou FALSO, indica se é ou não, roteamento de mensagens está ativada no IoT Hub |
| **parentSpanId** | String | O [id da marca span](https://w3c.github.io/trace-context/#parent-id) da mensagem principal, que seria neste caso o rastreio de mensagens D2C |

##### <a name="iot-hub-egress-logs"></a>Registos de saída do IoT Hub

Registos do Hub IoT isso quando iniciar sessão [encaminhamento](iot-hub-devguide-messages-d2c.md) está ativada e a mensagem é escrita um [endpoint](iot-hub-devguide-endpoints.md). Se não estiver ativado para o encaminhamento, o IoT Hub não registe este registo.

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

Na `properties` secção, este registo contém informações adicionais sobre a entrada de mensagem

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | O nome do ponto final de encaminhamento |
| **endpointType** | String | O tipo do encaminhamento ponto final |
| **parentSpanId** | String | O [id da marca span](https://w3c.github.io/trace-context/#parent-id) da mensagem principal, que seria neste caso o rastreio de mensagem de entrada do IoT Hub |


### <a name="read-logs-from-azure-event-hubs"></a>Registos de leitura a partir dos Hubs de eventos do Azure

Depois de configurar o registo através das definições de diagnóstico de eventos, pode criar aplicativos que lê os registos para que pode agir com base nas informações nos mesmos. Este código de exemplo obtém os registos de um hub de eventos:

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

## <a name="use-azure-resource-health"></a>Utilizar o estado de funcionamento de recursos do Azure

Utilize o Azure Resource Health para monitorizar se o seu hub IoT está em execução. Também pode saber se uma falha regional está a afetar o estado de funcionamento do seu hub IoT. Para compreender os detalhes específicos sobre o estado de funcionamento do seu IoT Hub do Azure, recomendamos que [utilização do Azure Monitor](#use-azure-monitor). 

O IoT Hub do Azure indica o estado de funcionamento num nível regional. Se uma falha regional tem impacto no seu hub IoT, o estado de funcionamento é apresentado como **desconhecido**. Para obter mais informações, consulte [tipos de recursos e o estado de funcionamento verifica-se no estado de funcionamento de recursos do Azure](../service-health/resource-health-checks-resource-types.md).

Para verificar o estado de funcionamento os hubs IoT, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para **estado de funcionamento do serviço** > **estado de funcionamento do recurso**.
1. A partir de caixas de lista pendente, selecione a sua subscrição, em seguida, selecione **IoT Hub** como o tipo de recurso.

Para saber mais sobre como interpretar os dados de estado de funcionamento, veja [descrição geral do Estado de funcionamento de recursos do Azure](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Passos Seguintes

- [Compreender as métricas do IoT Hub](iot-hub-metrics.md)
- [A monitorização remota do IoT e notificações com o Azure Logic Apps, ligar o seu IoT hub e caixa de correio](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

