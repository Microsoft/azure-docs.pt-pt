---
title: Quickstart - Envie telemetria para Azure IoT Hub quickstart (C) | Microsoft Docs
description: Neste início rápido, vai executar duas aplicações C de exemplo para enviar telemetria simulada para um hub IoT e para ler telemetria do hub IoT para processamento na cloud.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: a9478948cd5232c863014631acb884cd637a2f7d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864024"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Quickstart: Enviar telemetria de um dispositivo para um hub IoT e lê-lo com uma aplicação de back-end (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste guia de início rápido, irá enviar telemetria a partir de uma aplicação de dispositivo simulado, através do Hub IoT, para uma aplicação back-end para processamento.

O início rápido utiliza uma aplicação de exemplo de C do [Azure IoT Device SDK para C](iot-hub-device-sdk-c-intro.md) para enviar telemetria para um hub IoT. Os Azure IoT Device SDKs são escritos em [ANSI C (C99)](https://wikipedia.org/wiki/C99) para portabilidade e compatibilidade entre muitas plataformas. Antes de executar o código de exemplo, vai criar um hub IoT e registar o dispositivo simulado com esse hub.

Este artigo está escrito para Windows, mas também pode completar este quickstart no Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instale [o Visual Studio 2019](https://www.visualstudio.com/vs/) com o [desenvolvimento do "Desktop development with C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitado.

* Instale a versão mais recente do [Git](https://git-scm.com/download/).

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para este arranque rápido, você estará usando o [dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md). 

Para os seguintes ambientes, pode utilizar o SDK instalando estes pacotes e bibliotecas:

* **Linux**: pacotes apt-get estão disponíveis para Ubuntu 16.04 e 18.04 usando as seguintes arquiteturas CPU: amd64, arm64, armhf e i386. Para obter mais informações, veja [Using apt-get to create a C device client project on Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md) (Utilizar apt-get para criar um projeto de cliente de dispositivo C no Ubuntu).

* **mbed**: Para desenvolvedores que criam aplicações de dispositivos na plataforma mbed, publicámos uma biblioteca e amostras que o farão começar em minutos witH Azure IoT Hub. Para obter mais informações, veja [Use the mbed library](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed) (Utilizar a biblioteca mbed).

* **Arduino**: Se estiver a desenvolver em Arduino, pode aproveitar a biblioteca Azure IoT disponível no gestor da biblioteca Arduino IDE. Para obter mais informações, veja [The Azure IoT Hub library for Arduino](https://github.com/azure/azure-iot-arduino) (A biblioteca do IoT Hub do Azure para Arduino).

* **iOS**: o SDK de Dispositivo do Hub IoT está disponível como CocoaPods para dispositivos Mac e iOS. Para obter mais informações, veja [Exemplos de iOS para o Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

No entanto, neste arranque rápido, você vai preparar um ambiente de desenvolvimento usado para clonar e construir o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) do GitHub. O SDK no GitHub inclui o código de exemplo utilizado neste início rápido.

1. Descarregue o [sistema de construção CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Encontre o nome da etiqueta para a [última versão](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar a mais recente versão do repositório [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub. Utilize a etiqueta encontrada no passo anterior como o valor para o `-b` parâmetro:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

4. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Executar os seguintes comandos a partir do `azure-iot-sdk-c` diretório:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Executar o seguinte comando para construir uma versão do SDK específica para a sua plataforma de cliente de desenvolvimento. Será gerada uma solução do Visual Studio para o dispositivo simulado no diretório `cmake`.

    ```cmd
    cmake ..
    ```

    Se `cmake` não encontrar o seu compilador C++, poderá obter erros de construção enquanto executa o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Assim que a compilação for concluída com êxito, as últimas linhas de saída terão um aspeto semelhante ao seguinte:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utilizará o Azure Cloud Shell com a [extensão IoT](/cli/azure/iot) para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   **MyCDevice**: Este é o nome do dispositivo que está a registar. Recomenda-se a utilização **do MyCDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Executar o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no arranque rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT e envia uma cadeia de carateres como telemetria simulada.

1. Com um editor de texto, abra o ficheiro de origem iothub_convenience_sample.c e reveja o código de exemplo para enviar telemetria. O ficheiro encontra-se no seguinte local sob o diretório de trabalho onde clonou o Azure IoT C SDK:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Procure a declaração da constante `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Substitua o valor da `connectionString` constante pela cadeia de ligação do dispositivo que fez uma nota anterior. Em seguida, guarde as alterações ao **iothub_convenience_sample.c**.

3. Na janela do terminal local, navegue para o diretório do projeto *iothub_convenience_sample* no diretório CMake que criou no SDK C do Azure IoT. Introduza o seguinte comando a partir do seu diretório de trabalho:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Execute o CMake na janela de terminal local para criar o exemplo com o seu valor `connectionString` atualizado:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Na janela do terminal local, executar o seguinte comando para executar a aplicação do dispositivo simulado:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta secção, utilizará o Azure Cloud Shell com a [extensão IoT](/cli/azure/iot) para monitorizar as mensagens do dispositivo que são enviadas pelo dispositivo simulado.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub usando uma aplicação C, e leu a telemetria do hub usando a Azure Cloud Shell.

Para saber mais sobre a programação com o SDK C do Azure IoT Hub, avance para o Manual de instruções seguinte:

> [!div class="nextstepaction"]
> [Programar com o SDK C do Azure IoT Hub](iot-hub-devguide-develop-for-constrained-devices.md)
