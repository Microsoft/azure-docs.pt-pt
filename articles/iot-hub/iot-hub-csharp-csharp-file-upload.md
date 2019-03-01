---
title: Carregar ficheiros a partir de dispositivos no Hub de IoT do Azure com .NET | Documentos da Microsoft
description: Como carregar ficheiros a partir de um dispositivo para a cloud com o Azure IoT device SDK para .NET. Os ficheiros carregados são armazenados num contentor de BLOBs de armazenamento do Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robin.shahan
ms.openlocfilehash: d661f8834aec77957e0fd2713cde5da9a48903fd
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010581"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Carregar ficheiros a partir do seu dispositivo para a cloud com o IoT Hub com .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se sobre o código no [enviar mensagens da Cloud para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) tutorial para mostrar como utilizar as capacidades de carregamento de arquivo do IoT Hub. Mostra-lhe como para:

- Fornecer com segurança a um dispositivo do Azure URI de blob para carregar um ficheiro.

- Utilize as notificações de carregamento de arquivo do IoT Hub para acionar a processar o ficheiro no seu back-end de aplicação.

O [enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md) e [enviar mensagens da Cloud para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) artigos mostram a básica funcionalidade de mensagens dispositivo-para-cloud e na cloud para o dispositivo do IoT Hub. O [configurar o encaminhamento de mensagens com o IoT Hub](tutorial-routing.md) tutorial descreve uma maneira de armazenar de forma fiável as mensagens do dispositivo-para-cloud no armazenamento de Blobs do Azure. No entanto, em alguns cenários não pode facilmente mapear os dados de que seus dispositivos enviam nas mensagens dispositivo-para-cloud relativamente pequenas que aceita o IoT Hub. Por exemplo:

* Ficheiros grandes que contêm imagens
* Vídeos
* Dados de vibração amostrados em alta frequência
* Alguma forma de dados pré-processados

Estes ficheiros são normalmente processada na cloud com ferramentas, como de lote [do Azure Data Factory](../data-factory/introduction.md) ou o [Hadoop](../hdinsight/index.yml) pilha. Quando tiver de carregar ficheiros a partir de um dispositivo, pode continuar a utilizar a segurança e fiabilidade do IoT Hub.

No final deste tutorial executa duas aplicações de consola .NET:

* **SimulatedDevice**, uma versão modificada da aplicação criada no [enviar mensagens da Cloud para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) tutorial. Esta aplicação carrega um ficheiro para o armazenamento com um URI de SAS fornecido pelo IoT hub.

* **ReadFileUploadNotification**, que recebe notificações de carregamento de ficheiros do seu hub IoT.

> [!NOTE]
> IoT Hub suporta muitas plataformas de dispositivos e linguagens (incluindo C, Java e Javascript) através de SDKs de dispositivo do IoT do Azure. Consulte a [Centro de programadores de IoT do Azure](https://azure.microsoft.com/develop/iot) para obter instruções passo a passo sobre como ligar o dispositivo ao IoT Hub do Azure.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2017
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, modificar a aplicação de dispositivo que criou no [enviar mensagens da Cloud para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) para receber mensagens da cloud para dispositivo do IoT hub.

1. No Visual Studio, clique com botão direito a **SimulatedDevice** do projeto, clique em **Add**e, em seguida, clique em **Item existente**. Navegue para um ficheiro de imagem e incluí-lo em seu projeto. Este tutorial pressupõe que a imagem com o nome `image.jpg`.

1. Faça duplo clique na imagem e, em seguida, clique em **propriedades**. Certifique-se de que **Copy to Output Directory** está definida como **Copiar sempre**.

    ![Mostrar onde deve atualizar a propriedade de imagem para copiar para diretório de saída](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Na **Program.cs** de ficheiros, adicione as seguintes declarações na parte superior do ficheiro:

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

    O `UploadToBlobAsync` método aceita a origem de nome e o fluxo do ficheiro do arquivo a ser carregado e manipula o carregamento para o armazenamento. A aplicação de consola mostra o tempo que demora a carregar o ficheiro.

1. Adicione o seguinte método na **Main** método, logo antes do `Console.ReadLine()` linha:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de carregamento do ficheiro

Nesta secção, escrever uma aplicação de consola .NET que recebe mensagens de notificação de carregamento de arquivo do IoT Hub.

1. A solução atual do Visual Studio, criar um projeto do Visual c# Windows com o **aplicação de consola** modelo de projeto. Nomeie o projeto **ReadFileUploadNotification**.

    ![Novo projeto no Visual Studio](./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png)

2. No Solution Explorer, clique com botão direito a **ReadFileUploadNotification** projeto e, em seguida, clique em **gerir pacotes NuGet...** .

3. Na **Gestor de pacotes NuGet** janela, procure **Microsoft.Azure.Devices**, clique em **instalar**e aceite os termos de utilização.

    Esta ação transfere, instala e adiciona uma referência para o [pacote de NuGet do SDK do serviço do Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) no **ReadFileUploadNotification** projeto.

4. Na **Program.cs** de ficheiros, adicione as seguintes declarações na parte superior do ficheiro:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do hub IoT partir [enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

6. Adicione o seguinte método à classe **Programa**:

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

    Tenha em atenção que adrão receive é o mesmo utilizado para receber mensagens da cloud para dispositivo a partir da aplicação de dispositivo.

7. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

1. No Visual Studio, sua solução com o botão direito e selecione **definir projetos de arranque**. Selecione **vários projetos de arranque**, em seguida, selecione a **iniciar** ação para **ReadFileUploadNotification** e **SimulatedDevice**.

2. Prima **F5**. Ambas as aplicações devem começar. Deverá ver o carregamento foi concluída num aplicativo de console e a mensagem de notificação de carregamento recebida por outra aplicação de consola. Pode utilizar o [portal do Azure](https://portal.azure.com/) ou o Visual Studio Server Explorer para verificar a presença do arquivo carregado na sua conta de armazenamento do Azure.

    ![Captura de ecrã que mostra o ecrã de saída](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar as capacidades de carregamento de arquivo do IoT Hub para simplificar os carregamentos de ficheiros a partir de dispositivos. Pode continuar a explorar as funcionalidades do hub IoT e cenários com os seguintes artigos:

* [Criar um hub IoT através de programação](iot-hub-rm-template-powershell.md)
* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)
* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais os recursos do IoT Hub, veja:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

