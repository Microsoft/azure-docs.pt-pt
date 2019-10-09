---
title: Enviar telemetria para o Hub IoT do Azure (C#) | Microsoft Docs
description: Neste início rápido, vai executar duas aplicações C de exemplo para enviar telemetria simulada para um hub IoT e para ler telemetria do hub IoT para processamento na cloud.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 4ccfa45c56a7e59024ce0639f218861054e32395
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166950"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Início rápido: Enviar telemetria de um dispositivo para um hub IoT e lê-lo com um aplicativo de back-end (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste guia de início rápido, irá enviar telemetria a partir de uma aplicação de dispositivo simulado, através do Hub IoT, para uma aplicação back-end para processamento.

O início rápido utiliza uma aplicação de exemplo de C do [Azure IoT Device SDK para C](iot-hub-device-sdk-c-intro.md) para enviar telemetria para um hub IoT. Os Azure IoT Device SDKs são escritos em [ANSI C (C99)](https://wikipedia.org/wiki/C99) para portabilidade e compatibilidade entre muitas plataformas. Antes de executar o código de exemplo, vai criar um hub IoT e registar o dispositivo simulado com esse hub.

Este artigo foi escrito para Windows, mas você também pode concluir este guia de início rápido no Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) com a carga de [trabalho C++' desenvolvimento de desktop com '](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).
* Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para este guia de início rápido, você usará o [SDK do dispositivo IOT do Azure para C](iot-hub-device-sdk-c-intro.md). 

Para os ambientes a seguir, você pode usar o SDK instalando estes pacotes e bibliotecas:

* **Linux**: apt-obter pacotes estão disponíveis para Ubuntu 16, 4 e 18, 4 usando as seguintes arquiteturas de CPU: AMD64, arm64, armhf e i386. Para obter mais informações, veja [Using apt-get to create a C device client project on Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md) (Utilizar apt-get para criar um projeto de cliente de dispositivo C no Ubuntu).

* **Mbed**: Para os desenvolvedores que criam aplicativos de dispositivo na plataforma Mbed, publicamos uma biblioteca e exemplos que irão ajudá-lo a começar em minutos com o Hub IoT do Azure. Para obter mais informações, veja [Use the mbed library](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed) (Utilizar a biblioteca mbed).

* **Arduino**: Se estiver desenvolvendo no Arduino, você poderá aproveitar a biblioteca IoT do Azure disponível no Gerenciador de biblioteca do IDE do Arduino. Para obter mais informações, veja [The Azure IoT Hub library for Arduino](https://github.com/azure/azure-iot-arduino) (A biblioteca do IoT Hub do Azure para Arduino).

* **iOS**: O SDK do dispositivo do Hub IoT está disponível como CocoaPods para desenvolvimento de dispositivos Mac e iOS. Para obter mais informações, veja [Exemplos de iOS para o Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

No entanto, neste guia de início rápido, você preparará um ambiente de desenvolvimento usado para clonar e compilar o [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) do github. O SDK no GitHub inclui o código de exemplo utilizado neste início rápido.

1. Baixe o [sistema de Build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (o Visual Studio e a carga de trabalho ' C++desenvolvimento de desktops com ') estejam instalados em seu computador, **antes** de iniciar a instalação `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Abra um prompt de comando ou shell do git bash e navegue até um diretório de trabalho para o qual você deseja clonar o SDK do Azure IoT C. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Esta operação deve demorar vários minutos a ser concluída.

3. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Insira os seguintes comandos em seu diretório de trabalho:

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Execute o comando a seguir para criar uma versão do SDK específica para sua plataforma de cliente de desenvolvimento. Será gerada uma solução do Visual Studio para o dispositivo simulado no diretório `cmake`.

    ```cmd
    cmake ..
    ```

    Se `cmake` não encontrar seu C++ compilador, você poderá obter erros de compilação ao executar o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta seção, você usará o Azure Cloud Shell com a [extensão de IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registrar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT.

   **MyCDevice**: Esse é o nome do dispositivo que você está registrando. É recomendável usar **MyCDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, também precisará usar esse nome em todo este artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar:

   **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT e envia uma cadeia de carateres como telemetria simulada.

1. Com um editor de texto, abra o ficheiro de origem iothub_convenience_sample.c e reveja o código de exemplo para enviar telemetria. O arquivo está localizado no seguinte local sob o diretório de trabalho em que você clonou o SDK do Azure IoT C:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Procure a declaração da constante `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Substitua o valor da constante `connectionString` pela cadeia de conexão do dispositivo anotada anteriormente. Em seguida, guarde as alterações ao **iothub_convenience_sample.c**.

3. Na janela do terminal local, navegue para o diretório do projeto *iothub_convenience_sample* no diretório CMake que criou no SDK C do Azure IoT. Digite o seguinte comando em seu diretório de trabalho:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Execute o CMake na janela de terminal local para criar o exemplo com o seu valor `connectionString` atualizado:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Na janela do terminal local, execute o seguinte comando para executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta seção, você usará o Azure Cloud Shell com a [extensão de IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorar as mensagens do dispositivo que são enviadas pelo dispositivo simulado.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você configura um hub IoT, registrou um dispositivo, enviou telemetria simulada para o Hub usando um aplicativo C e leu a telemetria do Hub usando o Azure Cloud Shell.

Para saber mais sobre a programação com o SDK C do Azure IoT Hub, avance para o Manual de instruções seguinte:

> [!div class="nextstepaction"]
> [Programar com o SDK C do Azure IoT Hub](iot-hub-devguide-develop-for-constrained-devices.md)