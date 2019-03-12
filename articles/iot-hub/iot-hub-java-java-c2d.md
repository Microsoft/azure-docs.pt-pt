---
title: Mensagens de cloud para o dispositivo com o IoT Hub do Azure (Java) | Documentos da Microsoft
description: Como enviar mensagens da cloud para dispositivo a um dispositivo de um hub IoT do Azure com os SDKs IoT do Azure para Java. Modificar uma aplicação de dispositivo simulado para receber mensagens da cloud para o dispositivo e modificar uma aplicação de back-end para enviar as mensagens de cloud-para-dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 9032a6903833ba819e09fd1ca11cd6b43d5485cb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541694"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Enviar mensagens da cloud para dispositivo hub IoT (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O IoT Hub do Azure é um serviço totalmente gerido que ajuda a habilitar comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução de back-end. O [enviar telemetria a partir de um dispositivo para um Hub (Java)](quickstart-send-telemetry-java.md) tutorial mostra como criar um hub IoT, aprovisionar uma identidade de dispositivo no mesmo e código de uma aplicação de dispositivo simulado que envia mensagens do dispositivo para a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se nas [enviar telemetria a partir de um dispositivo para um IoT Hub (Java)](quickstart-send-telemetry-java.md). Ele mostra como fazer o seguinte:

* Da sua solução back-end, envie mensagens de cloud-para-dispositivo para um único dispositivo através do IoT Hub.

* Receba mensagens da cloud para o dispositivo num dispositivo.

* Da sua solução back-end, pedir confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre [mensagens na cloud para o dispositivo no Guia do programador do IoT Hub](iot-hub-devguide-messaging.md).

No final deste tutorial, executa duas aplicações de consola Java:

* **simulated-device**, uma versão modificada da aplicação que criou no [enviar telemetria a partir de um dispositivo para um Hub (Java)](quickstart-send-telemetry-java.md), que liga ao seu hub IoT e recebe mensagens de cloud-para-dispositivo.

* **Enviar-c2d-messages**, que envia uma mensagem de cloud-para-dispositivo para a aplicação de dispositivo simulado através do IoT Hub e, em seguida, recebe a confirmação de entrega.

> [!NOTE]
> IoT Hub tem suporte SDK para muitas plataformas de dispositivos e linguagens (incluindo C, Java e Javascript) através de SDKs de dispositivo do IoT do Azure. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código do tutorial e, em geral, hub IoT do Azure, consulte a [Centro de programadores de IoT do Azure](https://azure.microsoft.com/develop/iot).

Para concluir este tutorial, precisa do seguinte:

* Uma versão de trabalho completa da [enviar telemetria a partir de um dispositivo para um Hub (Java)](quickstart-send-telemetry-java.md) ou o [configurar o encaminhamento de mensagens com o IoT Hub](tutorial-routing.md) tutorial.

* A versão mais recente de [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação do dispositivo simulado

Nesta secção, modificar a aplicação de dispositivo simulado que criou no [enviar telemetria a partir de um dispositivo para um Hub (Java)](quickstart-send-telemetry-java.md) para receber mensagens da cloud para dispositivo do IoT hub.

1. Com um editor de texto, abra o ficheiro simulated-device\src\main\java\com\mycompany\app\App.java.

2. Adicione as seguintes **MessageCallback** classe como uma classe aninhada dentro de **aplicação** classe. O **executar** método é invocado quando o dispositivo recebe uma mensagem do IoT Hub. Neste exemplo, o dispositivo sempre notifica o hub IoT que concluiu a mensagem:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Modificar a **principal** método para criar um **AppMessageCallback** instância e a chamada a **setMessageCallback** método antes de ele abre o cliente da seguinte forma:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Se utilizar HTTPS em vez de MQTT ou AMQP como o transporte, o **DeviceClient** instância verifica a existência de mensagens do Hub de IoT com pouca frequência (menos de 25 minutos). Para obter mais informações sobre as diferenças entre o suporte para MQTT, AMQP e HTTPS e a limitação do IoT Hub, consulte a [secção do Guia do programador do IoT Hub de mensagens](iot-hub-devguide-messaging.md).

4. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de cloud-para-dispositivo

Nesta secção, vai criar uma aplicação de consola Java que envia mensagens de cloud-para-dispositivo para a aplicação de dispositivo simulado. Precisa do ID de dispositivo do dispositivo que adicionou no [enviar telemetria a partir de um dispositivo para um Hub (Java)](quickstart-send-telemetry-java.md) início rápido. Também precisa da cadeia de ligação do IoT Hub para o hub que pode encontrar no [portal do Azure](https://portal.azure.com).

1. Criar um projeto Maven designado **send-c2d-messages** com o seguinte comando na sua linha de comandos. Tenha em atenção de que este comando é um comando único, por extenso:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na sua linha de comandos, navegue para a nova pasta de envio-c2d-messages.

3. Com um editor de texto, abra o ficheiro pom. xml na pasta send-c2d-messages e adicione a seguinte dependência para o **dependências** nó. Adicionar a dependência permite-lhe utilizar o **iothub-java-service-client** pacote na sua aplicação para comunicar com o seu serviço do hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do serviço iot** usando [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Guarde e feche o ficheiro pom.xml.

5. Com um editor de texto, abra o ficheiro de send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis de nível de classe para o **App** de classe, substituindo **{yourhubconnectionstring}** e **{yourdeviceid}** com os valores que apontou anteriormente:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Substitua a **principal** método com o código a seguir. Este código liga ao seu hub IoT, envia uma mensagem para o seu dispositivo e, em seguida, aguarda uma confirmação de que o dispositivo recebido e processado a mensagem:
   
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
    > Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).


9. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Numa linha de comandos da pasta simulated-device, execute o seguinte comando para começar a enviar telemetria ao seu hub IoT e para escutar as mensagens de cloud-para-dispositivo enviadas a partir do seu hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Executar a aplicação de dispositivo simulado](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Uma linha de comandos na pasta a enviar-c2d-messages, execute o seguinte comando para enviar uma mensagem de cloud-para-dispositivo e aguardar uma confirmação de comentários:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Execute o comando para enviar a mensagem de cloud-para-dispositivo](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a enviar e receber mensagens da cloud para o dispositivo. 

Para ver exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, veja [Aceleradores de solução de IoT do Azure](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, veja a [Guia do programador do IoT Hub](iot-hub-devguide.md).