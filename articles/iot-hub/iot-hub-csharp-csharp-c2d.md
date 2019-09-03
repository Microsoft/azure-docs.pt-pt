---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (.NET) | Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um hub IoT do Azure usando os SDKs do IoT do Azure para .NET. Você modifica um aplicativo de dispositivo para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviar as mensagens da nuvem para o dispositivo.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 99acd43128bedcf3dba470f84c0a406861d77e2d
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147782"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Enviar mensagens da nuvem para seu dispositivo com o Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a habilitar comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. O guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md) mostra como criar um hub IOT, provisionar uma identidade de dispositivo nele e codificar um aplicativo de dispositivo que envia mensagens do dispositivo para a nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia no [envio de telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md). Ele mostra como realizar as seguintes tarefas:

* De seu back-end da solução, envie mensagens da nuvem para o dispositivo para um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

* Do seu back-end da solução, solicite a confirmação de entrega (*comentários*) para mensagens enviadas a um dispositivo do Hub IOT.

Você pode encontrar mais informações sobre mensagens da nuvem para o dispositivo em [D2C e C2D Messaging com o Hub IOT](iot-hub-devguide-messaging.md).

No final deste tutorial, você executará dois aplicativos de console .NET.

* **SimulatedDevice**. Esse aplicativo se conecta ao Hub IoT e recebe mensagens da nuvem para o dispositivo. Este aplicativo é uma versão modificada do aplicativo criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Esse aplicativo envia uma mensagem da nuvem para o dispositivo para o aplicativo do dispositivo por meio do Hub IoT e recebe sua confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte do SDK para muitas plataformas de dispositivo e linguagens, incluindo C, Java, Python e JavaScript, por meio de [SDKs do dispositivo IOT do Azure](iot-hub-devguide-sdks.md). Para obter instruções detalhadas sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, consulte o guia do [desenvolvedor do Hub IOT](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="receive-messages-in-the-device-app"></a>Receber mensagens no aplicativo do dispositivo

Nesta seção, modifique o aplicativo do dispositivo criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md) para receber mensagens da nuvem para o dispositivo do Hub IOT.

1. No Visual Studio, no projeto **SimulatedDevice** , adicione o seguinte método à classe **programa** .

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

1. Adicione o seguinte método ao método **Main** , logo antes da `Console.ReadLine()` linha:

   ```csharp
   ReceiveC2dAsync();
   ```

O `ReceiveAsync` método retorna de forma assíncrona a mensagem recebida no momento em que é recebida pelo dispositivo. Retorna *NULL* após um período de tempo limite Especificável. Neste exemplo, o padrão de um minuto é usado. Quando o aplicativo recebe um *valor nulo*, ele deve continuar aguardando novas mensagens. Esse requisito é o motivo `if (receivedMessage == null) continue` da linha.

A chamada para `CompleteAsync()` notifica o Hub IOT de que a mensagem foi processada com êxito. A mensagem pode ser removida com segurança da fila do dispositivo. Se algo aconteceu que impediu o aplicativo do dispositivo de concluir o processamento da mensagem, o Hub IoT a entrega novamente. A lógica de processamento de mensagens no aplicativo do dispositivo deve ser *idempotente*, de modo que o recebimento da mesma mensagem várias vezes produz o mesmo resultado.

Um aplicativo também pode abandonar temporariamente uma mensagem, o que faz com que o Hub IoT retenha a mensagem na fila para consumo futuro. Ou o aplicativo pode rejeitar uma mensagem, o que remove permanentemente a mensagem da fila. Para obter mais informações sobre o ciclo de vida da mensagem da nuvem para o dispositivo, consulte [D2C e C2D Messaging with IOT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Ao usar HTTPS em vez de MQTT ou AMQP como um transporte, `ReceiveAsync` o método retorna imediatamente. O padrão com suporte para mensagens da nuvem para o dispositivo com HTTPS são dispositivos conectados intermitentemente que verificam mensagens com pouca frequência (menos do que a cada 25 minutos). Emitir mais HTTPS recebe resultados na limitação do Hub IoT às solicitações. Para obter mais informações sobre as diferenças entre o suporte a MQTT, AMQP e HTTPS e a limitação do Hub IoT, consulte [mensagens D2C e C2D com o Hub IOT](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens da nuvem para o dispositivo por meio do Hub IoT criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md). Para enviar mensagens da nuvem para o dispositivo, seu serviço precisa da permissão de **conexão de serviço** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem da nuvem para o dispositivo

Agora você escreve um aplicativo de console .NET que envia mensagens da nuvem para o dispositivo para o aplicativo do dispositivo.

1. Na solução atual do Visual Studio, selecione **arquivo** > **novo** > **projeto**. Em **criar um novo projeto**, selecione **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Nomeie o projeto *SendCloudToDevice*. Em **solução**, selecione **Adicionar à solução** e aceite a versão mais recente do .NET Framework. Selecione **Create** (Criar) para criar o projeto.

   ![Configurar um novo projeto no Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. No Gerenciador de Soluções, clique com o botão direito do mouse na nova solução e selecione **gerenciar pacotes NuGet**.

1. Em **gerenciar pacotes NuGet**, selecione **procurar**e, em seguida, procure e selecione **Microsoft. Azure.** Devices. Selecione **instalar**.

   Esta etapa baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do serviço IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Adicione a seguinte `using` instrução na parte superior do arquivo **Program.cs** .

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Adicione o seguinte método à classe **Programa**. Defina o nome do dispositivo para o que você usou ao definir o dispositivo em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Esse método envia uma nova mensagem da nuvem para o dispositivo para o dispositivo com a ID, `myFirstDevice`. Altere esse parâmetro somente se você o tiver modificado a partir daquele usado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md).

1. Por fim, adicione as linhas a seguir ao método **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. No Gerenciador de soluções, clique com o botão direito do mouse em sua solução e selecione **definir projetos de inicialização**.

1. Em **Propriedades** > comuns**projeto de inicialização**, selecione **vários projetos de inicialização**e, em seguida, selecione a ação **Iniciar** para **ReadDeviceToCloudMessages**, **SimulatedDevice**e **SendCloudToDevice** . Selecione **OK** para guardar as alterações.

1. Pressione **F5**. Todos os três aplicativos devem começar. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você deve ver a mensagem sendo recebida pelo aplicativo do dispositivo.

   ![Mensagem de recebimento de aplicativo](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Receber comentários de entrega

É possível solicitar confirmações de entrega (ou expiração) do Hub IoT para cada mensagem da nuvem para o dispositivo. Essa opção permite que o back-end da solução informe a lógica de repetição ou de compensação facilmente. Para obter mais informações sobre os comentários da nuvem para o dispositivo, consulte [D2C e C2D Messaging with IOT Hub](iot-hub-devguide-messaging.md).

Nesta seção, você modifica o aplicativo **SendCloudToDevice** para solicitar comentários e recebê-lo do Hub IOT.

1. No Visual Studio, no projeto **SendCloudToDevice** , adicione o seguinte método à classe **programa** .

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

    Observe que esse padrão de recebimento é o mesmo usado para receber mensagens da nuvem para o dispositivo do aplicativo do dispositivo.

1. Adicione a seguinte linha ao método **Main** , logo após `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Para solicitar comentários para a entrega da sua mensagem da nuvem para o dispositivo, você precisa especificar uma propriedade no método **SendCloudToDeviceMessageAsync** . Adicione a linha a seguir, logo após `var commandMessage = new Message(...);` a linha.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Execute os aplicativos pressionando **F5**. Você deve ver todos os três aplicativos iniciarem. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você deve ver a mensagem sendo recebida pelo aplicativo do dispositivo e, depois de alguns segundos, a mensagem de comentários sendo recebida pelo aplicativo **SendCloudToDevice** .

   ![Mensagem de recebimento de aplicativo](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como retirada exponencial, conforme sugerido em [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Passos Seguintes

Neste "como", você aprendeu como enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas de ponta a ponta que usam o Hub IoT, consulte [acelerador de solução de monitoramento remoto de IOT do Azure](https://docs.microsoft.com/azure/iot-suite/).

Para saber mais sobre como desenvolver soluções com o Hub IoT, confira o [Guia do desenvolvedor do Hub IOT](iot-hub-devguide.md).
