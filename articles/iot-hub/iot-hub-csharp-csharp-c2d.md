---
title: Mensagens nuvem-para-dispositivo com Azure IoT Hub (.NET) Microsoft Docs
description: Como enviar mensagens nuvem-para-dispositivo para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para .NET. Modifica uma aplicação de dispositivo para receber mensagens cloud-to-device e modificar uma aplicação de back-end para enviar as mensagens cloud-to-device.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: d8df9884c0104792240d85d9ebd4235ef2a18741
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142352"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Envie mensagens da nuvem para o seu dispositivo com IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações biducionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. O [telemetria Enviar de um dispositivo para um quickstart do hub IoT](quickstart-send-telemetry-dotnet.md) mostra como criar um hub IoT, providenciar uma identidade do dispositivo no mesmo, e codificar uma aplicação de dispositivo que envia mensagens dispositivo-a-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se no [Envio de telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md). Mostra como fazer as seguintes tarefas:

* A partir da parte de trás da sua solução, envie mensagens nuvem-dispositivo para um único dispositivo através do IoT Hub.

* Receba mensagens nuvem-dispositivo num dispositivo.

* A partir da sua solução back end, solicite o reconhecimento de entrega *(feedback)* para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens nuvem-a-dispositivo em [Mensagens D2C e C2D com IoT Hub](iot-hub-devguide-messaging.md).

No final deste tutorial, executam duas aplicações de consola .NET.

* **SimuladoDevice**. Esta aplicação conecta-se ao seu hub IoT e recebe mensagens nuvem-dispositivo. Esta aplicação é uma versão modificada da app criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Esta aplicação envia uma mensagem nuvem-para-dispositivo para a aplicação do dispositivo através do IoT Hub, e depois recebe o seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte SDK para muitas plataformas e idiomas de dispositivos, incluindo C, Java, Python e Javascript, através [de SDKs de dispositivo Azure IoT.](iot-hub-devguide-sdks.md) Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código deste tutorial e, em geral, ao Azure IoT Hub, consulte o guia de desenvolvimento do [IoT Hub](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Receber mensagens na aplicação do dispositivo

Nesta secção, modifique a aplicação do dispositivo que criou em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md) para receber mensagens nuvem-dispositivo a partir do hub IoT.

1. No Estúdio Visual, no projeto **SimulatedDevice,** adicione o seguinte método à classe **SimulatedDevice.**

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

1. Adicione o seguinte método no método **principal,** imediatamente antes da `Console.ReadLine()` linha:

   ```csharp
   ReceiveC2dAsync();
   ```

O `ReceiveAsync` método retorna assincroticamente a mensagem recebida no momento em que é recebida pelo dispositivo. Retorna *nulo* após um período de tempo especificado. Neste exemplo, é utilizado o padrão de um minuto. Quando a aplicação recebe um *nulo,* deve continuar a aguardar novas mensagens. Esta exigência é a razão da `if (receivedMessage == null) continue` linha.

A chamada para `CompleteAsync()` notificar o IoT Hub de que a mensagem foi processada com sucesso e que a mensagem pode ser removida com segurança da fila do dispositivo. O dispositivo deve chamar este método quando o seu processamento estiver concluído com sucesso, independentemente do protocolo que está a utilizar.

Com AMQP e HTTPS, mas não MQTT, o dispositivo também pode:

* Abandone uma mensagem, o que resulta em IoT Hub manter a mensagem na fila do dispositivo para consumo futuro.
* Rejeite uma mensagem que remova permanentemente a mensagem da fila do dispositivo.

Se algo acontecer que impeça o dispositivo de completar, abandonar ou rejeitar a mensagem, o IoT Hub irá, após um período de tempo fixo, fazer fila para a entrega novamente. Por este motivo, a lógica de processamento de mensagens na aplicação do dispositivo deve ser *idempotente*– de modo a que receber a mesma mensagem várias vezes produza o mesmo resultado.

Para obter informações mais detalhadas sobre como o IoT Hub processa mensagens nuvem-dispositivo, incluindo detalhes do ciclo de vida da mensagem nuvem-para-dispositivo, consulte [Enviar mensagens cloud-to-device a partir de um hub IoT](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Ao utilizar HTTPS em vez de MQTT ou AMQP como transporte, o `ReceiveAsync` método retorna imediatamente. O padrão suportado para mensagens nuvem-a-dispositivo com HTTPS é dispositivos intermitentemente ligados que verificam mensagens com pouca frequência (um mínimo de cada 25 minutos). A emissão de mais HTTPS recebe resultados no IoT Hub a estrangular os pedidos. Para obter mais informações sobre as diferenças entre suporte MQTT, AMQP e HTTPS, consulte [a orientação de comunicações Cloud-to-device](iot-hub-devguide-c2d-guidance.md) e [Escolha um protocolo de comunicação](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

Neste artigo, cria um serviço back-end para enviar mensagens cloud-to-device através do hub IoT que criou [enviar para um hub IoT](quickstart-send-telemetry-dotnet.md). Para enviar mensagens nuvem-para-dispositivo, o seu serviço necessita da permissão **de ligação de serviço.** Por padrão, cada IoT Hub é criado com uma política de acesso compartilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem nuvem-a-dispositivo

Nesta secção, cria-se uma aplicação de consola .NET que envia mensagens nuvem-para-dispositivo para a aplicação do dispositivo simulado.

1. Na atual solução Visual Studio, selecione **File**  >  **New**  >  **Project**. In **Create a new project**, selecione Console App **(.NET Framework)** e, em seguida, selecione **Next**.

1. Nomeie o projeto *SendCloudToDevice*. Em **Solução**, **selecione Adicionar à solução** e aceitar a versão mais recente do Quadro .NET. Selecione **Create** (Criar) para criar o projeto.

   ![Configure um novo projeto no Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. No Solution Explorer, clique com o botão direito no novo projeto e, em seguida, **selecione Gerir Pacotes NuGet**.

1. In **Manage NuGet Packages**, selecione **Procurar**e, em seguida, procurar e selecionar **Dispositivos Microsoft.Azure..** **Selecione instalar**.

   Este passo descarrega, instala e adiciona uma referência ao [pacote SDK NuGet do serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Adicione a seguinte `using` declaração no topo do ficheiro **Program.cs.**

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o `{iot hub connection string}` valor do espaço reservado pela cadeia de ligação do hub IoT que notou anteriormente na cadeia de [ligação do hub IoT](#get-the-iot-hub-connection-string). Substitua o `{device id}` valor do espaço reservado pelo ID do dispositivo que adicionou na [telemetria Enviar telemetria de um dispositivo para um arranque rápido do hub IoT.](quickstart-send-telemetry-dotnet.md)

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   static string targetDevice = "{device id}";
   ```

1. Adicione o seguinte método à classe **Programa** para enviar uma mensagem ao seu dispositivo.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync(targetDevice, commandMessage);
   }
   ```

1. Por último, adicione as seguintes linhas ao método **Principal.**

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. No Solutions Explorer, clique com o botão direito na sua solução e selecione **set StartUp Projects**.

1. No **Common Properties**Startup  >  **Project**, selecione **vários projetos de startups,** em seguida, selecione a ação **Iniciar** para **SimulatedDevice** e **SendCloudToDevice**. Selecione **OK** para guardar as alterações.

1. Prima **F5**. Ambas as aplicações devem começar. Selecione a janela **SendCloudToDevice** e prima **Enter**. Deverá ver a mensagem a ser recebida pela aplicação do dispositivo.

   ![Mensagem de receção de aplicativo de dispositivo](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Receber feedback de entrega

É possível solicitar reconhecimentos de entrega (ou expiração) do IoT Hub para cada mensagem nuvem-dispositivo. Esta opção permite que a solução recue para informar facilmente a lógica de retenção ou compensação. Para obter mais informações sobre o feedback nuvem-a-dispositivo, consulte [mensagens D2C e C2D com IoT Hub](iot-hub-devguide-messaging.md).

Nesta secção, modifica a aplicação **SendCloudToDevice** para solicitar feedback e recebe-a a partir do hub IoT.

1. No Visual Studio, no projeto **SendCloudToDevice,** adicione o seguinte método à classe **Programa.**

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

    Note que este padrão de receção é o mesmo usado para receber mensagens nuvem-dispositivo a partir da aplicação do dispositivo.

1. Adicione a seguinte linha no método **principal,** logo a seguir `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Para solicitar feedback para a entrega da sua mensagem nuvem-para-dispositivo, tem de especificar uma propriedade no método **SendCloudToDeviceMessageAsync.** Adicione a seguinte linha, logo a seguir à `var commandMessage = new Message(...);` linha.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Executar as aplicações pressionando **F5**. Devia ver ambas as aplicações a começar. Selecione a janela **SendCloudToDevice** e prima **Enter**. Deverá ver a mensagem a ser recebida pela aplicação do dispositivo e, passados alguns segundos, a mensagem de feedback recebida pela sua aplicação **SendCloudToDevice.**

   ![Aplicação de dispositivo que recebe mensagem e app de serviço recebendo feedback](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de retenção. No código de produção, deve implementar políticas de repetição, tais como o backoff exponencial, tal como sugerido no [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Passos seguintes

Neste modo de como, aprendeu a enviar e a receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte o [acelerador de solução de monitorização remota Azure IoT](/azure/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvimento do [IoT Hub.](iot-hub-devguide.md)