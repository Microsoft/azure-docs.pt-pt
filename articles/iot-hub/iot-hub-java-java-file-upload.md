---
title: Carregar ficheiros a partir de dispositivos no Hub de IoT do Azure com Java | Documentos da Microsoft
description: Como carregar ficheiros a partir de um dispositivo para a cloud com o Azure IoT device SDK para Java. Os ficheiros carregados são armazenados num contentor de BLOBs de armazenamento do Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 09580f3bb5d6f6f5ccb15adddf0cf1f9e19c2210
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38619408"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Carregar ficheiros a partir do seu dispositivo para a cloud com o IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se sobre o código no [enviar mensagens da Cloud para o dispositivo com o IoT Hub](iot-hub-java-java-c2d.md) tutorial para mostrar a como usar o [ficheiro capacidades de carregamento do IoT Hub](iot-hub-devguide-file-upload.md) para carregar um ficheiro para [BLOBs do Azure armazenamento](../storage/index.yml). Este tutorial mostra-lhe como:

- Fornecer com segurança a um dispositivo do Azure URI de blob para carregar um ficheiro.
- Utilize as notificações de carregamento de arquivo do IoT Hub para acionar a processar o ficheiro no seu back-end de aplicação.

O [introdução ao IoT Hub](iot-hub-java-java-getstarted.md) e [enviar mensagens da Cloud para o dispositivo com o IoT Hub](iot-hub-java-java-c2d.md) tutoriais mostram-dispositivo-para-cloud e cloud-para-dispositivo mensagens funcionalidades básicas do IoT Hub. O [mensagens de processo dispositivo-para-Cloud](tutorial-routing.md) tutorial descreve uma maneira de armazenar de forma fiável as mensagens do dispositivo-para-cloud no armazenamento de Blobs do Azure. No entanto, em alguns cenários não pode facilmente mapear os dados de que seus dispositivos enviam nas mensagens dispositivo-para-cloud relativamente pequenas que aceita o IoT Hub. Por exemplo:

* Ficheiros grandes que contêm imagens
* Vídeos
* Dados de vibração amostrados em alta frequência
* Alguma forma de pré-processados dados.

Estes ficheiros são normalmente processada na cloud com ferramentas, como de lote [do Azure Data Factory](../data-factory/introduction.md) ou o [Hadoop](../hdinsight/index.yml) pilha. Quando precisar de upland arquivos de um dispositivo, pode continuar a utilizar a segurança e fiabilidade do IoT Hub.

No final deste tutorial executa duas aplicações de consola Java:

* **simulated-device**, uma versão modificada da aplicação criada no tutorial [mensagens Send Cloud-para-dispositivo com o IoT Hub]. Esta aplicação carrega um ficheiro para o armazenamento com um URI de SAS fornecido pelo IoT hub.
* **carregar-notificação de leitura-ficheiro**, que recebe notificações de carregamento de ficheiros do seu hub IoT.

> [!NOTE]
> IoT Hub suporta muitas plataformas de dispositivos e linguagens (incluindo C, .NET e Javascript) através de SDKs de dispositivo do IoT do Azure. Consulte a [Centro de Programadores do Azure IoT] para obter instruções passo a passo sobre como ligar o dispositivo ao IoT Hub do Azure.

Para concluir este tutorial, precisa do seguinte:

* A versão mais recente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, modificar a aplicação de dispositivo que criou no [enviar mensagens da Cloud para o dispositivo com o IoT Hub](iot-hub-java-java-c2d.md) para carregar um ficheiro para o hub IoT.

1. Copiar um ficheiro de imagem para o `simulated-device` pasta e mude o nome `myimage.png`.

1. Com um editor de texto, abra o `simulated-device\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Adicione a declaração de variável para o **aplicação** classe:

    ```java
    private static String fileName = "myimage.png";
    ```

1. Para processar mensagens de retorno de chamada de estado de carregamento de ficheiro, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Para carregar imagens para o IoT Hub, adicione o seguinte método para o **aplicação** classe para carregar imagens para o IoT Hub:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

1. Modificar a **principal** método para chamar os **uploadFile** método conforme mostrado no seguinte fragmento:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

1. Utilize o seguinte comando para criar o **simulated-device** aplicação e verificação de erros:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de carregamento do ficheiro

Nesta secção, vai criar uma aplicação de consola do Java que recebe mensagens de notificação de carregamento de arquivo do IoT Hub.

Terá do **iothubowner** cadeia de ligação do hub IoT concluir esta secção. Pode encontrar a cadeia de ligação no [portal do Azure](https://portal.azure.com/) sobre o **política de acesso partilhado** painel.

1. Criar um projeto Maven designado **carregamento-notificação de leitura-ficheiro** com o seguinte comando na sua linha de comandos. Tenha em atenção de que este comando é um comando único, por extenso:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. Na sua linha de comandos, navegue para o novo `read-file-upload-notification` pasta.

1. Com um editor de texto, abra a `pom.xml` de ficheiros a `read-file-upload-notification` pasta e adicione a seguinte dependência à **dependências** nó. Adicionar a dependência permite-lhe utilizar o **iothub-java-service-client** pacote na sua aplicação para comunicar com o seu serviço do hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do serviço iot** usando [pesquisa Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Guarde e feche o `pom.xml` ficheiro.

1. Com um editor de texto, abra o `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. Adicione as seguintes variáveis de nível de classe para o **aplicação** classe:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. Para obter informações sobre o carregamento de ficheiros para o console de impressão, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Recieve file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Para iniciar o thread que escuta notificações de carregamento de ficheiro, adicione o seguinte código para o **principal** método:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

1. Guarde e feche o `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Utilize o seguinte comando para criar o **carregamento-notificação de leitura-ficheiro** aplicação e verificação de erros:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

Uma linha de comandos a `read-file-upload-notification` pasta, execute o seguinte comando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Uma linha de comandos a `simulated-device` pasta, execute o seguinte comando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Captura de ecrã seguinte mostra a saída do **simulated-device** aplicação:

![Saída da aplicação de dispositivo simulado](media/iot-hub-java-java-upload/simulated-device.png)

Captura de ecrã seguinte mostra a saída do **carregamento-notificação de leitura-ficheiro** aplicação:

![Saída da aplicação de carregamento-notificação de leitura-ficheiro](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Pode utilizar o portal para ver o ficheiro carregado no contentor de armazenamento que configurou:

![Ficheiro carregado](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar as capacidades de carregamento de arquivo do IoT Hub para simplificar os carregamentos de ficheiros a partir de dispositivos. Pode continuar a explorar as funcionalidades do hub IoT e cenários com os seguintes artigos:

* [Criar um hub IoT através de programação][lnk-create-hub]
* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar ainda mais os recursos do IoT Hub, veja:

* [Simular um dispositivo com o IoT Edge][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Centro de Programadores do Azure IoT]: http://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md


