---
title: 'Quickstart: Envie telemetria para O Hub Azure IoT com Java'
description: Neste início rápido, irá executar duas aplicações Java de exemplo para enviar telemetria simulada para um hub IoT e ler telemetria do mesmo para processamento na cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: db2783844f41d1bb1c12f1dd41fd336a38c82e7e
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675657"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Quickstart: Envie telemetria para um hub Azure IoT e leia-o com uma aplicação Java

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Neste arranque rápido, envia-se telemetria para o Azure IoT Hub e lê-se com uma aplicação Java. O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Este quickstart usa duas aplicações java pré-escritas: uma para enviar a telemetria e outra para ler a telemetria do centro. Antes de executar estas duas aplicações, tem de criar um hub IoT e registar um dispositivo no hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* Kit de Desenvolvimento Java SE 8. Em [Java suporte a longo prazo para Azure e Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable), sob suporte a longo **prazo**, selecione **Java 8**.

* [Apache Maven 3.](https://maven.apache.org/download.cgi)

* [Um projeto de Java de amostra.](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip)

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste quickstart utiliza o protocolo MQTT, que comunica através da porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

Pode verificar qual a versão atual do Java no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
java -version
```

Pode verificar qual a versão atual do Maven no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
mvn --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Adicionar extensão Azure IoT

Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IoT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   **MyJavaDevice**: Este é o nome do dispositivo que está a registar. É aconselhável usar **o MyJavaDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Execute o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

    **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no início.

3. Você também precisa do ponto final compatível com Hubs de _eventos,_ _caminho compatível com Hubs_de eventos , e _chave primária_ de serviço do seu hub IoT para permitir que a aplicação back-end se ligue ao seu hub IoT e recupere as mensagens. Os seguintes comandos obtêm estes valores para o hub IoT:

     **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

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

    Substitua o valor da variável `connString` com a cadeia de ligação do dispositivo de que fez uma nota anterior. Em seguida, guarde as suas alterações para **SimulatedDevice.java**.

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas e compile a aplicação de dispositivo simulado:

    ```cmd/sh
    mvn clean package
    ```

4. Na janela de terminal local, execute os seguintes comandos para executar a aplicação de dispositivo simulado:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Saída da telemetria enviada pelo dispositivo para o seu hub IoT](media/quickstart-send-telemetry-java/iot-hub-simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

A aplicação back-end liga-se ao ponto final de **eventos** do lado do serviço no seu Hub IoT. A aplicação recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

1. Noutra janela de terminal local, navegue para o diretório de raiz do projeto Java de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\read-d2c-messages**.

2. Abra o ficheiro **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** num editor de texto à sua escolha. Atualize as seguintes variáveis e guarde as alterações no ficheiro.

    | Variável | Valor |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Substitua o valor da variável pelo ponto final compatível com os Hubs de Eventos que fez uma nota anterior. |
    | `eventHubsCompatiblePath`     | Substitua o valor da variável pelo caminho compatível com hubs de eventos que fez uma nota anterior. |
    | `iotHubSasKey`                | Substitua o valor da variável pela chave primária de serviço que fez uma nota anterior. |

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas e compilar a aplicação back-end:

    ```cmd/sh
    mvn clean package
    ```

4. Na janela de terminal local, execute os seguintes comandos para executar a aplicação back-end:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação back-end recebe telemetria enviada pelo dispositivo simulado para o hub:

    ![Saída como aplicação back-end recebe telemetria enviada para o seu hub IoT](media/quickstart-send-telemetry-java/iot-hub-read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub usando uma aplicação Java, e leu a telemetria do hub usando uma simples aplicação back-end.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-java.md)
