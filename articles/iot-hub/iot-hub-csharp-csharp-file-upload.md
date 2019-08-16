---
title: Carregar arquivos de dispositivos para o Hub IoT do Azure com .NET | Microsoft Docs
description: Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para .NET. Os arquivos carregados são armazenados em um contêiner de blob do armazenamento do Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: 98b5fb06bf018c9176fc989786d8bd1821914f43
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558458"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial se baseia no código do tutorial [enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-csharp-csharp-c2d.md) para mostrar como usar os recursos de carregamento de arquivo do Hub IOT. Ele mostra como:

* Forneça com segurança um dispositivo com um URI de blob do Azure para carregar um arquivo.

* Use as notificações de upload de arquivo do Hub IoT para disparar o processamento do arquivo no back-end do aplicativo.

O tutorial [Enviar telemetria de um dispositivo para um início rápido do Hub IOT](quickstart-send-telemetry-dotnet.md) e [enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-csharp-csharp-c2d.md) mostrar a funcionalidade básica de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IOT. O tutorial [Configurar o roteamento de mensagens com o Hub IOT](tutorial-routing.md) descreve uma maneira de armazenar com confiança as mensagens do dispositivo para a nuvem no armazenamento de blobs Microsoft Azure. No entanto, em alguns cenários, não é possível mapear facilmente os dados que seus dispositivos enviam para as mensagens de dispositivo para nuvem relativamente pequenas que o Hub IoT aceita. Por exemplo:

* Arquivos grandes que contêm imagens

* Os meus vídeos

* Dados de vibração amostrados em alta frequência

* Alguma forma de dados pré-processados

Esses arquivos normalmente são processados em lote na nuvem usando ferramentas como [Azure data Factory](../data-factory/introduction.md) ou a pilha do [Hadoop](../hdinsight/index.yml) . Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

No final deste tutorial, você executará dois aplicativos de console .NET:

* **SimulatedDevice**. Esse aplicativo carrega um arquivo no armazenamento usando um URI de SAS fornecido pelo Hub IoT. É uma versão modificada do aplicativo criado no tutorial [enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-csharp-csharp-c2d.md) .

* **ReadFileUploadNotification**. Este aplicativo recebe notificações de upload de arquivo do Hub IoT.

> [!NOTE]
> O Hub IoT dá suporte a várias plataformas de dispositivo e linguagens, incluindo C, Java, Python e JavaScript, por meio de SDKs do dispositivo IoT do Azure. Consulte a [central de desenvolvedores do Azure IOT](https://azure.microsoft.com/develop/iot) para obter instruções passo a passo sobre como conectar seu dispositivo ao Hub IOT do Azure.

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Visual Studio

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você modificará o aplicativo do dispositivo criado em [enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-csharp-csharp-c2d.md) para receber mensagens da nuvem para o dispositivo do Hub IOT.

1. No Visual Studio Gerenciador de soluções, clique com o botão direito do mouse no projeto **SimulatedDevice** e selecione **Adicionar** > **Item existente**. Localize um arquivo de imagem e inclua-o em seu projeto. Este tutorial pressupõe que a imagem é `image.jpg`nomeada.

1. Clique com o botão direito do mouse na imagem e selecione **Propriedades**. Verifique se **a cópia para o diretório de saída** está definida como **copiar sempre**.

    ![Mostrar onde atualizar a propriedade de imagem para copiar para o diretório de saída](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. No arquivo **Program.cs** , adicione as seguintes instruções na parte superior do arquivo:

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

    O `UploadToBlobAsync` método usa o nome do arquivo e a origem do fluxo do arquivo a ser carregado e manipula o carregamento no armazenamento. O aplicativo de console exibe o tempo necessário para carregar o arquivo.

1. Adicione a seguinte linha ao método **principal** , logo antes `Console.ReadLine()`de:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como retirada exponencial, conforme sugerido em [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, você cria um serviço de back-end para receber mensagens de notificação de upload de arquivo do Hub IoT criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md). Para receber mensagens de notificação de upload de arquivo, seu serviço precisa da permissão de **conexão de serviço** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de upload de arquivo

Nesta seção, você escreverá um aplicativo de console .NET que recebe mensagens de notificação de upload de arquivo do Hub IoT.

1. Na solução atual do Visual Studio, selecione **arquivo** > **novo** > **projeto**. Em **criar um novo projeto**, selecione **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Nomeie o projeto *ReadFileUploadNotification*. Em **solução**, selecione **Adicionar à solução**. Selecione **Create** (Criar) para criar o projeto.

    ![Configurar o projeto ReadFileUploadNotification no Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReadFileUploadNotification** e selecione **gerenciar pacotes NuGet**.

1. No **Gerenciador de pacotes NuGet**, selecione **procurar**. Pesquise e selecione **Microsoft. Azure.** Devices e, em seguida, selecione **instalar**.

    Esta etapa baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do serviço IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) no projeto **ReadFileUploadNotification** .

1. No arquivo **Program.cs** para este projeto, adicione a seguinte instrução na parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o `{iot hub connection string}` valor do espaço reservado pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

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

    Observe que esse padrão de recebimento é o mesmo usado para receber mensagens da nuvem para o dispositivo do aplicativo do dispositivo.

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

1. No Gerenciador de soluções, clique com o botão direito do mouse em sua solução e selecione **definir projetos de inicialização**.

1. Em **Propriedades** > comuns**projeto de inicialização**, selecione **vários projetos de inicialização**e, em seguida, selecione a ação **Iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**. Selecione **OK** para guardar as alterações.

1. Pressione **F5**. Ambos os aplicativos devem iniciar. Você deve ver o carregamento concluído em um aplicativo de console e a mensagem de notificação de upload recebida pelo outro aplicativo de console. Você pode usar o [portal do Azure](https://portal.azure.com/) ou o Visual Studio Gerenciador de servidores para verificar a presença do arquivo carregado em sua conta de armazenamento do Azure.

    ![Captura de tela mostrando o ecrã de saída](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar a explorar os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um hub IoT programaticamente](iot-hub-rm-template-powershell.md)

* [Introdução ao SDK do C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [Implantando ia em dispositivos de borda com Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
