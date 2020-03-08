---
title: Comunicar para dispositivo app em C com streams de dispositivos Azure IoT Hub
description: Neste arranque rápido, executa-se uma aplicação do lado do dispositivo C que comunica com um dispositivo IoT através de um fluxo de dispositivos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 52f9e6529329c5bb1abb176082294dc26e64baa3
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675533"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Quickstart: Comunicar a uma aplicação do dispositivo em C através de fluxos de dispositivos IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

O Azure IoT Hub suporta atualmente os streams do dispositivo como recurso de [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os streams de [dispositivos IoT Hub](iot-hub-device-streams-overview.md) permitem que as aplicações de serviço e dispositivo saem de forma segura e amiga da firewall. Durante a pré-visualização pública, o C SDK suporta fluxos de dispositivos apenas no lado do dispositivo. Como resultado, este arranque rápido cobre instruções para executar apenas a aplicação do lado do dispositivo. Para executar uma aplicação do lado do serviço correspondente, consulte estes artigos:

* [Comunicar com aplicações de dispositivos através C# de fluxos de dispositivos IoT Hub](./quickstart-device-streams-echo-csharp.md)

* [Comunicar com aplicações de dispositivos no Node.js através de fluxos de dispositivos IoT Hub](./quickstart-device-streams-echo-nodejs.md)

A aplicação c do lado do dispositivo neste quickstart tem a seguinte funcionalidade:

* Estabeleça um fluxo de dispositivo para um dispositivo IoT.

* Receba os dados enviados da aplicação do lado do serviço e ecoe-os de volta.

O código demonstra o processo de iniciação de um fluxo de dispositivos, bem como como usá-lo para enviar e receber dados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes pré-requisitos:

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) com o desenvolvimento do **Ambiente de Trabalho com C++**  carga de trabalho ativada.

* Instale a versão mais recente do [Git](https://git-scm.com/download/).

* Execute o seguinte comando para adicionar a extensão Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) específicos para o Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

A visualização dos fluxos de dispositivos é atualmente suportada apenas para centros IoT que são criados nas seguintes regiões:

  * E.U.A. Central
  * EUA Centrais EUA
  * Europa do Norte
  * Ásia Sudeste

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para este arranque rápido, utilize o [dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md). Prepara-se um ambiente de desenvolvimento usado para clonar e construir o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a partir do GitHub. O SDK no GitHub inclui o código de amostra que é usado neste arranque rápido.

   > [!NOTE]
   > Antes de iniciar este procedimento, certifique-se de que o Estúdio Visual está instalado com o desenvolvimento do Ambiente de **Trabalho com C++**  carga de trabalho.

1. Instale o [sistema de construção CMake](https://cmake.org/download/) conforme descrito na página de descarregamento.

1. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar o [repositório Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar alguns minutos.

1. Crie um subdiretório *cmake* no diretório raiz do repositório git, e navegue para essa pasta. Executar os seguintes comandos do diretório *azure-iot-sdk-c:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos do diretório *de cmake* para construir uma versão do SDK específica para a sua plataforma de clientes de desenvolvimento.

   * Em Linux:

      ```bash
      cmake ..
      make -j
      ```

   * No Windows, abra um [Pedido de Comando de Desenvolvimento para Estúdio Visual](/dotnet/framework/tools/developer-command-prompt-for-vs). Execute o comando para a sua versão do Estúdio Visual. Este quickstart utiliza o Visual Studio 2019. Estes comandos criam uma solução De Estúdio Visual para o dispositivo simulado no diretório *de cmake.*

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

Tem de registar um dispositivo com o seu hub IoT antes de se ligar. Nesta secção, utiliza a Casca de Nuvem Azure com a [extensão IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando na Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.
   > * Para o nome do dispositivo que está a registar, é aconselhável utilizar o *MyDevice* como mostrado. Se escolher um nome diferente para o seu dispositivo, utilize esse nome ao longo deste artigo e atualize o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de ligação* do dispositivo para o dispositivo que acabou de registar, execute o seguinte comando na Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Note a cadeia de ligação do dispositivo devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicar entre o dispositivo e o serviço através de fluxos de dispositivos

Nesta secção, executa tanto a aplicação do lado do dispositivo como a aplicação do lado do serviço e comunica entre os dois.

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Para executar a aplicação do lado do dispositivo, siga estes passos:

1. Forneça as credenciais do seu dispositivo editando o ficheiro **fonte iothub_client_c2d_streaming_sample.c** na pasta `iothub_client/samples/iothub_client_c2d_streaming_sample` e adicionando a sua cadeia de ligação ao dispositivo.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Compile o código com os seguintes comandos:

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

Como mencionado anteriormente, o IoT Hub C SDK suporta fluxos de dispositivos apenas no lado do dispositivo. Para construir e executar a aplicação do lado do serviço que acompanha, siga as instruções numa das seguintes acelerações:

* [Comunicar a uma C# aplicação de dispositivo sintetante através de fluxos de dispositivos IoT Hub](./quickstart-device-streams-echo-csharp.md)

* [Comunicar a uma aplicação de dispositivo no Node.js através de fluxos de dispositivos IoT Hub](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hub IoT, registou um dispositivo, estabeleceu um fluxo de dispositivo entre uma aplicação C no dispositivo e outra aplicação no lado do serviço, e utilizou o fluxo para enviar dados de um lado para o outro entre as aplicações.

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
