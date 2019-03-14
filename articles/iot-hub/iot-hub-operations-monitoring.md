---
title: Operações do IoT Hub do Azure (preterido) de monitorização | Documentos da Microsoft
description: Como utilizar a monitorização para monitorizar o estado das operações no hub IoT em tempo real de operações do IoT Hub do Azure.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.openlocfilehash: d839e2e9922ac68af3aea37884e8b2f72b80b0e7
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791584"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Operações do IoT Hub monitorização (preterido)

Monitorização de operações do IoT Hub permite-lhe monitorizar o estado das operações no hub IoT em tempo real. IoT Hub regista os eventos em várias categorias de operações. Pode participar no envio de eventos de um ou mais categorias para um ponto final do hub IoT para processamento. Pode monitorizar os dados de erros ou configurar o processamento mais complexo com base nos padrões de dados.

>[!NOTE]
>IoT Hub **operações de monitorização foi preteridas e foi removidas do IoT Hub, 10 de Março de 2019**. Para monitorizar as operações e o estado de funcionamento do IoT Hub, veja [monitorizar o estado de funcionamento do IoT Hub do Azure e diagnosticar problemas rapidamente][lnk-monitor]. Para obter mais informações sobre a linha cronológica de preterição, consulte [monitorizar as soluções de IoT do Azure com o Azure Monitor e o Azure Resource Health][lnk-blog-announcement].

IoT Hub monitoriza seis categorias de eventos:

* Operações de identidade de dispositivo
* Telemetria do dispositivo
* Mensagens da cloud para dispositivo
* Ligações
* Carrega o ficheiro
* Encaminhamento de mensagens

> [!IMPORTANT]
> Monitorização de operações do IoT Hub não garante a entrega fiável ou ordenada de eventos. Dependendo da infraestrutura subjacente do IoT Hub, alguns eventos podem ser perdidos ou entregues fora de ordem. Utilize operações de monitorização para gerar alertas com base em sinais de erro, como as tentativas de ligação com falhas ou interrupções de ligação de elevada frequência para dispositivos específicos. Não deverá confiar nas operações de monitorização de eventos para criar um armazenamento consistente para o estado do dispositivo, por exemplo, um arquivo de controle ligado ou desligado o estado de um dispositivo. 

## <a name="how-to-enable-operations-monitoring"></a>Como ativar a monitorização de operações

1. Crie um hub IoT. Pode encontrar instruções sobre como criar um hub IoT a [começar] [ lnk-get-started] guia.

1. Abra o painel do IoT hub. A partir daí, clique em **monitorização de operações**.

    ![Monitorização de configuração no portal de operações de acesso][1]

1. Selecione as categorias de monitorização que pretende monitorizar e, em seguida, clique em **guardar**. Os eventos estão disponíveis para leitura a partir do ponto final compatível com o Event Hub listado na **definições de monitorização**. O ponto de final do IoT Hub é chamado `messages/operationsmonitoringevents`.

    ![Configurar operações de monitorização no seu hub IoT][2]

> [!NOTE]
> Selecionando **verboso** de monitorização para o **ligações** categoria faz com que o IoT Hub gerar mensagens de diagnóstico adicionais. Para todas as outras categorias, o **verboso** definição alterações a quantidade de informações do IoT Hub inclui em cada mensagem de erro.

## <a name="event-categories-and-how-to-use-them"></a>Categorias de eventos e como utilizá-los

Cada categoria faixas de monitorização de operações um tipo diferente de interação com o IoT Hub e cada categoria de monitorização tem um esquema que define a forma como são estruturados eventos nessa categoria.

### <a name="device-identity-operations"></a>Operações de identidade de dispositivo

A categoria de operações de identidade de dispositivo rastreia erros que ocorrem quando está tentando criar, atualizar ou eliminar uma entrada no registo de identidade do hub IoT. Esta categoria de controlo é útil para cenários de aprovisionamento.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetria do dispositivo

A categoria de telemetria do dispositivo rastreia erros ocorridos no IoT hub e estão relacionadas com o pipeline de telemetria. Esta categoria inclui erros que ocorrem quando o envio de eventos de telemetria (por exemplo, a limitação) e a receção de eventos de telemetria (por exemplo, o leitor não autorizado). Esta categoria não pode detetar erros causados por código em execução no próprio dispositivo.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Comandos de cloud para dispositivo

A categoria de comandos do cloud-para-dispositivo rastreia erros ocorridos no IoT hub e estão relacionadas com o pipeline de mensagens da cloud para o dispositivo. Esta categoria inclui erros que ocorrem quando enviar mensagens da cloud para o dispositivo (por exemplo, o remetente não autenticado), receber mensagens da cloud para o dispositivo (por exemplo, a contagem de entrega excedida) e receber comentários de mensagem de cloud-para-dispositivo (como comentários expiraram). Esta categoria não detectar erros de um dispositivo que trata incorretamente uma mensagem de cloud para o dispositivo se a mensagem de cloud para o dispositivo foi entregue com êxito.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Ligações

A categoria de ligações rastreia erros que ocorrem quando os dispositivos ligarem ou desligar de um hub IoT. Esta categoria de controlo é útil para identificar as tentativas de ligação não autorizado e para controlar quando uma conexão está perdida para dispositivos em áreas de conectividade ruim.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Carrega o ficheiro

A categoria de carregamento do ficheiro rastreia erros ocorridos no IoT hub e estão relacionadas com a funcionalidade de carregamento do ficheiro. Esta categoria inclui:

* Erros que ocorrem ao URI de SAS, por exemplo, quando este expirar antes de um dispositivo notifica o hub de um carregamento concluído.
* Não foi possível carregamentos comunicados pelo dispositivo.
* Erros que ocorrem quando um ficheiro não foi encontrado no armazenamento durante a criação de mensagem de notificação do IoT Hub.

Esta categoria não pode detetar erros que ocorram diretamente enquanto o dispositivo está a carregar um ficheiro para o armazenamento.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Encaminhamento de mensagens

A categoria de roteamento de mensagem rastreia erros que ocorrem durante a avaliação de rota de mensagem e o estado de funcionamento do ponto de extremidade como percebido pelo IoT Hub. Esta categoria inclui eventos, como quando uma regra avalia como "undefined", quando o IoT Hub marca um ponto de extremidade como morto e outros erros recebidos de um ponto de extremidade. Esta categoria não inclui erros específicos sobre as mensagens propriamente ditos (por exemplo, o dispositivo erros de limitação), que são apresentadas na categoria "telemetria do dispositivo".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>Ligar para o ponto final de monitorização

O ponto final de monitorização no seu hub IoT é um ponto de extremidade compatível com o Event Hub. Pode usar qualquer mecanismo que funciona com os Hubs de eventos para ler mensagens de monitorização a partir deste ponto final. O exemplo seguinte cria um leitor básico que não é adequado para uma implementação de alto débito. Para obter mais informações sobre como processar mensagens a partir do Event Hubs, veja o tutorial [Introdução ao Event Hubs][lnk-eventhubs-tutorial].

Para ligar ao ponto final de monitorização, terá de uma cadeia de ligação e o nome do ponto final. Os passos seguintes mostram como encontrar os valores necessários no portal do:

1. No portal, navegue para o painel de recursos do IoT Hub.

1. Escolher **monitorização de operações**e anote o **nome compatível com o Event Hub** e **endpoint compatível com o Event Hub** valores:

    ![Valores de ponto final compatível com o Hub de eventos][img-endpoints]

1. Escolher **políticas de acesso partilhado**, em seguida, escolha **serviço**. Anote o **chave primária** valor:

    ![Chave de principal de política de acesso partilhado de serviço][img-service-key]

O seguinte exemplo de código do c# está a ser utilizado a partir de um Visual Studio **ambiente de trabalho clássico do Windows** aplicação de consola c#. O projeto tem o **windowsazure. Servicebus** pacote NuGet instalado.

* Substitua o marcador de posição de cadeia de ligação uma cadeia de ligação que utiliza a **ponto final de compatível com o Event Hub** e o serviço **chave primária** valores que apontou anteriormente, conforme mostrado no exemplo a seguir:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Substitua o marcador de nome de ponto final monitorização com o **nome compatível com o Event Hub** valor que apontou anteriormente.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
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
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes
Para explorar ainda mais os recursos do IoT Hub, veja:

* [Guia do programador do IoT Hub][lnk-devguide]
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
