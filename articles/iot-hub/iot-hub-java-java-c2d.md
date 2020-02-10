---
title: Mensagens cloud-to-device com Azure IoT Hub (Java)  Microsoft Docs
description: Como enviar mensagens cloud-to-device para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para Java. Modifica uma aplicação simulada de dispositivopara receber mensagens cloud-to-device e modificar uma aplicação de back-end para enviar as mensagens cloud-to-device.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 518f8057f222a628f8c3cd077cad4a7362e2cac8
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110821"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Envie mensagens cloud-to-device com IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. A [telemetria Enviar de um dispositivo para um quickstart de hub IoT](quickstart-send-telemetry-java.md) mostra como criar um hub IoT, fornecer uma identidade de dispositivo no mesmo, e codificar uma aplicação simulada de dispositivo que envia mensagens dispositivo-para-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se em [Enviar a telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md). Mostra-lhe como fazer o seguinte:

* A partir da sua solução traseira, envie mensagens cloud-to-device para um único dispositivo através do IoT Hub.

* Receba mensagens cloud-to-device num dispositivo.

* A partir da sua solução na parte de trás, solicite reconhecimento de entrega *(feedback)* para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre [mensagens cloud-to-device no guia de desenvolvimento do IoT Hub](iot-hub-devguide-messaging.md).

No final deste tutorial, executa duas aplicações de consola Java:

* **dispositivo simulado**, uma versão modificada da aplicação criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md), que se conecta ao seu hub IoT e recebe mensagens cloud-to-device.

* **mensagens de envio c2d,** que enviam uma mensagem cloud-to-device para a aplicação simulada do dispositivo através do IoT Hub, e depois recebem o seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte SDK para muitas plataformas e idiomas de dispositivos (incluindo C, Java, Python e Javascript) através de SDKs do dispositivo Azure IoT. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código deste tutorial e, em geral, ao Azure IoT Hub, consulte o Centro de [Desenvolvimento Azure IoT](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Pré-requisitos

* Uma versão completa de trabalho da [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md) quickstart ou o [encaminhamento de mensagens Configure com tutorial IoT Hub.](tutorial-routing.md)

* [Kit](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)de Desenvolvimento Java SE 8 . Certifique-se de que seleciona **Java 8** sob suporte a **longo prazo** para obter downloads para JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação de dispositivo simulado

Nesta secção, modifica a aplicação simulada de dispositivo que criou no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md) para receber mensagens cloud-to-device do hub IoT.

1. Com um editor de texto, abra o ficheiro simulated-device\src\main\java\com\mycompany\app\App.java.

2. Adicione a seguinte aula **de MessageCallback** como uma classe aninhada dentro da classe **App.** O método **de execução** é invocado quando o dispositivo recebe uma mensagem do IoT Hub. Neste exemplo, o dispositivo notifica sempre o hub IoT que completou a mensagem:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modifique o método **principal** para criar uma instância **AppMessageCallback** e ligue para o método **setMessageCallback** antes de abrir o cliente da seguinte forma:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Se utilizar https em vez de MQTT ou AMQP como transporte, a instância **Do Cliente do Dispositivo** verifica frequentemente mensagens do IoT Hub (menos do que a cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTPS, e a aceleração do IoT Hub, consulte a [secção de mensagens do guia de desenvolvimento do IoT Hub.](iot-hub-devguide-messaging.md)

4. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo cria um serviço de backend para enviar mensagens cloud-to-device através do hub IoT que criou em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md). Para enviar mensagens cloud-to-device, o seu serviço necessita da permissão de ligação do **serviço.** Por padrão, cada Hub IoT é criado com uma política de acesso partilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem cloud-to-device

Nesta secção, cria-se uma aplicação de consola Java que envia mensagens cloud-to-device para a aplicação simulada do dispositivo. Precisa da identificação do dispositivo que adicionou na [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md) arranque rápido. Você também precisa da cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string).

1. Crie um projeto Maven chamado **enviar mensagens de envio c2d** utilizando o seguinte comando no seu pedido de comando. Note que este comando é um comando único e longo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A seu pedido de comando, navegue para a nova pasta de mensagens de envio c2d.

3. Utilizando um editor de texto, abra o ficheiro pom.xml na pasta de mensagens enviadas c2d e adicione a seguinte dependência ao nó de **dependências.** A adição da dependência permite-lhe utilizar o pacote **iothub-java-service-client** na sua aplicação para comunicar com o seu serviço de hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode consultar a versão mais recente do **iot-service-client** usando a [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Guarde e feche o ficheiro pom.xml.

5. Utilizando um editor de texto, abra o ficheiro de mensagens de envio c2d\src\main\java\com\mycompany\app\app.java.

6. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis de nível de classe à classe **App,** substituindo **{yourhubconnectionstring}** e **{yourdeviceid}** com os valores que observou anteriormente:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Substitua o método **principal** pelo seguinte código. Este código liga-se ao seu hub IoT, envia uma mensagem para o seu dispositivo e, em seguida, aguarda um reconhecimento que o dispositivo recebeu e processou a mensagem:

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
    > Para a simplicidade, este tutorial não implementa qualquer política de retenção. No código de produção, deve implementar políticas de retry (como backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

9. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Num pedido de comando na pasta do dispositivo simulado, execute o seguinte comando para começar a enviar telemetria para o seu hub IoT e para ouvir as mensagens cloud-to-device enviadas do seu hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar a aplicação de dispositivo simulado](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Num pedido de comando na pasta de mensagens de envio c2d, execute o seguinte comando para enviar uma mensagem cloud-to-device e aguarde um reconhecimento de feedback:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o comando para enviar a mensagem cloud-to-device](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e a receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub [IoT, consulte aceleradores de soluções Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de [desenvolvimento do IoT Hub.](iot-hub-devguide.md)
