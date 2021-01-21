---
title: Quickstart - Azure IoT Hub streams C quickstart para SSH e RDP
description: Neste quickstart, você executou uma aplicação de amostra C que funciona como um proxy para permitir cenários SSH e RDP sobre fluxos de dispositivos IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 2305a87b91160b5de90f4cbfbc9418adc50bb92a
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624410"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Quickstart: Ative o SSH e o RDP sobre um fluxo de dispositivo ioT Hub utilizando uma aplicação de procuração C (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

O Azure IoT Hub suporta atualmente os fluxos de dispositivos como [uma funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[As correntes de dispositivos IoT Hub](./iot-hub-device-streams-overview.md) permitem que as aplicações de serviço e dispositivos se comuniquem de forma segura e amiga da firewall. Para obter uma visão geral da configuração, consulte [a página local de amostra de procuração.](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

Este quickstart descreve a configuração para fazer túneis de tráfego Secure Shell (SSH) (utilizando a porta 22) através de correntes de dispositivo. A configuração para o tráfego de Protocolo de Ambiente de Trabalho Remoto (RDP) é semelhante e requer uma simples alteração de configuração. Como os fluxos de dispositivos são aplicações e protocolo-agnóstico, pode modificar este quickstart para acomodar outros tipos de tráfego de aplicações.

## <a name="prerequisites"></a>Pré-requisitos

* A pré-visualização dos fluxos de dispositivos é atualmente suportada apenas para centros IoT que são criados nas seguintes regiões:

  * E.U.A. Central
  * EUA Central EUAP
  * Europa do Norte
  * Sudeste Asiático

* Instale [o Visual Studio 2019](https://www.visualstudio.com/vs/) com o desenvolvimento do Desktop com a carga de trabalho [C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ativada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Como funciona

A figura a seguir ilustra como os programas de procuração local e de dispositivo permitem a conectividade de ponta a ponta entre os processos SSH e daemon SSH. Durante a pré-visualização pública, o C SDK suporta apenas os fluxos de dispositivos no lado do dispositivo. Como resultado, este quickstart cobre instruções para executar apenas a aplicação de procuração local do dispositivo. Para construir e executar a aplicação do lado do serviço que o acompanha, siga as instruções numa das seguintes quickstarts:

* [SSH/RDP sobre fluxos de dispositivos IoT Hub usando proxy C#](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP sobre fluxos de dispositivos IoT Hub usando proxy NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Configuração de procuração local](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. O proxy local de serviço liga-se ao hub IoT e inicia uma corrente de dispositivo para o dispositivo alvo.

2. O dispositivo-procuração local completa o aperto de mão de iniciação do fluxo e estabelece um túnel de streaming de ponta a ponta através do ponto final de streaming do hub IoT para o lado de serviço.

3. O dispositivo-procuração local liga-se ao daemon SSH que está a ouvir na porta 22 do dispositivo. Esta definição é configurável, tal como descrito na secção "Executar a aplicação proxy local do dispositivo".

4. O representante local de serviço aguarda novas ligações SSH de um utilizador ouvindo numa porta designada, que neste caso é a porta 2222. Esta definição é configurável, tal como descrito na secção "Executar a aplicação proxy local do dispositivo". Quando o utilizador se conecta através do cliente SSH, o túnel permite que o tráfego de aplicações SSH seja transferido entre os programas do cliente SSH e do servidor.

> [!NOTE]
> O tráfego SSH que é enviado através de um fluxo de dispositivo é escavado através do ponto de streaming do hub IoT em vez de ser enviado diretamente entre o serviço e o dispositivo. Para obter mais informações, consulte os [benefícios da utilização de fluxos de dispositivos Iot Hub](iot-hub-device-streams-overview.md#benefits). Além disso, a figura ilustra o daemon SSH que está a funcionar no mesmo dispositivo (ou máquina) que o dispositivo-procuração local. Neste arranque rápido, o fornecimento do endereço IP do daemon SSH permite que o dispositivo-procuração local e o daemon também funcionam em diferentes máquinas.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para este arranque rápido, utilize o [dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md). Você prepara um ambiente de desenvolvimento usado para clonar e construir o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) de GitHub. O SDK no GitHub inclui o código de amostra que é usado neste arranque rápido.

1. Descarregue o [sistema de construção CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e o desenvolvimento do Desktop com carga de trabalho *C++* sejam instalados na sua máquina, *antes* de iniciar a instalação CMake. Depois de os pré-requisitos estarem em vigor e o download ser verificado, pode instalar o sistema de construção CMake.

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

   * No Windows, execute os seguintes comandos no Developer Command Prompt para Visual Studio 2015 ou 2017. Uma solução visual studio para o dispositivo simulado será gerada no *diretório cmake.*

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

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utilize a Azure Cloud Shell com a [extensão IoT](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest&preserve-view=true) para registar um dispositivo simulado.

1. Para criar a identidade do dispositivo, executar o seguinte comando em Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.
   > * Para o nome do dispositivo que está a registar, é aconselhável utilizar *o MyDevice* como mostrado. Se escolher um nome diferente para o seu dispositivo, use esse nome ao longo deste artigo e atualize o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de ligação* do dispositivo para o dispositivo que acabou de registar, execute os seguintes comandos em Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Note a cadeia de ligação do dispositivo devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo através de fluxos de dispositivos

Nesta secção, estabelece-se um fluxo de ponta a ponta para o tráfego do túnel SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de procuração local do dispositivo

1. Editar o ficheiro de origem **iothub_client_c2d_streaming_proxy_sample.c** na pasta , e fornecer a `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` cadeia de ligação do dispositivo, o dispositivo alvo IP/nome de anfitrião e a porta SSH 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Compilar a amostra:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Executar o programa compilado no dispositivo:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Executar a aplicação de procuração local de serviço

Como discutido na secção "Como funciona", o estabelecimento de um fluxo de ponta a ponta para o tráfego de SSH no túnel requer um representante local em cada extremidade (tanto no serviço como nos lados do dispositivo). Durante a pré-visualização pública, o IoT Hub C SDK suporta apenas os fluxos de dispositivos no lado do dispositivo. Para construir e executar o representante local de serviço, siga as instruções num dos seguintes quickstarts:

   * [Streams de dispositivo SSH/RDP sobre ioT Hub usando aplicações de procuração C#](./quickstart-device-streams-proxy-csharp.md)
   * [Streams de dispositivoS SSH/RDP sobre ioT Hub utilizando aplicações de procuração Node.js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Estabelecer uma sessão de SSH

Depois de ambos os proxies de dispositivo e serviço-local estarem em execução, use o seu programa de cliente SSH e ligue-se ao representante local de serviço na porta 2222 (em vez do daemon SSH diretamente).

```cmd/sh
ssh {username}@localhost -p 2222
```

Neste ponto, a janela de entrada SSH pede-lhe que introduza as suas credenciais.

A imagem a seguir mostra a saída da consola no dispositivo-procuração local, que se conecta ao daemon SSH `IP_address:22` em:

![Saída de procuração local do dispositivo](./media/quickstart-device-streams-proxy-c/device-console-output.png)

A imagem a seguir mostra a saída da consola do programa de clienteS SSH. O cliente SSH comunica ao daemon SSH ligando-se à porta 22, que o representante local de serviço está a ouvir:

![Saída do cliente SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você montou um hub IoT, registou um dispositivo, implantou um dispositivo e um programa de procuração local de serviço para estabelecer um fluxo de dispositivo através do IoT Hub, e usou os proxies para o túnel de tráfego SSH.

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
