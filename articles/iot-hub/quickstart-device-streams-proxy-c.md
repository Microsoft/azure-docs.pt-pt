---
title: Início rápido dos fluxos de dispositivos no Hub IoT do Azure para SSH e RDP
description: Neste guia de início rápido, você executa um aplicativo C de exemplo que atua como um proxy para habilitar cenários SSH e RDP em fluxos de dispositivo do Hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 03b0269b1a4500fd8ae26cd5e56f48427c5506aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429189"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Início rápido: habilitar SSH e RDP em um fluxo de dispositivo do Hub IoT usando um aplicativo de proxy C (versão prévia)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

O Hub IoT do Azure atualmente dá suporte a fluxos de dispositivo como um [recurso de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivo do Hub IOT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e de dispositivo se comuniquem de maneira segura e amigável ao firewall. Para obter uma visão geral da configuração, consulte [a página de exemplo de proxy local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Este guia de início rápido descreve a configuração do tráfego SSH (Secure Shell de túnel) (usando a porta 22) por meio de fluxos de dispositivo. A configuração do tráfego de protocolo RDP (RDP) é semelhante e requer uma alteração de configuração simples. Como os fluxos de dispositivo são independentes de aplicativo e de protocolo, você pode modificar este guia de início rápido para acomodar outros tipos de tráfego de aplicativos.

## <a name="how-it-works"></a>Como funciona

A figura a seguir ilustra como os programas de proxy local do dispositivo e do serviço permitem a conectividade de ponta a ponta entre os processos do cliente SSH e do daemon SSH. Durante a visualização pública, o SDK do C dá suporte a fluxos de dispositivo somente no lado do dispositivo. Como resultado, este guia de início rápido aborda instruções para executar apenas o aplicativo de proxy de dispositivo local. Para compilar e executar o aplicativo do lado do serviço que acompanha, siga as instruções em um dos seguintes guias de início rápido:

* [SSH/RDP em fluxos de dispositivo do Hub C# IOT usando proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP em fluxos de dispositivo do Hub IOT usando o proxy NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Configuração de proxy local](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. O proxy de serviço local conecta-se ao Hub IoT e inicia um fluxo de dispositivo para o dispositivo de destino.

2. O proxy de dispositivo local conclui o handshake de início de fluxo e estabelece um túnel de streaming de ponta a ponta por meio do ponto de extremidade de streaming do Hub IoT para o lado do serviço.

3. O proxy de dispositivo local conecta-se ao daemon SSH que está escutando na porta 22 do dispositivo. Essa configuração é configurável, conforme descrito na seção "executar o aplicativo de proxy local do dispositivo".

4. O proxy de serviço local aguarda novas conexões SSH de um usuário ouvindo em uma porta designada, que nesse caso é a porta 2222. Essa configuração é configurável, conforme descrito na seção "executar o aplicativo de proxy local do dispositivo". Quando o usuário se conecta via cliente SSH, o túnel permite que o tráfego do aplicativo SSH seja transferido entre os programas cliente SSH e servidor.

> [!NOTE]
> O tráfego SSH enviado por um fluxo de dispositivo é encapsulado por meio do ponto de extremidade de streaming do Hub IoT em vez de ser enviado diretamente entre o serviço e o dispositivo. Para obter mais informações, consulte os [benefícios de usar fluxos de dispositivo do Hub IOT](iot-hub-device-streams-overview.md#benefits). Além disso, a figura ilustra o daemon SSH que está em execução no mesmo dispositivo (ou computador) que o proxy local do dispositivo. Neste guia de início rápido, fornecer o endereço IP do daemon SSH permite que o proxy de dispositivo-local e o daemon sejam executados em computadores diferentes também.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, há suporte para a visualização de fluxos de dispositivo apenas para os hubs IoT criados nas seguintes regiões:

  * E.U.A. Central
  * EUA Central EUAP
  * Europa do Norte
  * Ásia Sudeste

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) com o [desenvolvimento de C++ desktop com](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) a carga de trabalho habilitada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).

* Execute o comando a seguir para adicionar a extensão de IoT do Azure para CLI do Azure à sua instância do Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT ao CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para este guia de início rápido, você usa o [SDK do dispositivo IOT do Azure para C](iot-hub-device-sdk-c-intro.md). Você prepara um ambiente de desenvolvimento usado para clonar e compilar o [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) do github. O SDK no GitHub inclui o código de exemplo usado neste guia de início rápido.

1. Baixe o [sistema de Build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e o *desenvolvimento de desktop com C++*  carga de trabalho) estejam instalados em seu computador, *antes* de iniciar a instalação do cmake. Depois que os pré-requisitos estiverem em vigor e o download for verificado, você poderá instalar o sistema de compilação CMake.

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

   * No Windows, execute os seguintes comandos no Prompt de Comando do Desenvolvedor para Visual Studio 2015 ou 2017. Uma solução do Visual Studio para o dispositivo simulado será gerada no diretório *CMake* .

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

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta seção, você usará Azure Cloud Shell com a [extensão de IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registrar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.
   > * Para o nome do dispositivo que você está registrando, é recomendável usar *MyDevice* , conforme mostrado. Se você escolher um nome diferente para seu dispositivo, use esse nome em todo este artigo e atualize o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de conexão do dispositivo* que você acabou de registrar, execute os seguintes comandos no Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observe a cadeia de conexão do dispositivo retornado para uso posterior neste guia de início rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

Nesta seção, você estabelece um fluxo de ponta a ponta para encapsular o tráfego SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar o aplicativo de proxy de dispositivo local

1. Edite o arquivo de origem **iothub_client_c2d_streaming_proxy_sample. c** na pasta `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`e forneça a cadeia de conexão do dispositivo, o IP/nome do host do dispositivo de destino e a porta SSH 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Compile o exemplo:

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

1. Execute o programa compilado no dispositivo:

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

### <a name="run-the-service-local-proxy-application"></a>Executar o aplicativo de proxy local de serviço

Conforme discutido na seção "como funciona", estabelecer um fluxo de ponta a ponta para encapsular o tráfego SSH requer um proxy local em cada extremidade (nos lados do serviço e do dispositivo). Durante a visualização pública, o SDK do Hub IoT C dá suporte a fluxos de dispositivo somente no lado do dispositivo. Para compilar e executar o proxy de serviço local, siga as instruções em um dos seguintes guias de início rápido:

   * [SSH/RDP em fluxos de dispositivo do Hub C# IOT usando aplicativos de proxy](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP em fluxos de dispositivo do Hub IoT usando aplicativos de proxy do node. js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Estabelecer uma sessão SSH

Depois que os proxies locais do dispositivo e do serviço estiverem em execução, use o programa cliente SSH e conecte-se ao proxy local do serviço na porta 2222 (em vez do daemon SSH diretamente).

```cmd/sh
ssh {username}@localhost -p 2222
```

Neste ponto, a janela de entrada SSH solicita que você insira suas credenciais.

A imagem a seguir mostra a saída do console no proxy local do dispositivo, que se conecta ao daemon SSH em `IP_address:22`:

![Saída do proxy local do dispositivo](./media/quickstart-device-streams-proxy-c/device-console-output.png)

A imagem a seguir mostra a saída do console do programa cliente SSH. O cliente SSH se comunica com o daemon SSH conectando-se à porta 22, na qual o proxy de serviço local está escutando:

![Saída do cliente SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você configura um hub IoT, registrou um dispositivo, implantou um dispositivo e um programa de proxy de serviço local para estabelecer um fluxo de dispositivo por meio do Hub IoT e usou os proxies para encapsular o tráfego SSH.

Para saber mais sobre fluxos de dispositivo, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivo](./iot-hub-device-streams-overview.md)
