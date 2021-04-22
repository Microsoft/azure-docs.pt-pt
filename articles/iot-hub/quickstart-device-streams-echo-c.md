---
title: Quickstart - Comunique à aplicação do dispositivo em C com fluxos de dispositivoS Azure IoT Hub
description: Neste arranque rápido, você executou uma aplicação do lado do dispositivo C que comunica com um dispositivo IoT através de um fluxo de dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: ce0d0f9df4862d3c8ac8417e257fbf340e41961f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864128"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Quickstart: Comunicar a uma aplicação do dispositivo em C via fluxos de dispositivo ioT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

O Azure IoT Hub suporta atualmente os fluxos de dispositivos como [uma funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[As correntes de dispositivos IoT Hub](iot-hub-device-streams-overview.md) permitem que as aplicações de serviço e dispositivos se comuniquem de forma segura e amiga da firewall. Durante a pré-visualização pública, o C SDK suporta apenas os fluxos de dispositivos no lado do dispositivo. Como resultado, este arranque rápido cobre instruções para executar apenas a aplicação do lado do dispositivo. Para executar uma aplicação do lado do serviço correspondente, consulte estes artigos:

* [Comunicar para aplicativos de dispositivo em C# via streams de dispositivo ioT Hub](./quickstart-device-streams-echo-csharp.md)

* [Comunicar aplicativos de dispositivos em Node.js através de streams de dispositivos IoT Hub](./quickstart-device-streams-echo-nodejs.md)

A aplicação C do lado do dispositivo neste arranque rápido tem a seguinte funcionalidade:

* Estabeleça um fluxo de dispositivo para um dispositivo IoT.

* Receba os dados enviados da aplicação do lado do serviço e ecoe-os de volta.

O código demonstra o processo de iniciação de um fluxo de dispositivos, bem como como usá-lo para enviar e receber dados.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes pré-requisitos:

* Instale [o Visual Studio 2019](https://www.visualstudio.com/vs/) com o desenvolvimento do Desktop com a carga de trabalho **C++** ativada.

* Instale a versão mais recente do [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

A pré-visualização dos fluxos de dispositivos é atualmente suportada apenas para centros IoT que são criados nas seguintes regiões:

  * E.U.A. Central
  * EUA Central EUAP
  * Europa do Norte
  * Sudeste Asiático

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para este arranque rápido, utilize o [dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md). Você prepara um ambiente de desenvolvimento usado para clonar e construir o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) de GitHub. O SDK no GitHub inclui o código de amostra que é usado neste arranque rápido.

   > [!NOTE]
   > Antes de iniciar este procedimento, certifique-se de que o Visual Studio está instalado com o desenvolvimento do Desktop com carga de trabalho **C++.**

1. Instale o [sistema de construção CMake](https://cmake.org/download/) como descrito na página de descarregamento.

1. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar o repositório [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve levar alguns minutos.

1. Crie um subdiretório *de cmake* no diretório de raiz do repositório de git, e navegue para essa pasta. Executar os seguintes comandos a partir do diretório *azure-iot-sdk-c:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos do *diretório cmake* para construir uma versão do SDK que é específica para a sua plataforma de clientes de desenvolvimento.

   * Em Linux:

      ```bash
      cmake ..
      make -j
      ```

   * No Windows, abra um [pedido de comando do desenvolvedor para o Estúdio Visual](/dotnet/framework/tools/developer-command-prompt-for-vs). Executar o comando para a sua versão do Estúdio Visual. Este quickstart usa o Visual Studio 2019. Estes comandos criam uma solução Visual Studio para o dispositivo simulado no *diretório cmake.*

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Tem de registar um dispositivo com o seu hub IoT antes de poder ligar. Nesta secção, utilize a Azure Cloud Shell com a [Extensão IoT](/cli/azure/iot) para registar um dispositivo simulado.

1. Para criar a identidade do dispositivo, executar o seguinte comando em Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.
   > * Para o nome do dispositivo que está a registar, é aconselhável utilizar *o MyDevice* como mostrado. Se escolher um nome diferente para o seu dispositivo, use esse nome ao longo deste artigo e atualize o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de ligação* do dispositivo para o dispositivo que acabou de registar, execute o seguinte comando em Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Note a cadeia de ligação do dispositivo devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicar entre o dispositivo e o serviço através de fluxos de dispositivos

Nesta secção, execute a aplicação do lado do dispositivo e a aplicação do lado do serviço e comunique entre os dois.

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Para executar a aplicação do lado do dispositivo, siga estes passos:

1. Forneça as credenciais do seu dispositivo editando o ficheiro de origem **iothub_client_c2d_streaming_sample.c** na `iothub_client/samples/iothub_client_c2d_streaming_sample` pasta e adicionando a cadeia de ligação do dispositivo.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Compilar o código com os seguintes comandos:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Executar o programa compilado:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Executar a aplicação do lado do serviço

Como mencionado anteriormente, o IoT Hub C SDK suporta apenas fluxos de dispositivos no lado do dispositivo. Para construir e executar a aplicação do lado do serviço que o acompanha, siga as instruções numa das seguintes quickstarts:

* [Comunique a uma aplicação de dispositivo em C# através de streams de dispositivo ioT Hub](./quickstart-device-streams-echo-csharp.md)

* [Comunique a uma aplicação de dispositivo em Node.js através de streams de dispositivos IoT Hub](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hub IoT, registou um dispositivo, estabeleceu um fluxo de dispositivos entre uma aplicação C no dispositivo e outra aplicação no lado do serviço, e utilizou o fluxo para enviar dados entre as aplicações.

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
