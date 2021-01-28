---
title: 'Quickstart: Enviar telemetria para Azure IoT Hub com Java'
description: Neste início rápido, irá executar duas aplicações Java de exemplo para enviar telemetria simulada para um hub IoT e ler telemetria do mesmo para processamento na cloud.
author: wesmc7777
manager: lizross
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 01/27/2021
ms.openlocfilehash: c0f1272bf195c6d5ef2dfe88cc6541f731fa51c8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928558"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Quickstart: Envie telemetria para um hub Azure IoT e leia-o com uma aplicação Java

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Neste arranque rápido, você envia telemetria para Azure IoT Hub e lê-lo com uma aplicação Java. O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Este quickstart utiliza duas aplicações java pré-escritas: uma para enviar a telemetria e outra para ler a telemetria a partir do centro. Antes de executar estas duas aplicações, tem de criar um hub IoT e registar um dispositivo no hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* Kit de Desenvolvimento Java SE 8. Em [Java, suporte a longo prazo para Azure e Azure Stack,](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)sob **suporte a longo prazo,** selecione Java **8**.

    Pode verificar qual a versão atual do Java no seu computador de desenvolvimento através do seguinte comando:

    ```cmd/sh
    java -version
    ```

* [Apache Maven 3](https://maven.apache.org/download.cgi).

    Pode verificar qual a versão atual do Maven no seu computador de desenvolvimento através do seguinte comando:

    ```cmd/sh
    mvn --version
    ```

* Faça o download ou clone do repositório azure-iot-samples-java utilizando o botão **código** na página do [repositório azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java). 

    Este artigo utiliza as [amostras de mensagens simuladas](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/simulated-device) e [de leitura-d2c](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/read-d2c-messages) do repositório.

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   **MyJavaDevice**: Este é o nome do dispositivo que está a registar. Recomenda-se usar **o MyJavaDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Executar o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

    **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no arranque rápido.

3. Também precisa do _ponto final compatível com Os Centros de Eventos,_ _caminho compatível com Os Hubs de Eventos_ e _da chave primária_ de serviço do seu hub IoT para permitir que a aplicação back-end se conecte ao seu hub IoT e recupere as mensagens. Os seguintes comandos obtêm estes valores para o hub IoT:

     **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Tome nota destes três valores, que utilizará mais tarde no arranque rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT e envia telemetria simulada de humidade e de temperatura.

1. Numa janela de terminal local, navegue para o diretório de raiz do projeto Java de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device**.

2. Abra o ficheiro **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** num editor de texto à sua escolha.

    Substitua o valor da `connString` variável pela cadeia de ligação do dispositivo que fez uma nota anterior. Em seguida, guarde as suas alterações para **SimulatedDevice.java**.

    ```java
    public class SimulatedDevice {
      // The device connection string to authenticate the device with your IoT hub.
      // Using the Azure CLI:
      // az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table

      //private static String connString = "{Your device connection string here}";    
      private static String connString = "HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}";    
     ```

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas e compile a aplicação de dispositivo simulado:

    ```cmd/sh
    mvn clean package
    ```

4. Na janela de terminal local, execute os seguintes comandos para executar a aplicação de dispositivo simulado:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Saída da telemetria enviada pelo dispositivo para o seu hub IoT](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

A aplicação back-end liga-se ao ponto final de **eventos** do lado do serviço no seu Hub IoT. A aplicação recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

1. Noutra janela de terminal local, navegue para o diretório de raiz do projeto Java de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\read-d2c-messages**.

2. Abra o ficheiro **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** num editor de texto à sua escolha. Atualize as seguintes variáveis e guarde as alterações no ficheiro.

    | Variável | Valor |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | Substitua o valor da variável pelo ponto final compatível com Os Centros de Eventos que fez uma nota anterior. |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | Substitua o valor da variável pelo caminho compatível com Os Centros de Eventos que fez uma nota anterior. |
    | `IOT_HUB_SAS_KEY`                | Substitua o valor da variável pela chave primária de serviço que fez uma nota de anterior. |

    ```java
    public class ReadDeviceToCloudMessages {
    
      private static final String EH_COMPATIBLE_CONNECTION_STRING_FORMAT = "Endpoint=%s/;EntityPath=%s;"
          + "SharedAccessKeyName=%s;SharedAccessKey=%s";
    
      // az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_ENDPOINT = "{your Event Hubs compatible endpoint}";
    
      // az iot hub show --query properties.eventHubEndpoints.events.path --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_PATH = "{your Event Hubs compatible name}";
    
      // az iot hub policy show --name service --query primaryKey --hub-name {your IoT Hub name}
      private static final String IOT_HUB_SAS_KEY = "{your service primary key}";    
    ```


3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas e compilar a aplicação back-end:

    ```cmd/sh
    mvn clean package
    ```

4. Na janela de terminal local, execute os seguintes comandos para executar a aplicação back-end:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação back-end recebe telemetria enviada pelo dispositivo simulado para o hub:

    ![Saída como aplicação back-end recebe telemetria enviada para o seu hub IoT](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximos passos

Neste quickstart, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub usando uma aplicação Java, e leu a telemetria do hub usando uma simples aplicação de back-end.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-java.md)
