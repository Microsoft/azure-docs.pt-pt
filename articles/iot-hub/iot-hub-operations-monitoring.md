---
title: Monitorização de operações do Azure IoT Hub (depreciada) Microsoft Docs
description: Como utilizar o monitor de operações do Azure IoT Hub para monitorizar o estado das operações no seu hub IoT em tempo real.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 956a676709322860da7f08d032d370ed66f55b3f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92139329"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Monitorização das operações do IoT Hub (depreciada)

A monitorização das operações do IoT Hub permite-lhe monitorizar o estado das operações no seu hub IoT em tempo real. O IoT Hub acompanha eventos em várias categorias de operações. Pode optar pelo envio de eventos de uma ou mais categorias para um ponto final do seu hub IoT para processamento. Pode monitorizar os dados por erros ou configurar um processamento mais complexo com base em padrões de dados.

>[!NOTE]
>A monitorização das operações do IoT Hub **é depreciada e foi removida do IoT Hub em 10 de março de 2019**. Para monitorizar as operações e a saúde do IoT Hub, consulte [monitorar a saúde do Azure IoT Hub e diagnosticar rapidamente os problemas](iot-hub-monitor-resource-health.md). Para obter mais informações sobre a linha temporal de depreciação, consulte [monitorar as suas soluções Azure IoT com o Azure Monitor e a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

O IoT Hub monitoriza seis categorias de eventos:

* Operações de identidade do dispositivo
* Telemetria do dispositivo
* Mensagens nuvem-para-dispositivo
* Ligações
* Uploads de ficheiros
* Encaminhamento de mensagens

> [!IMPORTANT]
> A monitorização das operações do IoT Hub não garante a entrega fiável ou ordenada de eventos. Dependendo da infraestrutura subjacente ao IoT Hub, alguns eventos podem ser perdidos ou entregues fora de encomenda. Utilize a monitorização de operações para gerar alertas baseados em sinais de erro, tais como tentativas de ligação falhadas ou desconexões de alta frequência para dispositivos específicos. Não deve contar com eventos de monitorização de operações para criar uma loja consistente para o estado do dispositivo, por exemplo, um rastreio de loja ligado ou desligado do estado de um dispositivo. 

## <a name="how-to-enable-operations-monitoring"></a>Como permitir a monitorização das operações

1. Criar um hub IoT. Pode encontrar instruções sobre como criar um hub IoT no guia [Get Started.](quickstart-send-telemetry-dotnet.md)

2. Abra a lâmina do seu hub IoT. A partir daí, clique em **Monitorização de Operações.**

    ![Configuração de monitorização de operações de acesso no portal](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Selecione as categorias de monitorização que deseja monitorizar e, em seguida, clique em **Guardar**. Os eventos estão disponíveis para leitura a partir do ponto final compatível com o Event Hub listado nas **definições de monitorização.** O ponto final do IoT Hub chama-se `messages/operationsmonitoringevents` .

    ![Configure a monitorização das operações no seu hub IoT](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> A seleção da monitorização **verbose** para a categoria **Connections** faz com que o Hub IoT gere mensagens de diagnóstico adicionais. Para todas as outras categorias, a definição **verbose** altera a quantidade de informação que o IoT Hub inclui em cada mensagem de erro.

## <a name="event-categories-and-how-to-use-them"></a>Categorias de eventos e como usá-los

Cada categoria de monitorização de operações acompanha um tipo diferente de interação com o IoT Hub, e cada categoria de monitorização tem um esquema que define como os eventos nessa categoria são estruturados.

### <a name="device-identity-operations"></a>Operações de identidade do dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando tenta criar, atualizar ou apagar uma entrada no registo de identidade do seu hub IoT. Rastrear esta categoria é útil para cenários de provisionamento.

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

A categoria de telemetria do dispositivo rastreia erros que ocorrem no hub IoT e estão relacionados com o gasoduto de telemetria. Esta categoria inclui erros que ocorrem ao enviar eventos de telemetria (como estrangulamento) e receber eventos de telemetria (como leitor não autorizado). Esta categoria não pode apanhar erros causados pelo código em funcionamento no próprio dispositivo.

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

### <a name="cloud-to-device-commands"></a>Comandos nuvem-a-dispositivo

A categoria de comandos cloud-to-device rastreia erros que ocorrem no hub IoT e estão relacionados com o pipeline de mensagens nuvem-para-dispositivo. Esta categoria inclui erros que ocorrem quando ocorrem mensagens nuvem-para-dispositivo (como remetente não autorizado), receber mensagens nuvem-a-dispositivo (como a contagem de entregas excedida) e receber feedback de mensagens nuvem-a-dispositivo (como o feedback expirado). Esta categoria não apanha erros de um dispositivo que manuseie indevidamente uma mensagem nuvem-dispositivo se a mensagem nuvem-dispositivo tiver sido entregue com sucesso.

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

A categoria de ligações rastreia erros que ocorrem quando os dispositivos se ligam ou desligam de um hub IoT. Rastrear esta categoria é útil para identificar tentativas de ligação não autorizadas e para rastrear quando uma ligação é perdida para dispositivos em áreas de fraca conectividade.

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

### <a name="file-uploads"></a>Uploads de ficheiros

A categoria de upload de ficheiros rastreia os erros que ocorrem no hub IoT e estão relacionados com a funcionalidade de upload de ficheiros. Esta categoria inclui:

* Erros que ocorrem com o SAS URI, como quando expira antes de um dispositivo notificar o centro de um upload concluído.

* Uploads falhados reportados pelo dispositivo.

* Erros que ocorrem quando um ficheiro não é encontrado no armazenamento durante a criação de mensagens de notificação IoT Hub.

Esta categoria não pode apanhar erros que ocorrem diretamente enquanto o dispositivo está a carregar um ficheiro para armazenamento.

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

A categoria de encaminhamento de mensagens rastreia erros que ocorrem durante a avaliação da rota da mensagem e saúde do ponto final como percebido pelo IoT Hub. Esta categoria inclui eventos como quando uma regra avalia para "indefinido", quando o IoT Hub marca um ponto final como morto, e quaisquer outros erros recebidos de um ponto final. Esta categoria não inclui erros específicos sobre as próprias mensagens (tais como erros de estrangulamento do dispositivo), que são relatados na categoria "telemetria do dispositivo".

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

## <a name="connect-to-the-monitoring-endpoint"></a>Ligar ao ponto final de monitorização

O ponto final de monitorização no seu hub IoT é um ponto final compatível com o Event Hub. Pode utilizar qualquer mecanismo que funcione com os Centros de Eventos para ler mensagens de monitorização a partir deste ponto final. A amostra a seguir cria um leitor básico que não é adequado para uma implantação de alta produção. Para obter mais informações sobre como processar mensagens a partir do Event Hubs, veja o tutorial [Introdução ao Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Para ligar ao ponto final de monitorização, precisa de uma cadeia de ligação e do nome do ponto final. Os seguintes passos mostram-lhe como encontrar os valores necessários no portal:

1. No portal, navegue para a sua lâmina de recurso IoT Hub.

2. Escolha **a monitorização**de operações e tome nota do **nome compatível com** o Event Hub e dos valores **do ponto final compatíveis com o Event Hub:**

    ![Valores de ponto final compatíveis com o Hub de Evento](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Escolha **políticas de acesso compartilhado**e escolha o **serviço.** Tome nota do valor da **chave primária:**

    ![Chave primária da política de acesso partilhado do serviço](./media/iot-hub-operations-monitoring/service-key.png)

A seguinte amostra de código C# é retirada de uma aplicação de consola Visual Studio **Windows Classic Desktop** C#. O projeto tem o pacote **WindowsAzure.ServiceBus** NuGet instalado.

* Substitua o espaço reservado de ligação por uma cadeia de ligação que utiliza os valores de **chave primário** compatíveis com o **Centro de Eventos** e de serviço que observou anteriormente como mostrado no exemplo seguinte:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Substitua o espaço reservado para o ponto final de monitorização pelo valor **de nome compatível com o Event Hub** que anotado anteriormente.

```csharp
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

## <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/quickstart-linux.md)