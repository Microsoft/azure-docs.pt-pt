---
title: Dispositivo Azure IoT Hub transmite c arranque rápido para SSH e RDP
description: Neste arranque rápido, executa uma aplicação de amostra C que funciona como um proxy para permitir cenários de SSH e RDP sobre os fluxos de dispositivos IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: f47c2c30f71d6510ee66bb19bdd936e0780e242c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461873"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Quickstart: Ative o SSH e o RDP sobre um fluxo de dispositivos IoT Hub utilizando uma aplicação de procuração C (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

O Azure IoT Hub suporta atualmente os streams do dispositivo como recurso de [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os streams de [dispositivos IoT Hub](./iot-hub-device-streams-overview.md) permitem que as aplicações de serviço e dispositivo saem de forma segura e amiga da firewall. Para obter uma visão geral da configuração, consulte a página da amostra de [procuração local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Este arranque rápido descreve a configuração para o túnel secure Shell (SSH) tráfego (utilizando a porta 22) através de correntes de dispositivos. A configuração do tráfego do Protocolo de Ambiente de Trabalho Remoto (RDP) é semelhante e requer uma simples alteração de configuração. Como os fluxos de dispositivos são aplicação e protocolo-agnóstico, pode modificar este quickstart para acomodar outros tipos de tráfego de aplicações.

## <a name="how-it-works"></a>Como funciona

A figura que se segue ilustra como os programas de procuração local de dispositivos e serviços permitem a conectividade de ponta a ponta entre o cliente SSH e os processos de daemon SSH. Durante a pré-visualização pública, o C SDK suporta fluxos de dispositivos apenas no lado do dispositivo. Como resultado, este quickstart cobre instruções para executar apenas a aplicação de procuração local do dispositivo. Para construir e executar a aplicação do lado do serviço que acompanha, siga as instruções numa das seguintes acelerações:

* [SSH/RDP sobre fluxos de dispositivos IoT Hub usando C# proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP sobre fluxos de dispositivos IoT Hub utilizando proxy NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Configuração local de procuração](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. O representante local de serviço liga-se ao hub IoT e inicia um fluxo de dispositivo soro para o dispositivo alvo.

2. O representante local do dispositivo completa o aperto de mão de iniciação do fluxo e estabelece um túnel de streaming de ponta a ponta através do ponto final de streaming do centro IoT para o lado do serviço.

3. O representante local do dispositivo liga-se ao daemon SSH que está a ouvir na porta 22 do dispositivo. Esta definição é configurável, conforme descrito na secção "Executar a aplicação proxy local do dispositivo".

4. O representante local do serviço aguarda novas ligações SSH de um utilizador ouvindo uma porta designada, que neste caso é a porta 2222. Esta definição é configurável, conforme descrito na secção "Executar a aplicação proxy local do dispositivo". Quando o utilizador se conecta através do cliente SSH, o túnel permite que o tráfego de aplicações SSH seja transferido entre o cliente SSH e os programas de servidor.

> [!NOTE]
> O tráfego de SSH que é enviado sobre um fluxo de dispositivoé túnel através do ponto final de streaming do hub IoT em vez de enviado diretamente entre o serviço e o dispositivo. Para mais informações, consulte os [benefícios da utilização de fluxos de dispositivos Iot Hub](iot-hub-device-streams-overview.md#benefits). Além disso, a figura ilustra o daemon SSH que está a funcionar no mesmo dispositivo (ou máquina) que o proxy local do dispositivo. Neste arranque rápido, o fornecimento do endereço IP da SSH permite que o proxy local do dispositivo e o daemon também funcionam em diferentes máquinas.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* A visualização dos fluxos de dispositivos é atualmente suportada apenas para centros IoT que são criados nas seguintes regiões:

  * E.U.A. Central
  * EUA Centrais EUA
  * Europa do Norte
  * Ásia Sudeste

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) com o desenvolvimento do [Ambiente de Trabalho com C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) carga de trabalho ativada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).

* Execute o seguinte comando para adicionar a extensão Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) específicos para o Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para este arranque rápido, utilize o [dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md). Prepara-se um ambiente de desenvolvimento usado para clonar e construir o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a partir do GitHub. O SDK no GitHub inclui o código de amostra que é usado neste arranque rápido.

1. Descarregue o [sistema de construção CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Estúdio Visual (Visual Studio e o desenvolvimento do *Ambiente de Trabalho com C++*  carga de trabalho) estejam instalados na sua máquina, *antes* de iniciar a instalação do CMake. Depois de os pré-requisitos estarem em vigor e o download ser verificado, pode instalar o sistema de construção CMake.

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

   * No Windows, execute os seguintes comandos no Comando de Desenvolvimento Prompt for Visual Studio 2015 ou 2017. Uma solução De Estúdio Visual para o dispositivo simulado será gerada no diretório *de cmake.*

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

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utiliza a Azure Cloud Shell com a [extensão IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando na Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.
   > * Para o nome do dispositivo que está a registar, é aconselhável utilizar o *MyDevice* como mostrado. Se escolher um nome diferente para o seu dispositivo, utilize esse nome ao longo deste artigo e atualize o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de ligação* do dispositivo para o dispositivo que acabou de registar, execute os seguintes comandos na Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Note a cadeia de ligação do dispositivo devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo através de fluxos de dispositivos

Nesta secção, estabelece-se um fluxo de ponta a ponta para o túnel de tráfego ssh.

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de procuração local de dispositivo

1. Editar o ficheiro fonte **iothub_client_c2d_streaming_proxy_sample.c** na pasta `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`, e fornecer a sua cadeia de ligação ao dispositivo, o nome ip/nome do dispositivo alvo e a porta SSH 22:

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

Tal como discutido na secção "Como funciona", o estabelecimento de um fluxo de ponta a ponta para o tráfego de SSH do túnel requer um proxy local em cada extremidade (tanto no lado do serviço como do dispositivo). Durante a pré-visualização pública, o IoT Hub C SDK suporta fluxos de dispositivos apenas no lado do dispositivo. Para construir e executar o representante local de serviço, siga as instruções numa das seguintes acelerações:

   * [Streams de dispositivos SSH/RDP C# sobre ioT Hub usando aplicações proxy](./quickstart-device-streams-proxy-csharp.md)
   * [Streams de dispositivos SSH/RDP sobre ioT Hub usando aplicações de proxy Node.js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Estabelecer uma sessão de SSH

Depois de ambos os proxies locais de dispositivo e serviço estarem em execução, use o seu programa de cliente SSH e ligue-se ao proxy local de serviço na porta 2222 (em vez do daemon SSH diretamente).

```cmd/sh
ssh {username}@localhost -p 2222
```

Neste ponto, a janela de entrada ssh leva-o a introduzir as suas credenciais.

A imagem seguinte mostra a saída da consola no proxy local do dispositivo, que se liga ao daemon SSH em `IP_address:22`:

![Saída de procuração local de dispositivo](./media/quickstart-device-streams-proxy-c/device-console-output.png)

A imagem seguinte mostra a saída da consola do programa de cliente SSH. O cliente SSH comunica com o daemon SSH ligando-se à porta 22, que o representante local de serviço está a ouvir:

![Saída de cliente SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, instalou um hub IoT, registou um dispositivo, implementou um dispositivo e um programa de procuração local de serviço para estabelecer um fluxo de dispositivo através do IoT Hub, e usou os proxies para fazer um túnel de tráfego ssh.

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
