---
title: Faça upload de ficheiros de dispositivos para Azure IoT Hub com .NET / Microsoft Docs
description: Como carregar ficheiros de um dispositivo para a nuvem usando o dispositivo Azure IoT SDK para .NET. Os ficheiros carregados são armazenados num recipiente de bolhas de armazenamento Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 8d45ad630d09a4909cf00b830df139057cc0fcaf
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142296"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se no código nas [mensagens Enviar mensagens nuvem-para-dispositivo com tutorial IoT Hub](iot-hub-csharp-csharp-c2d.md) para lhe mostrar como utilizar as capacidades de upload de ficheiros do IoT Hub. Mostra como:

* Forneça de forma segura um dispositivo com um URI blob Azure para o upload de um ficheiro.

* Utilize as notificações de upload de ficheiros IoT Hub para desencadear o processamento do ficheiro na parte de trás da sua aplicação.

O [Envio de telemetria de um dispositivo para um quickstart do hub IoT](quickstart-send-telemetry-dotnet.md) e [enviar mensagens nuvem-para-dispositivo com tutorial IoT Hub](iot-hub-csharp-csharp-c2d.md) mostram a funcionalidade básica de mensagens de dispositivo para nuvem e nuvem-para-dispositivo do IoT Hub. O [Configure Message Encaminhing com tutorial IoT Hub](tutorial-routing.md) descreve uma forma de armazenar de forma fiável mensagens de dispositivo para nuvem no armazenamento do Microsoft Azure Blob. No entanto, em alguns cenários não é possível mapear facilmente os dados que os seus dispositivos enviam para as mensagens relativamente pequenas de dispositivo para nuvem que o IoT Hub aceita. Por exemplo:

* Grandes ficheiros que contêm imagens

* Vídeos

* Dados de vibração recolhidos em alta frequência

* Alguma forma de dados pré-processados

Estes ficheiros são normalmente processados na nuvem utilizando ferramentas como [a Azure Data Factory](../data-factory/introduction.md) ou a pilha [Hadoop.](../hdinsight/index.yml) Quando precisa de carregar ficheiros a partir de um dispositivo, ainda pode utilizar a segurança e a fiabilidade do IoT Hub.

No final deste tutorial executam duas aplicações de consola .NET:

* **SimuladoDevice**. Esta aplicação envia um ficheiro para armazenamento utilizando um SAS URI fornecido pelo seu hub IoT. É uma versão modificada da app criada nas [mensagens Enviar mensagens cloud-to-device com tutorial IoT Hub.](iot-hub-csharp-csharp-c2d.md)

* **ReadFileUploadNotification**. Esta aplicação recebe notificações de upload de ficheiros do seu hub IoT.

> [!NOTE]
> O IoT Hub suporta muitas plataformas e idiomas de dispositivos, incluindo C, Java, Python e Javascript, através de SDKs de dispositivoS Azure IoT. Consulte o [Centro de Desenvolvimento Azure IoT](https://azure.microsoft.com/develop/iot) para obter instruções passo a passo sobre como ligar o seu dispositivo ao Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Faça upload de um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, modifica a aplicação do dispositivo que criou no [Enviar mensagens nuvem-a-dispositivo com ioT Hub](iot-hub-csharp-csharp-c2d.md) para receber mensagens nuvem-dispositivo a partir do hub IoT.

1. No Visual Studio Solution Explorer, clique à direita no projeto **SimulatedDevice** e selecione **Add**  >  **Existing Item**. Encontre um ficheiro de imagem e inclua-o no seu projeto. Este tutorial pressupõe que a imagem está `image.jpg` nomeada.

1. Clique com o botão direito na imagem e, em seguida, selecione **Propriedades**. Certifique-se de que **o Copy to Output Directory** está sempre definido para **Copiar**.

    ![Mostrar onde atualizar a propriedade de imagem para Copy to Output Directory](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. No ficheiro **Program.cs,** adicione as seguintes declarações no topo do ficheiro:

    ```csharp
    using System.IO;
    ```

1. Adicione o seguinte método à classe **Programa:**

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

    O `UploadToBlobAsync` método requer o nome do ficheiro e a fonte de fluxo do ficheiro para ser carregado e manuseia o upload para armazenamento. A aplicação da consola exibe o tempo que demora a carregar o ficheiro.

1. Adicione a seguinte linha no método **principal,** imediatamente `Console.ReadLine()` antes:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Por uma questão de simplicidade, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar políticas de repetição, tais como o backoff exponencial, tal como sugerido no [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

Neste artigo, cria um serviço back-end para receber mensagens de notificação de upload de ficheiros do hub IoT que criou [em Enviar por email o artigo Telemetria para um hub IoT](quickstart-send-telemetry-dotnet.md). Para receber mensagens de notificação de upload de ficheiros, o seu serviço necessita da permissão **de ligação do serviço.** Por padrão, cada IoT Hub é criado com uma política de acesso compartilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de upload de ficheiros

Nesta secção, escreve uma aplicação de consola .NET que recebe mensagens de notificação de upload de ficheiros do IoT Hub.

1. Na atual solução Visual Studio, selecione **File**  >  **New**  >  **Project**. In **Create a new project**, selecione Console App **(.NET Framework)** e, em seguida, selecione **Next**.

1. Nomeie o projeto *ReadFileUploadNotification*. Em **Solução**, **selecione Adicionar à solução**. Selecione **Create** (Criar) para criar o projeto.

    ![Configure o projeto ReadFileUploadNotification em Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. No Solution Explorer, clique com o botão direito no projeto **ReadFileUploadNotification** e selecione **Gerir pacotes NuGet**.

1. In **NuGet Package Manager**, selecione **Procurar**. Procure e selecione **Microsoft.Azure.Devices**e, em seguida, **selecione Instalar**.

    Este passo descarrega, instala e adiciona uma referência ao [pacote SDK NuGet do serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) no projeto **ReadFileUploadNotification.**

1. No **Program.cs** ficheiro para este projeto, adicione a seguinte declaração no topo do ficheiro:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o `{iot hub connection string}` valor do espaço reservado pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de [ligação do hub IoT](#get-the-iot-hub-connection-string):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adicione o seguinte método à classe **Programa:**

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

    Note que este padrão de receção é o mesmo usado para receber mensagens nuvem-dispositivo a partir da aplicação do dispositivo.

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

1. No Solutions Explorer, clique com o botão direito na sua solução e selecione **set StartUp Projects**.

1. No **Common Properties**Startup  >  **Project**, selecione **Vários projetos de startups,** em seguida, selecione a ação **iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**. Selecione **OK** para guardar as alterações.

1. Prima **F5**. Ambas as aplicações devem começar. Deverá ver o upload concluído numa aplicação de consola e a mensagem de notificação de upload recebida pela outra aplicação de consola. Pode utilizar o [portal Azure](https://portal.azure.com/) ou o Visual Studio Server Explorer para verificar a presença do ficheiro carregado na sua conta de Armazenamento Azure.

    ![Screenshot mostrando o ecrã de saída](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar as capacidades de upload de ficheiros do IoT Hub para simplificar os uploads de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades e cenários do IoT Hub com os seguintes artigos:

* [Crie um hub IoT programáticamente](iot-hub-rm-template-powershell.md)

* [Introdução à C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/quickstart-linux.md)