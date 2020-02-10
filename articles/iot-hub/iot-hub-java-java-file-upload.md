---
title: Faça upload de ficheiros de dispositivos para O Hub Azure IoT com Java Microsoft Docs
description: Como fazer o upload de ficheiros de um dispositivo para a nuvem utilizando o dispositivo Azure IoT SDK para Java. Os ficheiros carregados são armazenados num recipiente de armazenamento Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: fcc2013f67c6e91182979a9bcab683894088a1d5
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110372"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se no código no envio de [mensagens cloud-to-device com tutorial IoT Hub](iot-hub-java-java-c2d.md) para lhe mostrar como usar as [capacidades de upload de ficheiros do IoT Hub](iot-hub-devguide-file-upload.md) para fazer o upload de um ficheiro para o armazenamento de [blob Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça de forma segura um dispositivo com um blob URI Azure para o upload de um ficheiro.

* Utilize as notificações de upload de ficheiros IoT Hub para desencadear o processamento do ficheiro na parte de trás da sua aplicação.

A [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md) quickstart e [enviar mensagens cloud-to-device com tutorial IoT Hub](iot-hub-java-java-c2d.md) mostram a funcionalidade básica de mensagens de dispositivo-nuvem e nuvem-a-dispositivo do IoT Hub. O [encaminhamento de mensagens Configure com o tutorial IoT Hub](tutorial-routing.md) descreve uma forma de armazenar de forma fiável mensagens dispositivo-a-nuvem no armazenamento de blob Azure. No entanto, em alguns cenários não é possível mapear facilmente os dados que os seus dispositivos enviam para as mensagens relativamente pequenas de dispositivo-nuvem que o IoT Hub aceita. Por exemplo:

* Ficheiros grandes que contêm imagens
* Vídeos
* Dados de vibração recolhidos em alta frequência
* Alguma forma de dados pré-processados.

Estes ficheiros são normalmente processados na nuvem utilizando ferramentas como a [Azure Data Factory](../data-factory/introduction.md) ou a stack [Hadoop.](../hdinsight/index.yml) Quando precisa de ficheiros de um dispositivo, ainda pode utilizar a segurança e a fiabilidade do IoT Hub.

No final deste tutorial executa duas aplicações de consola Java:

* **dispositivo simulado**, uma versão modificada da app criada no tutorial [Enviar mensagens cloud-to-device com IoT Hub]. Esta aplicação envia um ficheiro para armazenamento utilizando um SAS URI fornecido pelo seu hub IoT.

* **read-file-upload-notification**, que recebe notificações de upload de ficheiros do seu hub IoT.

> [!NOTE]
> O IoT Hub suporta muitas plataformas e idiomas de dispositivos (incluindo C, .NET e Javascript) através de SDKs do dispositivo Azure IoT. Consulte o Centro de [Desenvolvimento Azure IoT](https://azure.microsoft.com/develop/iot) para obter instruções passo a passo sobre como ligar o seu dispositivo ao Hub Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos

* [Kit](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)de Desenvolvimento Java SE 8 . Certifique-se de que seleciona **Java 8** sob suporte a **longo prazo** para obter downloads para JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Faça upload de um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, modifica a aplicação do dispositivo que criou em [Enviar mensagens cloud-to-device com IoT Hub](iot-hub-java-java-c2d.md) para fazer o upload de um ficheiro para o hub IoT.

1. Copie um ficheiro de imagem para a pasta `simulated-device` e mude o nome `myimage.png`.

2. Usando um editor de texto, abra o ficheiro `simulated-device\src\main\java\com\mycompany\app\App.java`.

3. Adicione a declaração variável à classe **App:**

    ```java
    private static String fileName = "myimage.png";
    ```

4. Para processar mensagens de chamada de registo de envio de ficheiros, adicione a seguinte classe aninhada à classe **App:**

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Para fazer upload de imagens para o IoT Hub, adicione o seguinte método à classe **App** para fazer o upload de imagens para o IoT Hub:

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

6. Modifique o método **principal** para chamar o método **uploadFile,** como mostrado no seguinte corte:

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

7. Utilize o seguinte comando para construir a aplicação **de dispositivo simulado** e verificar se há erros:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo cria um serviço de backend para receber mensagens de notificação de envio de ficheiros do hub IoT que criou no [Envio de telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md). Para receber mensagens de notificação de upload de ficheiros, o seu serviço necessita da permissão de ligação do **serviço.** Por padrão, cada Hub IoT é criado com uma política de acesso partilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receba uma notificação de upload de ficheiro

Nesta secção, cria-se uma aplicação de consola Java que recebe mensagens de notificação de upload de ficheiros do IoT Hub.

1. Crie um projeto Maven chamado **read-file-upload-notification** usando o seguinte comando no seu pedido de comando. Note que este comando é um comando único e longo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A seu pedido de comando, navegue para a nova pasta `read-file-upload-notification`.

3. Utilizando um editor de texto, abra o ficheiro `pom.xml` na pasta `read-file-upload-notification` e adicione a seguinte dependência ao nó de **dependências.** A adição da dependência permite-lhe utilizar o pacote **iothub-java-service-client** na sua aplicação para comunicar com o seu serviço de hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode consultar a versão mais recente do **iot-service-client** usando a [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Guarde e feche o ficheiro `pom.xml`.

5. Usando um editor de texto, abra o ficheiro `read-file-upload-notification\src\main\java\com\mycompany\app\App.java`.

6. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua o valor do espaço reservado `{Your IoT Hub connection string}` pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT:](#get-the-iot-hub-connection-string)

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Para imprimir informações sobre o upload do ficheiro para a consola, adicione a seguinte classe aninhada à classe **App:**

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

9. Para iniciar o fio que ouve as notificações de upload de ficheiros, adicione o seguinte código ao método **principal:**

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

10. Guarde e feche o ficheiro `read-file-upload-notification\src\main\java\com\mycompany\app\App.java`.

11. Utilize o seguinte comando para construir a aplicação **de notificação de leitura-upload de ficheiros** e verificar se há erros:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

Num pedido de comando na pasta `read-file-upload-notification`, execute o seguinte comando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Num pedido de comando na pasta `simulated-device`, execute o seguinte comando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

A seguinte imagem mostra a saída da aplicação **de dispositivo simulado:**

![Saída da aplicação de dispositivo simulado](media/iot-hub-java-java-upload/simulated-device.png)

A seguinte imagem mostra a saída da aplicação de **notificação de leitura-ficheiro::**

![Saída da aplicação de notificação de leitura-upload de ficheiros](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Pode utilizar o portal para visualizar o ficheiro carregado no recipiente de armazenamento configurado:

![Ficheiro carregado](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar as capacidades de upload de ficheiros do IoT Hub para simplificar os uploads de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades e cenários do hub IoT com os seguintes artigos:

* [Criar um hub IoT programática](iot-hub-rm-template-powershell.md)

* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Simular um dispositivo com IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
