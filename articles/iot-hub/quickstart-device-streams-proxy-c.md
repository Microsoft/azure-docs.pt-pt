---
title: IoT Hub dispositivo fluxos C início rápido do Azure para SSH e RDP (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, irá executar um aplicativo de exemplo C que age como um proxy para ativar cenários RDP e SSH ao longo do IoT Hub fluxos de dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: b958711c498f0826f2a48d92d4892eb43ec8d18a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446083"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Início rápido: Ativar o RDP e SSH ao longo de um fluxo de dispositivo do IoT Hub através de uma aplicação de proxy do C (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Atualmente, o IoT Hub do Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Para uma descrição geral da configuração, consulte [a página de exemplo de Proxy Local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Este início rápido descreve a configuração de túnel do tráfego de Secure Shell (SSH) (utilizar a porta 22) através de fluxos de dispositivo. A configuração para o tráfego de protocolo RDP (Remote Desktop) é semelhante e requer uma alteração de configuração simples. Como os fluxos de dispositivo são aplicações - e indiferente a protocolo, pode modificar este início rápido para acomodar outros tipos de tráfego de aplicativo.

## <a name="how-it-works"></a>Como funciona

A figura a seguir ilustra como os programas de proxy local do dispositivo e do serviço habilitar a conectividade de ponto a ponto entre o cliente SSH e processos de daemon SSH. Durante a pré-visualização pública, o SDK de C suporta fluxos de dispositivo do lado do dispositivo só. Como resultado, este Guia Rápido abrange as instruções para executar apenas a aplicação de proxy do dispositivo local. Deve executar um dos inícios rápidos do lado do serviço:

* [SSH/RDP sobre dispositivos no IoT Hub transmite em fluxo através de C# proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP sobre fluxos de dispositivo do IoT Hub com o proxy de NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Configuração de local proxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. O proxy de serviço local se liga ao IoT hub e inicia um fluxo de dispositivo para o dispositivo de destino.

2. O proxy local do dispositivo concluir o handshake de inicialização de fluxo e estabelece um túnel de transmissão em fluxo ponto-a-ponto através de ponto final de transmissão em fluxo do hub IoT para o lado do serviço.

3. O proxy local do dispositivo liga-se para o daemon de SSH que está à escuta na porta 22 no dispositivo. Esta definição é configurável, conforme descrito na secção "Executar a aplicação do proxy de dispositivo-local".

4. O proxy de serviço local aguarda novas ligações SSH de um utilizador através da escuta numa porta designada, que neste caso é porta 2222. Esta definição é configurável, conforme descrito na secção "Executar a aplicação do proxy de dispositivo-local". Quando o usuário se conecta por meio de cliente SSH, o túnel permite o tráfego de aplicativo de SSH a serem transferidos entre os programas de cliente e servidor SSH.

> [!NOTE]
> Tráfego SSH que é enviado através de um fluxo de dispositivo está em túnel através de ponto final de transmissão em fluxo do hub IoT em vez de enviadas diretamente entre o serviço e dispositivo. Para obter mais informações, consulte a [vantagens da utilização de fluxos de dispositivo do Iot Hub](iot-hub-device-streams-overview.md#benefits). Além disso, a figura ilustra o daemon de SSH está em execução no mesmo dispositivo (ou na máquina) como o proxy de dispositivo local. Neste início rápido, fornecendo o endereço IP de daemon SSH permite que o proxy de dispositivo local e o daemon para ser executado em diferentes computadores também.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* A pré-visualização de fluxos de dispositivo é atualmente suportada apenas para os hubs IoT que são criados nas seguintes regiões:

  * EUA Central
  * E.U.A. central EUAP

* Instale [Visual Studio 2019](https://www.visualstudio.com/vs/) com o [desenvolvimento com C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) carga de trabalho ativada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).

* Execute o seguinte comando para adicionar a extensão de IoT do Azure para a CLI do Azure à sua instância do Cloud Shell. A extensão de IOT adiciona o IoT Hub, o IoT Edge e o serviço aprovisionamento de dispositivos IoT (DPS)-comandos específicos para a CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, utilize o [Azure IoT device SDK para C](iot-hub-device-sdk-c-intro.md). Preparar-se um ambiente de desenvolvimento usado para clonar e criar os [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) do GitHub. O SDK no GitHub inclui o código de exemplo utilizada neste início rápido.

1. Transfira o [sistema de compilação CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e o *desenvolvimento com C++*  carga de trabalho) estão instalados no seu computador *antes* iniciar a instalação de CMake. Depois dos pré-requisitos estão assegurados e o download é verificado, pode instalar o sistema de compilação CMake.

1. Abra uma linha de comandos ou a shell do Git Bash. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Deve esperar que esta operação demore alguns minutos.

1. Criar uma *cmake* subdiretório no diretório de raiz do repositório Git, conforme mostrado no comando seguinte e navegue para essa pasta.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos a partir do *cmake* directory para criar uma versão do SDK específica para sua plataforma de cliente de desenvolvimento.

   * No Linux:

      ```bash
      cmake ..
      make -j
      ```

   * No Windows, execute os seguintes comandos na linha de comandos do programador para o Visual Studio 2015 ou 2017. Uma solução do Visual Studio para o dispositivo simulado será gerada na *cmake* diretório.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, vai utilizar Azure Cloud Shell com o [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registar um dispositivo simulado.

1. Para criar a identidade de dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua a *YourIoTHubName* espaço reservado com o nome que escolher para o seu hub IoT.
   > * Uso *MyDevice*, conforme mostrado. É o nome fornecido para o dispositivo registado. Se escolher um nome diferente para o seu dispositivo, utilize esse nome ao longo deste artigo e atualizar o nome do dispositivo em aplicativos de exemplo, antes de gerá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Para obter o *cadeia de ligação do dispositivo* para o dispositivo que acabou de registar, execute os seguintes comandos no Cloud Shell:

   > [!NOTE]
   > Substitua a *YourIoTHubName* espaço reservado com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Tenha em atenção a cadeia de ligação do dispositivo para utilizar mais tarde neste início rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

Nesta secção, estabelecer um fluxo de ponto-a-ponto para encaminhar o tráfego SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de proxy local do dispositivo

1. Edite o ficheiro de origem *iothub_client_c2d_streaming_sample.c* na pasta *iothub_client/exemplos/iothub_client_c2d_streaming_sample*e fornecer a cadeia de ligação do dispositivo, de destino nome de anfitrião/IP do dispositivo e a porta SSH 22:

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
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

### <a name="run-the-service-local-proxy-application"></a>Executar a aplicação do proxy de serviço local

Como discutido na seção "Como funciona", o estabelecimento de um fluxo de ponto-a-ponto para encaminhar o tráfego SSH requer um proxy local em cada extremidade (sobre o serviço e os lados de dispositivo). Durante a pré-visualização pública, o SDK de C do Hub IoT suporta fluxos de dispositivo do lado do dispositivo só. Para criar e executar o proxy de serviço local, siga as instruções dos inícios rápidos:

   * [SSH/RDP sobre dispositivos no IoT Hub transmite em fluxo através de C# aplicações de proxy](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP sobre fluxos de dispositivo do IoT Hub com aplicações de proxy do node. js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Estabelecer uma sessão SSH

Após os dois proxies o local do dispositivo e do serviço estão em execução, use o seu programa de cliente SSH e ligue-se para o proxy de serviço local na porta 2222 (em vez do daemon diretamente de SSH).

```cmd/sh
ssh <username>@localhost -p 2222
```

Neste momento, a janela de início de sessão SSH pede-lhe para introduzir as suas credenciais.

A imagem seguinte mostra o resultado da consola no proxy local do dispositivo, que liga-se para o daemon de SSH em `IP_address:22`:

![Saída de proxy local do dispositivo](./media/quickstart-device-streams-proxy-c/device-console-output.png)

A imagem seguinte mostra o resultado da consola do programa de cliente SSH. O cliente SSH comunica com o daemon SSH ao ligar-se a porta 22, que está a escutar o proxy de serviço local:

![Saída do cliente SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, configurar um hub IoT, registou um dispositivo, implementou um dispositivo - e um programa de proxy de serviço local para estabelecer um fluxo do dispositivo através do IoT Hub e utilizar os proxies para encaminhar o tráfego SSH.

Para saber mais sobre fluxos de dispositivo, veja:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
