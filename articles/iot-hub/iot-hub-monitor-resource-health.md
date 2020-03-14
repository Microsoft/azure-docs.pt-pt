---
title: Monitorize a saúde do seu Hub Azure IoT  Microsoft Docs
description: Use o Azure Monitor e a Azure Resource Health para monitorizar o seu Hub IoT e diagnosticar rapidamente problemas
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271086"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorize a saúde do Hub Azure IoT e diagnostice rapidamente problemas

As empresas que implementam o Azure IoT Hub esperam um desempenho fiável dos seus recursos. Para ajudá-lo a manter um olhar atento sobre as suas operações, o IoT Hub está totalmente integrado com o [Azure Monitor](../azure-monitor/index.yml) e a [Azure Resource Health.](../service-health/resource-health-overview.md) Estes dois serviços trabalham para lhe fornecer os dados necessários para manter as suas soluções IoT em funcionamento num estado saudável.

O Azure Monitor é uma única fonte de monitorização e exploração de madeira para todos os seus serviços Azure. Pode enviar os registos de diagnóstico que o Azure Monitor gera para registos do Monitor Azure, Hubs de Eventos ou Armazenamento Azure para processamento personalizado. As definições de métricas e diagnósticos do Azure Monitor dão-lhe visibilidade no desempenho dos seus recursos. Continuar a ler Este artigo para aprender a usar o [Monitor Azure](#use-azure-monitor) com o seu hub IoT. 

> [!IMPORTANT]
> Os eventos emitidos pelo serviço IoT Hub utilizando registos de diagnóstico do Monitor Azure não são garantidos ou encomendados. Alguns eventos podem ser perdidos ou entregues fora de ordem. Os registos de diagnóstico também não são para ser em tempo real, e pode levar vários minutos para que os eventos sejam registados na sua escolha de destino.

A Azure Resource Health ajuda-o a diagnosticar e a obter apoio quando um problema do Azure afeta os seus recursos. Um dashboard fornece estado de saúde atual e passado para cada um dos seus hubs IoT. Continue na secção na parte inferior deste artigo para aprender a usar a Saúde de [Recursos Azure](#use-azure-resource-health) com o seu hub IoT. 

O IoT Hub também fornece as suas próprias métricas que pode usar para entender o estado dos seus recursos IoT. Para saber mais, consulte as [métricas do Centro De IoT.](iot-hub-metrics.md)

## <a name="use-azure-monitor"></a>Utilizar o Azure Monitor

O Azure Monitor fornece informações de diagnóstico para os recursos Do Azure, o que significa que pode monitorizar as operações que ocorrem dentro do seu hub IoT.

As definições de diagnóstico do Azure Monitor substituem o monitor de operações do IoT Hub. Se utilizar atualmente a monitorização das operações, deverá migrar os seus fluxos de trabalho. Para obter mais informações, consulte [Migrate da monitorização das operações para as definições](iot-hub-migrate-to-diagnostics-settings.md)de diagnóstico .

Para saber mais sobre as métricas e eventos específicos que o Azure Monitor observa, consulte [métricas suportadas com serviços Azure Monitor](../azure-monitor/platform/metrics-supported.md) e [Supported, schemas e categorias para Registos de Diagnóstico Azure.](../azure-monitor/platform/diagnostic-logs-schema.md)

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Compreenda os troncos

O Azure Monitor rastreia diferentes operações que ocorrem no Hub IoT. Cada categoria tem um esquema que define como os eventos nessa categoria são relatados.

#### <a name="connections"></a>Ligações

A categoria de ligações rastreia o dispositivo de ligação e desconexão de eventos de um hub IoT, bem como erros. Esta categoria é útil para identificar tentativas de ligação não autorizadas e alertar quando perde a ligação aos dispositivos.

> [!NOTE]
> Para um estado de ligação fiável dos dispositivos, verifique o [batimento cardíaco](iot-hub-devguide-identity-registry.md#device-heartbeat)do dispositivo .

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

#### <a name="cloud-to-device-commands"></a>Comandos cloud-to-device

A categoria de comandos cloud-to-device rastreia erros que ocorrem no hub IoT e estão relacionados com o gasoduto de mensagens cloud-to-device. Esta categoria inclui erros que ocorrem a partir de:

* Envio de mensagens cloud-to-device (como erros de remetente não autorizados),
* Receber mensagens cloud-to-device (como contagem de entregas excedeu erros), e
* Receber feedback de mensagem cloud-to-device (como erros expirados de feedback).

Esta categoria não se depara com erros quando a mensagem cloud-to-device é entregue com sucesso, mas depois manuseada indevidamente pelo dispositivo.

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

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando tenta criar, atualizar ou apagar uma entrada no registo de identidade do seu hub IoT. O acompanhamento desta categoria é útil para o fornecimento de cenários.

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

A categoria de encaminhamento de mensagens rastreia erros que ocorrem durante a avaliação da rota da mensagem e a saúde do ponto final, como percebido pelo IoT Hub. Esta categoria inclui eventos como:

* Uma regra avalia "indefinido",
* IoT Hub marca um ponto final como morto, ou
* Qualquer erro recebido de um ponto final. 

Esta categoria não inclui erros específicos sobre as próprias mensagens (como erros de estrangulamento do dispositivo), que são relatados na categoria "telemetria do dispositivo".

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

A categoria de upload de ficheiros rastreia erros que ocorrem no hub IoT e estão relacionados com a funcionalidade de upload de ficheiros. Esta categoria inclui:

* Erros que ocorrem com o SAS URI, como quando expira antes de um dispositivo notar o centro de um upload concluído.

* Os uploads falhados reportados pelo dispositivo.

* Erros que ocorrem quando um ficheiro não é encontrado no armazenamento durante a criação de mensagens de notificação do IoT Hub.

Esta categoria não pode apanhar erros que ocorram diretamente enquanto o dispositivo está a enviar um ficheiro para armazenamento.

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

#### <a name="cloud-to-device-twin-operations"></a>Operações duplas cloud-to-device

A categoria de operações duplas cloud-to-device rastreia eventos iniciados pelo serviço em gémeos dispositivos. Estas operações podem incluir obter etiquetas gémeas, atualizar ou substituir, e atualizar ou substituir as propriedades desejadas.

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

#### <a name="device-to-cloud-twin-operations"></a>Operações gémeas dispositivo-nuvem

A categoria de operações de dispositivo-para-nuvem de duas operações rastreia eventos iniciados pelo dispositivo em gémeos dispositivos. Estas operações podem incluir obter twin, atualizar propriedades reportadas e subscrever as propriedades desejadas.

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

A categoria de consultas gémeas relata pedidos de consultas para gémeos dispositivos que são iniciados na nuvem.

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

A categoria de operações de emprego reporta sobre pedidos de emprego para atualizar gémeos de dispositivos ou invocar métodos diretos em vários dispositivos. Estes pedidos são iniciados na nuvem.

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

A categoria de métodos diretos rastreia as interações de resposta a pedidos enviadas para dispositivos individuais. Estes pedidos são iniciados na nuvem.

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

#### <a name="distributed-tracing-preview"></a>Rastreio distribuído (Pré-visualização)

A categoria de rastreio distribuído rastreia as IDs de correlação para mensagens que transportam o cabeçalho do contexto do traço. Para ativar totalmente estes registos, o código do lado do cliente deve ser atualizado seguindo [o Analyze e diagnosticar aplicações IoT de ponta a ponta com rastreio distribuído ioT Hub (pré-visualização)](iot-hub-distributed-tracing.md).

Note que `correlationId` está em conformidade com a proposta de Contexto de [Rastreio w3C,](https://github.com/w3c/trace-context) onde contém uma `trace-id` bem como uma `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (registos dispositivo-a-nuvem)

O IoT Hub regista este registo quando uma mensagem que contém propriedades de vestígios válidas chega ao IoT Hub.

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

Aqui, `durationMs` não é calculado, uma vez que o relógio do IoT Hub pode não estar sincronizado com o relógio do dispositivo, pelo que um cálculo de duração pode ser enganador. Recomendamos a lógica de escrita usando os selos de tempo na secção `properties` para capturar picos na latência dispositivo-a-nuvem.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **mensagemTamanho** | Número inteiro | O tamanho da mensagem dispositivo-para-nuvem em bytes |
| **dispositivoId** | Cadeia de caracteres alfanuméricos de 7 bits ASCII | A identidade do dispositivo |
| **chamadorLocalTimeUtc** | Carimbo de tempo UTC | O tempo de criação da mensagem como relatado pelo relógio local do dispositivo |
| **calleeLocalTimeUtc** | Carimbo de tempo UTC | A hora da chegada da mensagem à porta de entrada do IoT Hub, conforme relatado pelo relógio lateral do ioT Hub |

##### <a name="iot-hub-ingress-logs"></a>Logs de entrada IoT Hub

O IoT Hub regista este registo quando a mensagem que contém propriedades de vestígios válidas escreve para o Centro de Eventos interno ou incorporado.

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

Na secção `properties`, este registo contém informações adicionais sobre a entrada de mensagens.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Seja verdadeiro ou falso, indica se o encaminhamento de mensagens está ou não ativado no Hub IoT |
| **parentSpanId** | String | O [span-id](https://w3c.github.io/trace-context/#parent-id) da mensagem-mãe, que seria o traço de mensagem D2C neste caso |

##### <a name="iot-hub-egress-logs"></a>IoT Hub egress logs

O IoT Hub regista este registo quando o [encaminhamento](iot-hub-devguide-messages-d2c.md) está ativado e a mensagem é escrita para um [ponto final](iot-hub-devguide-endpoints.md). Se o encaminhamento não estiver ativado, o IoT Hub não regista este registo.

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

Na secção `properties`, este registo contém informações adicionais sobre a entrada de mensagens.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **nome final** | String | O nome do ponto final de encaminhamento |
| **ponto finalType** | String | O tipo de ponto final de encaminhamento |
| **parentSpanId** | String | O [span-id](https://w3c.github.io/trace-context/#parent-id) da mensagem dos pais, que seria o rasto de mensagem de entrada IoT Hub neste caso |

#### <a name="configurations"></a>Configurações

Os registos de configuração do IoT Hub rastreiam eventos e erros para o conjunto de funcionalidades de Gestão automática de Dispositivos.

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

### <a name="device-streams-preview"></a>Fluxos de dispositivos (Pré-visualização)

A categoria de streams do dispositivo rastreia as interações de resposta de pedido-resposta enviadas para dispositivos individuais.

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

### <a name="read-logs-from-azure-event-hubs"></a>Leia os registos dos Hubs de Eventos Azure

Depois de configurar o registo de eventos através de definições de diagnóstico, pode criar aplicações que lêem os registos para que possa tomar medidas com base nas informações que os incomodem. Este código de amostra recupera registos de um centro de eventos:

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

## <a name="use-azure-resource-health"></a>Utilizar a Saúde dos Recursos Azure

Utilize a Azure Resource Health para monitorizar se o seu hub IoT está a funcionar. Você também pode aprender se uma paralisação regional está afetando a saúde do seu hub IoT. Para compreender detalhes específicos sobre o estado de saúde do seu Hub Azure IoT, recomendamos que utilize o [Monitor Azure.](#use-azure-monitor)

O Azure IoT Hub indica saúde a nível regional. Se uma paragem regional afetar o seu hub ioT, o estado de saúde mostra como **Desconhecido.** Para saber mais, consulte os tipos de [recursos e os controlos de saúde na saúde dos recursos do Azure.](../service-health/resource-health-checks-resource-types.md)

Para verificar a saúde dos seus hubs ioT, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegar para **a saúde de serviço** > Saúde de **Recursos.**

3. A partir das caixas de entrega, selecione a sua subscrição e, em seguida, selecione **IoT Hub** como o tipo de recurso.

Para saber mais sobre como interpretar os dados de saúde, consulte a [visão geral da saúde dos recursos do Azure.](../service-health/resource-health-overview.md)

## <a name="next-steps"></a>Passos Seguintes

* [Compreender as métricas do IoT Hub](iot-hub-metrics.md)
* [Monitorização e notificações remotas IoT com Aplicações Lógicas Azure que ligam o seu hub IoT e caixa de correio](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
