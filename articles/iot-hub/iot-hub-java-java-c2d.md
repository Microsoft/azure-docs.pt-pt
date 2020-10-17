---
title: Mensagens em nuvem-para-dispositivo com Azure IoT Hub (Java) Microsoft Docs
description: Como enviar mensagens nuvem-para-dispositivo para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para Java. Modifica uma aplicação de dispositivo simulada para receber mensagens nuvem-a-dispositivo e modifica uma aplicação de back-end para enviar as mensagens cloud-to-device.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 763b9e05adc07c02265dbb511c073b42df44ea95
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146865"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Envie mensagens cloud-to-device com IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações biducionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. O [envio de telemetria de um dispositivo para um quickstart do hub IoT](quickstart-send-telemetry-java.md) mostra como criar um hub IoT, fornecê-lo e codificar uma aplicação de dispositivo simulado que envia mensagens dispositivo-a-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se no [Envio de telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md). Mostra como fazer o seguinte:

* A partir da parte de trás da sua solução, envie mensagens nuvem-dispositivo para um único dispositivo através do IoT Hub.

* Receba mensagens nuvem-dispositivo num dispositivo.

* A partir da sua solução back end, solicite o reconhecimento de entrega *(feedback)* para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre [mensagens nuvem-dispositivo no guia de desenvolvimento do IoT Hub.](iot-hub-devguide-messaging.md)

No final deste tutorial, executam duas aplicações de consola Java:

* **dispositivo simulado**, uma versão modificada da app criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md), que se conecta ao seu hub IoT e recebe mensagens nuvem-dispositivo.

* **enviar-c2d-mensagens**, que envia uma mensagem nuvem-para-dispositivo para a aplicação do dispositivo simulado através do IoT Hub, e depois recebe o seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte SDK para muitas plataformas e idiomas de dispositivos (incluindo C, Java, Python e Javascript) através de SDKs de dispositivoS Azure IoT. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código deste tutorial, e geralmente ao Azure IoT Hub, consulte o [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Pré-requisitos

* Uma versão completa de funcionamento da [telemetria Enviar telemetria de um dispositivo para um quickstart do hub IoT](quickstart-send-telemetry-java.md) ou o encaminhamento de [mensagens Configure com tutorial IoT Hub.](tutorial-routing.md)

* [Kit de Desenvolvimento Java SE 8](/java/azure/jdk/?view=azure-java-stable). Certifique-se de que seleciona **Java 8** sob **suporte a longo prazo** para obter downloads para JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receba mensagens na aplicação do dispositivo simulado

Nesta secção, modifica a aplicação de dispositivo simulado criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-java.md) para receber mensagens nuvem-dispositivo do hub IoT.

1. Com um editor de texto, abra o ficheiro simulated-device\src\main\java\com\mycompany\app\App.java.

2. Adicione a seguinte classe **MessageCallback** como uma classe aninhada dentro da classe **App.** O método **de execução** é invocado quando o dispositivo recebe uma mensagem do IoT Hub. Neste exemplo, o dispositivo notifica sempre o hub IoT de que completou a mensagem:

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

4. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

O `execute` método na classe `AppMessageCallback` `IotHubMessageResult.COMPLETE` regressa. Isto notifica o IoT Hub de que a mensagem foi processada com sucesso e que a mensagem pode ser removida com segurança da fila do dispositivo. O dispositivo deve devolver este valor quando o seu processamento estiver concluído com sucesso, independentemente do protocolo que está a utilizar.

Com AMQP e HTTPS, mas não MQTT, o dispositivo também pode:

* Abandone uma mensagem, o que resulta em IoT Hub manter a mensagem na fila do dispositivo para consumo futuro.
* Rejeite uma mensagem que remova permanentemente a mensagem da fila do dispositivo.

Se algo acontecer que impeça o dispositivo de completar, abandonar ou rejeitar a mensagem, o IoT Hub irá, após um período de tempo fixo, fazer fila para a entrega novamente. Por este motivo, a lógica de processamento de mensagens na aplicação do dispositivo deve ser *idempotente*– de modo a que receber a mesma mensagem várias vezes produza o mesmo resultado.

Para obter informações mais detalhadas sobre como o IoT Hub processa mensagens nuvem-dispositivo, incluindo detalhes do ciclo de vida da mensagem nuvem-para-dispositivo, consulte [Enviar mensagens cloud-to-device a partir de um hub IoT](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Se utilizar HTTPS em vez de MQTT ou AMQP como transporte, o **exemplo de DeviceClient** verifica frequentemente as mensagens do IoT Hub (um mínimo de cada 25 minutos). Para obter mais informações sobre as diferenças entre suporte MQTT, AMQP e HTTPS, consulte [a orientação de comunicações Cloud-to-device](iot-hub-devguide-c2d-guidance.md) e [Escolha um protocolo de comunicação](iot-hub-devguide-protocols.md).

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

Neste artigo cria-se um serviço de backend para enviar mensagens nuvem-a-dispositivo através do hub IoT que criou [Enviar por email o artigo Telemetria para um hub IoT](quickstart-send-telemetry-java.md). Para enviar mensagens nuvem-para-dispositivo, o seu serviço necessita da permissão **de ligação de serviço.** Por padrão, cada IoT Hub é criado com uma política de acesso compartilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem nuvem-a-dispositivo

Nesta secção, cria-se uma aplicação java para consola que envia mensagens nuvem-para-dispositivo para a aplicação do dispositivo simulado. Precisa do dispositivo ID do dispositivo que adicionou na [telemetria Enviar telemetria de um dispositivo para um quickstart do hub IoT.](quickstart-send-telemetry-java.md) Também precisa da cadeia de ligação do hub IoT que copiou anteriormente na [cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string).

1. Crie um projeto Maven chamado **mensagens de envio-c2d** utilizando o seguinte comando no seu pedido de comando. Note que este comando é um único e longo comando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No seu comando, navegue para a nova pasta de mensagens de envio-c2d.

3. Utilizando um editor de texto, abra o ficheiro pom.xml na pasta de mensagens de envio e adicione a seguinte dependência ao nó de **dependências.** A adição da dependência permite-lhe utilizar o pacote **iothub-java-service-cliente** na sua aplicação para comunicar com o seu serviço de hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **iot-service-client** utilizando a [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Guarde e feche o ficheiro pom.xml.

5. Utilizando um editor de texto, abra o ficheiro send-c2d-messages\src\main\java\com\mycompany\app\App.java.

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

8. Substitua o método **principal** pelo seguinte código. Este código liga-se ao seu hub IoT, envia uma mensagem para o seu dispositivo e, em seguida, aguarda um reconhecimento de que o dispositivo recebeu e processou a mensagem:

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
    > Para simplificar, este tutorial não implementa nenhuma política de retenção. No código de produção, deve implementar políticas de reequipante (como o backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

9. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Numa mensagem de comando na pasta do dispositivo simulado, executar o seguinte comando para começar a enviar telemetria para o seu hub IoT e ouvir mensagens nuvem-dispositivo enviadas do seu centro:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar a aplicação de dispositivo simulado](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Numa mensagem de comando na pasta de mensagens enviar-c2d, executar o seguinte comando para enviar uma mensagem nuvem-para-dispositivo e aguardar um reconhecimento de feedback:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o comando para enviar a mensagem nuvem-para-dispositivo](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte os [Aceleradores de Solução IoT Azure](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvimento do [IoT Hub.](iot-hub-devguide.md)