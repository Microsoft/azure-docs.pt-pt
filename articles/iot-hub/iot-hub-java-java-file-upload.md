---
title: Carregar arquivos de dispositivos para o Hub IoT do Azure com Java | Microsoft Docs
description: Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para Java. Os arquivos carregados são armazenados em um contêiner de blob do armazenamento do Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 3d1f82c5eadde52edb721185a497d9b187eebb7b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668071"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial se baseia no código do tutorial [enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-java-java-c2d.md) para mostrar como usar os [recursos de carregamento de arquivo do Hub IOT](iot-hub-devguide-file-upload.md) para carregar um arquivo no [armazenamento de BLOBs do Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça com segurança um dispositivo com um URI de blob do Azure para carregar um arquivo.

* Use as notificações de upload de arquivo do Hub IoT para disparar o processamento do arquivo no back-end do aplicativo.

O tutorial [Enviar telemetria de um dispositivo para um início rápido do Hub IOT](quickstart-send-telemetry-java.md) e [enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-java-java-c2d.md) mostrar a funcionalidade básica de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IOT. O tutorial [Configurar o roteamento de mensagens com o Hub IOT](tutorial-routing.md) descreve uma maneira de armazenar de forma confiável as mensagens do dispositivo para a nuvem no armazenamento de BLOBs do Azure. No entanto, em alguns cenários, não é possível mapear facilmente os dados que seus dispositivos enviam para as mensagens relativamente pequenas que o Hub IoT aceita. Por exemplo:

* Arquivos grandes que contêm imagens
* Os meus vídeos
* Dados de vibração amostrados em alta frequência
* Alguma forma de dados pré-processados.

Esses arquivos normalmente são processados em lote na nuvem usando ferramentas como [Azure data Factory](../data-factory/introduction.md) ou a pilha do [Hadoop](../hdinsight/index.yml) . Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

Ao final deste tutorial, você executará dois aplicativos de console do Java:

* **Simulated-Device**, uma versão modificada do aplicativo criado no tutorial [enviar mensagens da nuvem para o dispositivo com o Hub IOT]. Esse aplicativo carrega um arquivo no armazenamento usando um URI de SAS fornecido pelo Hub IoT.

* **Read-file-upload-Notification**, que recebe notificações de upload de arquivo do Hub IOT.

> [!NOTE]
> O Hub IoT dá suporte a várias plataformas de dispositivo e linguagens (incluindo C, .NET e JavaScript) por meio de SDKs do dispositivo IoT do Azure. Consulte a [central de desenvolvedores do Azure IOT](https://azure.microsoft.com/develop/iot) para obter instruções passo a passo sobre como conectar seu dispositivo ao Hub IOT do Azure.

Para concluir este tutorial, precisa do seguinte:

* A versão mais recente de [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você modificará o aplicativo do dispositivo criado em [enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-java-java-c2d.md) para carregar um arquivo no Hub IOT.

1. Copie um arquivo de imagem para `simulated-device` a pasta e renomeie- `myimage.png`o.

2. Usando um editor de texto, abra `simulated-device\src\main\java\com\mycompany\app\App.java` o arquivo.

3. Adicione a declaração de variável à classe de **aplicativo** :

    ```java
    private static String fileName = "myimage.png";
    ```

4. Para processar mensagens de retorno de chamada de status de carregamento de arquivo, adicione a seguinte classe aninhada à classe de **aplicativo** :

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Para carregar imagens no Hub IoT, adicione o seguinte método à classe **app** para carregar imagens no Hub IOT:

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

6. Modifique o método **Main** para chamar o método **UploadFile** , conforme mostrado no trecho a seguir:

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

7. Use o seguinte comando para criar o aplicativo **Simulated-Device** e verificar se há erros:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, você cria um serviço de back-end para receber mensagens de notificação de upload de arquivo do Hub IoT criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-java.md). Para receber mensagens de notificação de upload de arquivo, seu serviço precisa da permissão de **conexão de serviço** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de upload de arquivo

Nesta seção, você cria um aplicativo de console Java que recebe mensagens de notificação de upload de arquivo do Hub IoT.

1. Crie um projeto Maven chamado **Read-file-upload-Notification** usando o comando a seguir no prompt de comando. Observe que esse comando é um comando único e longo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a nova `read-file-upload-notification` pasta.

3. Usando um editor de texto, abra `pom.xml` o arquivo `read-file-upload-notification` na pasta e adicione a dependência a seguir ao nó **Dependencies** . Adicionar a dependência permite que você use o pacote **iothub-Java-Service-Client** em seu aplicativo para se comunicar com o serviço de Hub IOT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **IOT-Service-Client** usando a [pesquisa do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Salve e feche o `pom.xml` arquivo.

5. Usando um editor de texto, abra `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` o arquivo.

6. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua o `{Your IoT Hub connection string}` valor do espaço reservado pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Para imprimir informações sobre o upload de arquivo para o console, adicione a seguinte classe aninhada à classe **app** :

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
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

9. Para iniciar o thread que escuta notificações de upload de arquivo, adicione o seguinte código ao método **principal** :

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

10. Salve e feche o `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` arquivo.

11. Use o seguinte comando para compilar o aplicativo **Read-file-upload-Notification** e verificar se há erros:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

Em um prompt de comando na `read-file-upload-notification` pasta, execute o seguinte comando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Em um prompt de comando na `simulated-device` pasta, execute o seguinte comando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

A captura de tela a seguir mostra a saída do aplicativo **Simulated-Device** :

![Saída do aplicativo Simulated-Device](media/iot-hub-java-java-upload/simulated-device.png)

A captura de tela a seguir mostra a saída do aplicativo **Read-file-upload-Notification** :

![Saída do aplicativo Read-file-upload-Notification](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Você pode usar o portal para exibir o arquivo carregado no contêiner de armazenamento que você configurou:

![Arquivo carregado](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar a explorar os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um hub IoT programaticamente](iot-hub-rm-template-powershell.md)

* [Introdução ao SDK do C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [Simulando um dispositivo com IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
