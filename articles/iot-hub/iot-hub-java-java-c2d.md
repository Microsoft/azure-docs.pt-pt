---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (Java) | Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um hub IoT do Azure usando os SDKs do IoT do Azure para Java. Você modifica um aplicativo de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviar as mensagens da nuvem para o dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: e844fbe542e0517ffe1f99b550544e6eaca22885
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402369"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Enviar mensagens da nuvem para o dispositivo com o Hub IoT (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a habilitar comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. O guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-java.md) mostra como criar um hub IOT, provisionar uma identidade do dispositivo nele e codificar um aplicativo de dispositivo simulado que envia mensagens do dispositivo para a nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia no [envio de telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-java.md). Ele mostra como fazer o seguinte:

* De seu back-end da solução, envie mensagens da nuvem para o dispositivo para um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

* Do seu back-end da solução, solicite a confirmação de entrega (*comentários*) para mensagens enviadas a um dispositivo do Hub IOT.

Você pode encontrar mais informações sobre [as mensagens da nuvem para o dispositivo no guia do desenvolvedor do Hub IOT](iot-hub-devguide-messaging.md).

No final deste tutorial, você executará dois aplicativos de console do Java:

* **Simulated-Device**, uma versão modificada do aplicativo criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-java.md), que se conecta ao Hub IOT e recebe mensagens da nuvem para o dispositivo.

* **Send-C2D-messages**, que envia uma mensagem da nuvem para o dispositivo para o aplicativo de dispositivo simulado por meio do Hub IOT e recebe sua confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte do SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java e JavaScript) por meio de SDKs do dispositivo IoT do Azure. Para obter instruções detalhadas sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, consulte o centro de [desenvolvedores do Azure IOT](https://azure.microsoft.com/develop/iot).

Para concluir este tutorial, precisa do seguinte:

* Uma versão de trabalho completa do tutorial [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-java.md) ou [Configurar o roteamento de mensagens com o Hub IOT](tutorial-routing.md) .

* A versão mais recente de [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens no aplicativo do dispositivo simulado

Nesta seção, você modificará o aplicativo de dispositivo simulado criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-java.md) para receber mensagens da nuvem para o dispositivo do Hub IOT.

1. Com um editor de texto, abra o ficheiro simulated-device\src\main\java\com\mycompany\app\App.java.

2. Adicione a seguinte classe **MessageCallback** como uma classe aninhada dentro da classe **app** . O método **Execute** é invocado quando o dispositivo recebe uma mensagem do Hub IOT. Neste exemplo, o dispositivo sempre notifica o Hub IoT de que concluiu a mensagem:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modifique o método **Main** para criar uma instância **AppMessageCallback** e chame o método **setMessageCallback** antes de abrir o cliente da seguinte maneira:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Se você usar HTTPS em vez de MQTT ou AMQP como o transporte, a instância **DeviceClient** verificará mensagens do Hub IOT com pouca frequência (menos do que a cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte a MQTT, AMQP e HTTPS e a limitação do Hub IoT, consulte a [seção mensagens do guia do desenvolvedor do Hub IOT](iot-hub-devguide-messaging.md).

4. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens da nuvem para o dispositivo por meio do Hub IoT criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-java.md). Para enviar mensagens da nuvem para o dispositivo, seu serviço precisa da permissão de **conexão de serviço** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem da nuvem para o dispositivo

Nesta seção, você cria um aplicativo de console Java que envia mensagens da nuvem para o dispositivo para o aplicativo do dispositivo simulado. Você precisa da ID do dispositivo que adicionou no guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-java.md) . Você também precisa da cadeia de conexão do Hub IoT que copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

1. Crie um projeto Maven chamado **Send-C2D-messages** usando o comando a seguir no prompt de comando. Observe que esse comando é um comando único e longo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a nova pasta Send-C2D-messages.

3. Usando um editor de texto, abra o arquivo pom. xml na pasta Send-C2D-messages e adicione a dependência a seguir ao nó **Dependencies** . Adicionar a dependência permite que você use o pacote **iothub-Java-Service-Client** em seu aplicativo para se comunicar com o serviço de Hub IOT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **IOT-Service-Client** usando a [pesquisa do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Guarde e feche o ficheiro pom.xml.

5. Usando um editor de texto, abra o arquivo send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis de nível de classe à classe **app** , substituindo **{cadeiadeconexãodoseuhub}** e **{iddoseudispositivo}** pelos valores que você anotou anteriormente:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Substitua o método **Main** pelo código a seguir. Esse código se conecta ao seu hub IoT, envia uma mensagem para o dispositivo e aguarda uma confirmação de que o dispositivo recebeu e processou a mensagem:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como retirada exponencial), conforme sugerido no artigo [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

9. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Em um prompt de comando na pasta Simulated-Device, execute o seguinte comando para começar a enviar telemetria para o Hub IoT e para escutar mensagens da nuvem para o dispositivo enviadas do seu Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o aplicativo de dispositivo simulado](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Em um prompt de comando na pasta Send-C2D-messages, execute o seguinte comando para enviar uma mensagem da nuvem para o dispositivo e aguardar uma confirmação de comentário:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Execute o comando para enviar a mensagem da nuvem para o dispositivo](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas de ponta a ponta que usam o Hub IoT, consulte [aceleradores de solução do Azure IOT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre como desenvolver soluções com o Hub IoT, confira o [Guia do desenvolvedor do Hub IOT](iot-hub-devguide.md).
