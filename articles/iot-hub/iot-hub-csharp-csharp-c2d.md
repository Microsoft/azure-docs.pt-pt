---
title: Mensagens cloud-to-device com Azure IoT Hub (.NET) [ Microsoft Docs
description: Como enviar mensagens cloud-to-device para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para .NET. Modifica uma aplicação de dispositivo para receber mensagens cloud-to-device e modificar uma aplicação de back-end para enviar as mensagens cloud-to-device.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110212"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Envie mensagens da nuvem para o seu dispositivo com IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. A [telemetria Enviar de um dispositivo para um quickstart de hub IoT](quickstart-send-telemetry-dotnet.md) mostra como criar um hub IoT, fornecer uma identidade de dispositivo no mesmo, e codificar uma aplicação de dispositivo que envia mensagens dispositivo-para-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se em [Enviar a telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md). Mostra como fazer as seguintes tarefas:

* A partir da sua solução traseira, envie mensagens cloud-to-device para um único dispositivo através do IoT Hub.

* Receba mensagens cloud-to-device num dispositivo.

* A partir da sua solução na parte de trás, solicite reconhecimento de entrega *(feedback)* para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens cloud-to-device em [Mensagens D2C e C2D com IoT Hub](iot-hub-devguide-messaging.md).

No final deste tutorial, executa duas aplicações de consola .NET.

* **Dispositivo simulado**. Esta aplicação conecta-se ao seu hub IoT e recebe mensagens cloud-to-device. Esta aplicação é uma versão modificada da aplicação criada na [Send telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md).

* **Enviar Cloudtodevice**. Esta aplicação envia uma mensagem cloud-to-device para a aplicação do dispositivo através do IoT Hub, e recebe o seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte SDK para muitas plataformas e idiomas de dispositivos, incluindo C, Java, Python e Javascript, através de [SDKs do dispositivo Azure IoT.](iot-hub-devguide-sdks.md) Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código deste tutorial e, em geral, ao Azure IoT Hub, consulte o guia de [desenvolvimento do IoT Hub](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Receber mensagens na aplicação do dispositivo

Nesta secção, modifique a aplicação do dispositivo que criou no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md) para receber mensagens cloud-to-device do hub IoT.

1. No Estúdio Visual, no projeto **SimulatedDevice,** adicione o seguinte método à classe **Programa.**

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Adicione o seguinte método no método `Console.ReadLine()` **Principal,** mesmo antes da linha:

   ```csharp
   ReceiveC2dAsync();
   ```

O `ReceiveAsync` método devolve assincronicamente a mensagem recebida no momento em que é recebida pelo dispositivo. Devolve *nulo* após um período de tempo de paragem especificável. Neste exemplo, é utilizado o padrão de um minuto. Quando a aplicação receber um *nulo,* deve continuar a aguardar novas mensagens. Este requisito é a `if (receivedMessage == null) continue` razão da linha.

O apelo para `CompleteAsync()` que ioT Hub notifique que a mensagem foi processada com sucesso. A mensagem pode ser removida com segurança da fila do dispositivo. Se algo aconteceu que impedisse a aplicação do dispositivo de completar o processamento da mensagem, o IoT Hub entrega-a novamente. A lógica de processamento de mensagens na aplicação do dispositivo deve ser *idempotente,* para que receber a mesma mensagem várias vezes produza o mesmo resultado.

Uma aplicação também pode abandonar temporariamente uma mensagem, o que resulta em ioT hub mantendo a mensagem na fila para consumo futuro. Ou a aplicação pode rejeitar uma mensagem, que remove permanentemente a mensagem da fila. Para obter mais informações sobre o ciclo de vida da mensagem cloud-to-device, consulte [mensagens D2C e C2D com IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Ao utilizar HTTPS em vez de MQTT ou `ReceiveAsync` AMQP como transporte, o método regressa imediatamente. O padrão suportado para mensagens cloud-to-device com HTTPS é intermitentemente conectado dispositivos que verificam mensagens com pouca frequência (menos de 25 minutos). A emissão de mais HTTPS recebe resultados no IoT Hub que acelera os pedidos. Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTPS, e a aceleração do IoT Hub, consulte [as mensagens D2C e C2D com o IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

Neste artigo, cria um serviço back-end para enviar mensagens cloud-to-device através do hub IoT que criou no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md). Para enviar mensagens cloud-to-device, o seu serviço necessita da permissão de ligação do **serviço.** Por padrão, cada Hub IoT é criado com uma política de acesso partilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem cloud-to-device

Agora escreve uma aplicação de consola .NET que envia mensagens cloud-to-device para a aplicação do dispositivo.

1. Na atual solução Visual Studio, selecione **File** > **New** > **Project**. Em **Criar um novo projeto,** selecione App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Nomeie o projeto *SendCloudToDevice*. Em **Solução**, **selecione Adicionar à solução** e aceite a versão mais recente do .NET Framework. Selecione **Create** (Criar) para criar o projeto.

   ![Configure um novo projeto no Estúdio Visual](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. No Solution Explorer, clique na nova solução e, em seguida, selecione **Gerir pacotes NuGet**.

1. Em **Gerir pacotes NuGet,** selecione **Browse**, e, em seguida, procure e selecione **Microsoft.Azure.Devices**. **Selecione Instalar**.

   Este passo descarrega, instala e adiciona uma referência ao [pacote SDK NuGet do serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Adicione a `using` seguinte declaração no topo do ficheiro **Program.cs.**

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Adicione o seguinte método à classe **Programa**. Defina o nome do dispositivo para o que usou ao definir o dispositivo em [Enviar a telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Este método envia uma nova mensagem cloud-to-device `myFirstDevice`para o dispositivo com o ID, . Mude este parâmetro apenas se o modificar do utilizado no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md).

1. Por fim, adicione as seguintes linhas ao método **Principal.**

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. No Solutions Explorer, clique à direita na sua solução e selecione **Definir Projetos StartUp**.

1. No Common **Properties** > **Startup Project**, selecione **Vários projetos**de arranque, então selecione a ação **iniciar** para **ReadDeviceToCloudMessages,** **SimulatedDevice**e **SendCloudToDevice**. Selecione **OK** para guardar as alterações.

1. Prima **F5**. As três candidaturas devem começar. Selecione as janelas **SendCloudToDevice** e prima **Enter**. Deve ver a mensagem a ser recebida pela aplicação do dispositivo.

   ![Mensagem de receção de aplicativos](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Receber feedback de entrega

É possível solicitar reconhecimentos de entrega (ou expiração) do IoT Hub para cada mensagem cloud-to-device. Esta opção permite que a solução volte a informar facilmente a lógica de retry ou compensação. Para obter mais informações sobre o feedback cloud-to-device, consulte [Mensagens D2C e C2D com IoT Hub](iot-hub-devguide-messaging.md).

Nesta secção, modifica a aplicação **SendCloudToDevice** para solicitar feedback e recebe-a do hub IoT.

1. No Estúdio Visual, no projeto **SendCloudToDevice,** adicione o seguinte método à classe **Programa.**

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Note que este padrão de receção é o mesmo usado para receber mensagens cloud-to-device da aplicação do dispositivo.

1. Adicione a seguinte linha no método `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` **Principal,** logo a seguir .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Para solicitar feedback para a entrega da sua mensagem cloud-to-device, tem de especificar uma propriedade no método **SendCloudToDeviceMessageAsync.** Adicione a seguinte linha, `var commandMessage = new Message(...);` logo após a linha.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Executar as aplicações premindo **F5**. Devia ver as três candidaturas a começar. Selecione as janelas **SendCloudToDevice** e prima **Enter**. Deve ver a mensagem a ser recebida pela aplicação do dispositivo e, passados alguns segundos, a mensagem de feedback a ser recebida pela sua aplicação **SendCloudToDevice.**

   ![Mensagem de receção de aplicativos](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Para a simplicidade, este tutorial não implementa qualquer política de retenção. No código de produção, deve implementar políticas de retry, tais como backoff exponencial, como sugerido no manuseamento de [falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Passos seguintes

Neste como-fazer, aprendeu a enviar e a receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte o acelerador de solução de [monitorização remota Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de [desenvolvimento do IoT Hub.](iot-hub-devguide.md)
