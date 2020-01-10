---
title: Comunicar-se com o aplicativo de dispositivo em C com fluxos de dispositivo do Hub IoT do Azure
description: Neste guia de início rápido, você executa um aplicativo do lado do dispositivo C que se comunica com um dispositivo IoT por meio de um fluxo de dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 1d475c8e1f8dd332b60aef04242d2829feba93c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429211"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início rápido: comunicar-se com um aplicativo de dispositivo em C por meio de fluxos de dispositivo do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

O Hub IoT do Azure atualmente dá suporte a fluxos de dispositivo como um [recurso de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivo do Hub IOT](iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e de dispositivo se comuniquem de maneira segura e amigável ao firewall. Durante a visualização pública, o SDK do C dá suporte a fluxos de dispositivo somente no lado do dispositivo. Como resultado, este guia de início rápido aborda instruções para executar apenas o aplicativo do lado do dispositivo. Para executar um aplicativo do lado do serviço correspondente, consulte estes artigos:

* [Comunicar-se com aplicativos C# de dispositivo por meio de fluxos de dispositivo do Hub IOT](./quickstart-device-streams-echo-csharp.md)

* [Comunicar-se com aplicativos de dispositivo no node. js por meio de fluxos de dispositivo do Hub IoT](./quickstart-device-streams-echo-nodejs.md)

O aplicativo C do lado do dispositivo neste guia de início rápido tem a seguinte funcionalidade:

* Estabeleça um fluxo de dispositivo para um dispositivo IoT.

* Receber dados enviados do aplicativo do lado do serviço e ecoar novamente.

O código demonstra o processo de inicialização de um fluxo de dispositivo, bem como como usá-lo para enviar e receber dados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes pré-requisitos:

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) com o **desenvolvimento de C++ desktop com** a carga de trabalho habilitada.

* Instale a versão mais recente do [Git](https://git-scm.com/download/).

* Execute o comando a seguir para adicionar a extensão de IoT do Azure para CLI do Azure à sua instância do Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT ao CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

Atualmente, há suporte para a visualização de fluxos de dispositivo apenas para os hubs IoT criados nas seguintes regiões:

  * Centro dos E.U.A.
  * EUA Central EUAP
  * Europa do Norte
  * Sudeste Asiático

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para este guia de início rápido, você usa o [SDK do dispositivo IOT do Azure para C](iot-hub-device-sdk-c-intro.md). Você prepara um ambiente de desenvolvimento usado para clonar e compilar o [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) do github. O SDK no GitHub inclui o código de exemplo usado neste guia de início rápido.

   > [!NOTE]
   > Antes de iniciar este procedimento, certifique-se de que o Visual Studio esteja instalado com o **desenvolvimento de desktop com C++**  carga de trabalho.

1. Instale o [sistema de compilação CMake](https://cmake.org/download/) conforme descrito na página de download.

1. Abra uma linha de comandos ou a shell do Git Bash. Execute os seguintes comandos para clonar o repositório GitHub do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve levar alguns minutos.

1. Crie um subdiretório *CMake* no diretório raiz do repositório git e navegue até essa pasta. Execute os seguintes comandos no diretório *Azure-IOT-SDK-c* :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos do diretório *CMake* para criar uma versão do SDK específica para sua plataforma de cliente de desenvolvimento.

   * No Linux:

      ```bash
      cmake ..
      make -j
      ```

   * No Windows, abra um [prompt de comando do desenvolvedor para o Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Execute o comando para a sua versão do Visual Studio. Este guia de início rápido usa o Visual Studio 2019. Esses comandos criam uma solução do Visual Studio para o dispositivo simulado no diretório *CMake* .

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

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Você deve registrar um dispositivo com o Hub IoT antes que ele possa se conectar. Nesta seção, você usará Azure Cloud Shell com a [extensão de IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registrar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.
   > * Para o nome do dispositivo que você está registrando, é recomendável usar *MyDevice* , conforme mostrado. Se você escolher um nome diferente para seu dispositivo, use esse nome em todo este artigo e atualize o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de conexão do dispositivo* que você acabou de registrar, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observe a cadeia de conexão do dispositivo retornado para uso posterior neste guia de início rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicar entre o dispositivo e o serviço por meio de fluxos de dispositivo

Nesta seção, você executa o aplicativo do lado do dispositivo e o aplicativo do lado do serviço e se comunica entre os dois.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Para executar o aplicativo do lado do dispositivo, siga estas etapas:

1. Forneça suas credenciais de dispositivo editando o arquivo de origem **iothub_client_c2d_streaming_sample. c** na pasta `iothub_client/samples/iothub_client_c2d_streaming_sample` e adicionando a cadeia de conexão do dispositivo.

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

1. Execute o programa compilado:

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

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Conforme mencionado anteriormente, o SDK do Hub IoT C dá suporte a fluxos de dispositivo somente no lado do dispositivo. Para compilar e executar o aplicativo do lado do serviço que acompanha, siga as instruções em um dos seguintes guias de início rápido:

* [Comunicar-se com um aplicativo C# de dispositivo por meio de fluxos de dispositivo do Hub IOT](./quickstart-device-streams-echo-csharp.md)

* [Comunicar-se com um aplicativo de dispositivo no node. js por meio de fluxos de dispositivo do Hub IoT](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você configura um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivo entre um aplicativo C no dispositivo e outro aplicativo no lado do serviço e usou o fluxo para enviar dados entre os aplicativos.

Para saber mais sobre fluxos de dispositivo, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivo](./iot-hub-device-streams-overview.md)
