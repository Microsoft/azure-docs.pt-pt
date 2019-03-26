---
title: IoT Hub dispositivo fluxos C início rápido do Azure (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, irá executar um aplicativo do lado do serviço de C que comunica com um dispositivo de IoT através de um fluxo de dispositivo.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 026e072d6e5ac65d957b272300898de3dc9099a9
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438279"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início rápido: Comunicar com um aplicativo de dispositivo em C, por meio de fluxos de dispositivo do IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o IoT Hub do Microsoft Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Durante a pré-visualização pública, o SDK de C só suporta fluxos de dispositivo do lado do dispositivo. Como resultado, este início rápido abrange apenas as instruções para executar a aplicação do lado do dispositivo. Deve executar uma aplicação de lado do serviço que acompanha este artigo, o que está disponível na [ C# início rápido](./quickstart-device-streams-echo-csharp.md) ou [guia de introdução do node. js](./quickstart-device-streams-echo-nodejs.md).

O aplicativo de C do lado do dispositivo neste guia de introdução tem a seguinte funcionalidade:

* Estabelece um fluxo de dispositivo para um dispositivo de IoT.

* Recebe dados enviados do lado do serviço e eco-lo novamente.

O código demonstra o processo de inicialização de um fluxo de dispositivo, bem como usá-lo para enviar e receber dados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* A pré-visualização de fluxos de dispositivo é atualmente suportado apenas para os Hubs IoT criado nas seguintes regiões:

  * **E.U.A. central**
  * **E.U.A. central EUAP**

* Instale o [Visual Studio 2017](https://www.visualstudio.com/vs/) com a carga de trabalho [“Desenvolvimento do ambiente de trabalho em C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ativada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, vai utilizar o [Azure IoT device SDK para C](iot-hub-device-sdk-c-intro.md). Irá preparar um ambiente de desenvolvimento usado para clonar e criar os [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) do GitHub. O SDK no GitHub inclui o código de exemplo utilizado neste início rápido. 

1. Transfira o [sistema de compilação CMake](https://cmake.org/download/). Verifique se o binário transferido com o valor de hash criptográfico que corresponde à versão que baixar. Os valores de hash criptográfico também estão localizados da ligação de transferência de CMake já fornecida.

    O exemplo seguinte utilizado o Windows PowerShell para verificar o hash criptográfico para a versão 3.13.4 de x64 distribuição de MSI:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.13.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "64AC7DD5411B48C2717E15738B83EA0D4347CD51B940487DFF7F99A870656C09"
    True
    ```

    Os seguintes valores de hash para a versão 3.13.4 foram listados no site de CMake no momento da redação deste artigo:

    ```
    563a39e0a7c7368f81bfa1c3aff8b590a0617cdfe51177ddc808f66cc0866c76  cmake-3.13.4-Linux-x86_64.tar.gz
    7c37235ece6ce85aab2ce169106e0e729504ad64707d56e4dbfc982cb4263847  cmake-3.13.4-win32-x86.msi
    64ac7dd5411b48c2717e15738b83ea0d4347cd51b940487dff7f99a870656c09  cmake-3.13.4-win64-x64.msi
    ```

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Abra uma linha de comandos ou a shell do Git Bash. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Atualmente, o tamanho deste repositório é de cerca de 220 MB.

3. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. 

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Execute os seguintes comandos a partir do `cmake` diretório para criar uma versão do SDK específica da sua plataforma de cliente de desenvolvimento.

   * No Linux:

      ```bash
      cmake ..
      make -j
      ```

   * No Windows, execute os seguintes comandos na linha de comandos do programador para o Visual Studio 2015 ou 2017. Será gerada uma solução do Visual Studio para o dispositivo simulado no diretório `cmake`.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, vai utilizar o Azure Cloud Shell com a [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo. 

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

   **MyDevice**: Este é o nome fornecido para o dispositivo registado. Utilize MyDevice, conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que é semelhante ao seguinte exemplo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o serviço por meio de fluxos de dispositivo e dispositivo

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Para executar a aplicação do lado do dispositivo, terá de efetuar os seguintes passos:

1. Forneça as suas credenciais do dispositivo, editando o ficheiro de origem `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` e fornecer a cadeia de ligação do dispositivo.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. Compile o código da seguinte forma:

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

3. Execute o programa compilado:

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

Como mencionado anteriormente, o SDK de C do IoT Hub só suporta fluxos de dispositivo do lado do dispositivo. Para criar e executar a aplicação do lado do serviço, siga os passos disponíveis no [ C# início rápido](./quickstart-device-streams-echo-csharp.md) ou o [guia de introdução do node. js](./quickstart-device-streams-echo-nodejs.md).

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, ter configurar um hub IoT, registou um dispositivo, estabelecer um fluxo de dispositivo entre um aplicativo de C no dispositivo e outra aplicação no lado do serviço e utilizado o fluxo para enviar dados entre os aplicativos.

Use os links abaixo para saber mais sobre fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)