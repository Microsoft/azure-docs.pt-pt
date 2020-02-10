---
title: Upload de ficheiros de dispositivos para Azure IoT Hub com .NET  Microsoft Docs
description: Como fazer o upload de ficheiros de um dispositivo para a nuvem utilizando o dispositivo Azure IoT SDK para .NET. Os ficheiros carregados são armazenados num recipiente de armazenamento Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: b379f158672a9df3056acb09c63c392869a53283
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108709"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se no código no [Envio de mensagens cloud-to-device com tutorial IoT Hub](iot-hub-csharp-csharp-c2d.md) para lhe mostrar como usar as capacidades de upload de ficheiros do IoT Hub. Mostra como:

* Forneça de forma segura um dispositivo com um blob URI Azure para o upload de um ficheiro.

* Utilize as notificações de upload de ficheiros IoT Hub para desencadear o processamento do ficheiro na parte de trás da sua aplicação.

A [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md) quickstart e [enviar mensagens cloud-to-device com tutorial IoT Hub](iot-hub-csharp-csharp-c2d.md) mostram a funcionalidade básica de mensagens de dispositivo-nuvem e nuvem-a-dispositivo do IoT Hub. O [Configure Message Routing com ioT Hub](tutorial-routing.md) tutorial descreve uma forma de armazenar de forma fiável mensagens dispositivo-to-cloud no armazenamento do Microsoft Azure Blob. No entanto, em alguns cenários não é possível mapear facilmente os dados que os seus dispositivos enviam para as mensagens relativamente pequenas de dispositivo-nuvem que o IoT Hub aceita. Por exemplo:

* Ficheiros grandes que contêm imagens

* Vídeos

* Dados de vibração recolhidos em alta frequência

* Alguma forma de dados pré-processados

Estes ficheiros são normalmente processados na nuvem utilizando ferramentas como a [Azure Data Factory](../data-factory/introduction.md) ou a stack [Hadoop.](../hdinsight/index.yml) Quando precisa de fazer upload de ficheiros a partir de um dispositivo, ainda pode utilizar a segurança e a fiabilidade do IoT Hub.

No final deste tutorial executa duas aplicações de consola .NET:

* **Dispositivo simulado**. Esta aplicação envia um ficheiro para armazenamento utilizando um SAS URI fornecido pelo seu hub IoT. É uma versão modificada da aplicação criada no [Envio de mensagens cloud-to-device com tutorial IoT Hub.](iot-hub-csharp-csharp-c2d.md)

* **Notificação de Upload de Leitura**. Esta aplicação recebe notificações de upload de ficheiros do seu hub IoT.

> [!NOTE]
> O IoT Hub suporta muitas plataformas e idiomas de dispositivos, incluindo C, Java, Python e Javascript, através de SDKs do dispositivo Azure IoT. Consulte o Centro de [Desenvolvimento Azure IoT](https://azure.microsoft.com/develop/iot) para obter instruções passo a passo sobre como ligar o seu dispositivo ao Hub Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Faça upload de um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, modifica a aplicação do dispositivo que criou no [Enviar mensagens cloud-to-device com ioT Hub](iot-hub-csharp-csharp-c2d.md) para receber mensagens cloud-to-device do hub IoT.

1. No Visual Studio Solution Explorer, clique no projeto **SimulatedDevice** e selecione **Adicionar** > **Item Existente**. Encontre um ficheiro de imagem e inclua-o no seu projeto. Este tutorial assume que a imagem se chama `image.jpg`.

1. Clique na imagem para a direita e, em seguida, selecione **Propriedades**. Certifique-se de que **o Diretório copiar para saída** está definido para copiar **sempre**.

    ![Mostrar onde atualizar a propriedade de imagem para Copy to Output Directy](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. No ficheiro **Program.cs,** adicione as seguintes declarações na parte superior do ficheiro:

    ```csharp
    using System.IO;
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    O método `UploadToBlobAsync` leva o nome do ficheiro e a fonte de fluxo do ficheiro a ser carregado e manuseia o upload para armazenamento. A aplicação de consola apresenta o tempo que leva para carregar o ficheiro.

1. Adicione a seguinte linha no método **Principal,** mesmo antes `Console.ReadLine()`:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Por uma questão de simplicidade, este tutorial não implementa qualquer política de retenção. No código de produção, deve implementar políticas de retry, tais como backoff exponencial, como sugerido no manuseamento de [falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, cria um serviço back-end para receber mensagens de notificação de envio de ficheiros do hub IoT que criou no [Envio de telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md). Para receber mensagens de notificação de upload de ficheiros, o seu serviço necessita da permissão de ligação do **serviço.** Por padrão, cada Hub IoT é criado com uma política de acesso partilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receba uma notificação de upload de ficheiro

Nesta secção, escreve uma aplicação de consola .NET que recebe mensagens de notificação de upload de ficheiros do IoT Hub.

1. Na atual solução Visual Studio, selecione **File** > **New** > **Project**. Em **Criar um novo projeto,** selecione App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Nomeie o projeto *ReadFileUploadNotification*. Em **solução,** **selecione Adicionar à solução**. Selecione **Create** (Criar) para criar o projeto.

    ![Configure o projeto ReadFileUploadNotification no Estúdio Visual](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. No Solution Explorer, clique no direito do projeto **ReadFileUploadNotification** e selecione **Gerir pacotes NuGet**.

1. No **NuGet Package Manager,** selecione **Browse**. Procure e selecione **Microsoft.Azure.Devices**, e, em seguida, **selecione Instalar**.

    Este passo descarrega, instala e adiciona uma referência ao [pacote SDK NuGet do serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) no projeto **ReadFileUploadNotification.**

1. No ficheiro **Program.cs** para este projeto, adicione a seguinte declaração na parte superior do ficheiro:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado `{iot hub connection string}` pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT:](#get-the-iot-hub-connection-string)

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Note que este padrão de receção é o mesmo usado para receber mensagens cloud-to-device da aplicação do dispositivo.

1. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

1. No Solutions Explorer, clique à direita na sua solução e selecione **Definir Projetos StartUp**.

1. Em **Propriedades Comuns** > **Projeto de Arranque,** selecione **Vários projetos**de arranque, e, em seguida, selecione a ação **Iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**. Selecione **OK** para guardar as suas alterações.

1. Prima **F5**. Ambas as candidaturas devem começar. Deve ver o upload concluído numa aplicação de consola e a mensagem de notificação de upload recebida pela outra aplicação da consola. Pode utilizar o [portal Azure](https://portal.azure.com/) ou o Visual Studio Server Explorer para verificar a presença do ficheiro carregado na sua conta De armazenamento Azure.

    ![Screenshot mostrando o ecrã de saída](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar as capacidades de upload de ficheiros do IoT Hub para simplificar os uploads de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades e cenários do IoT Hub com os seguintes artigos:

* [Criar um hub IoT programática](iot-hub-rm-template-powershell.md)

* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementação de IA para dispositivos de borda com Borda Azure IoT](../iot-edge/tutorial-simulate-device-linux.md)
