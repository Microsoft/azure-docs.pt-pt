---
title: IoT Hub dispositivo fluxos C início rápido do Azure para SSH/RDP (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, irá executar um exemplo de aplicação de C que age como um proxy para permitir cenários SSH/RDP sobre fluxos de dispositivo do IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 59a84190386b554716472b4cb46c94030a66a4cb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077110"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Início rápido: SSH/RDP sobre fluxos de dispositivo do IoT Hub com C do proxy de aplicações (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Atualmente, o IoT Hub do Microsoft Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Ver [esta página](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) para uma descrição geral da configuração.

Este documento descreve a configuração de túnel tráfego SSH (utilizar a porta 22) através de fluxos de dispositivo. A configuração para o tráfego RDP é semelhante e requer uma alteração de configuração simples. Uma vez que os fluxos de dispositivo são o aplicativo e independente de protocolo, o guia de introdução presente pode ser modificado (ao alterar as portas de comunicação) para acomodar outros tipos de tráfego de aplicativo.

## <a name="how-it-works"></a>Como funciona?
A figura abaixo ilustra a configuração de como os programas de proxy local do dispositivo e do serviço irão ativar a conectividade de ponto a ponto entre o cliente SSH e processos de daemon SSH. Durante a pré-visualização pública, o SDK de C só suporta fluxos de dispositivo do lado do dispositivo. Como resultado, este início rápido abrange apenas as instruções para executar a aplicação de proxy do dispositivo local. Deve executar uma aplicação de proxy de serviço local que acompanha este artigo que está disponível no [ C# início rápido](./quickstart-device-streams-proxy-csharp.md) ou [guia de introdução do node. js](./quickstart-device-streams-proxy-nodejs.md) guias.

![Texto alternativo](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "configuração de Local proxy")

1. Proxy de serviço local liga ao IoT hub e inicia um fluxo de dispositivo para o dispositivo de destino.

2. Proxy local do dispositivo concluir o handshake de inicialização de fluxo e estabelece um túnel de transmissão em fluxo ponto-a-ponto através de ponto final de transmissão em fluxo do IoT Hub para o lado do serviço.

3. Proxy de dispositivo local liga-se para o daemon SSH (SSHD) à escuta na porta 22 no dispositivo (isto é configurável, conforme descrito [abaixo](#run-the device-local-proxy-application)).

4. Proxy de serviço local awaits em novas ligações de SSH do utilizador através da escuta numa porta designada, que neste caso é porta 2222 (isso também é configurável, conforme descrito [abaixo](#run-the-device-local-proxy-application)). Quando o usuário se conecta por meio de cliente SSH, o túnel permite o tráfego de aplicativo de SSH a serem transferidos entre os programas de cliente e servidor SSH.

> [!NOTE]
> Tráfego SSH que está a ser enviado através de um fluxo de dispositivo serão serem em túnel através de ponto final de transmissão em fluxo do IoT Hub, em vez de que está a ser enviadas diretamente entre o serviço e dispositivo. Isso fornece [esses benefícios](./iot-hub-device-streams-overview.md#benefits). Além disso, a figura ilustra o daemon SSH está em execução no mesmo dispositivo (ou na máquina) como o proxy de dispositivo local. Neste início rápido, fornecendo o endereço IP de daemon SSH permite que o proxy de dispositivo local e o daemon para ser executado em computadores diferentes, bem.

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

1. Baixe a versão 3.13.4 dos [sistema de compilação CMake](https://cmake.org/download/). Verifique o binário transferido com o valor de hash criptográfico correspondente. O exemplo seguinte utilizado o Windows PowerShell para verificar o hash criptográfico para a versão 3.13.4 de x64 distribuição de MSI:

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

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Ambiente de trabalho de desenvolvimento com C++") estão instalados no seu computador **antes de** a partir do `CMake` instalação. Depois dos pré-requisitos estão assegurados e o download é verificado, instale o sistema de compilação CMake.

2. Abra uma linha de comandos ou a shell do Git Bash. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    Atualmente, o tamanho deste repositório é de cerca de 220 MB. Esta operação deve demorar vários minutos a ser concluída.

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

2. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que é semelhante ao seguinte exemplo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de proxy local do dispositivo

1. Edite o ficheiro de origem `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` e fornecer a cadeia de ligação do dispositivo, dispositivo de destino IP/nome de anfitrião e a porta SSH 22:

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. Compile o exemplo:

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

3. Execute o programa compilado no dispositivo:

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

Como discutido [anteriormente](#how-it-works), estabelecimento de um fluxo de ponto-a-ponto para encaminhar o tráfego SSH requer um proxy local em cada extremidade (tanto no serviço e o dispositivo). Durante a pré-visualização pública, o SDK de C do Hub IoT suporta apenas fluxos de dispositivo do lado do dispositivo. Para criar e executar o proxy de serviço local, siga os passos disponíveis no [ C# início rápido](./quickstart-device-streams-proxy-csharp.md) ou o [guia de introdução do node. js](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>Estabelecer uma sessão SSH

Após os dois proxies o local do dispositivo e do serviço estão em execução, use o seu programa de cliente SSH e ligue-se para o proxy de serviço local na porta 2222 (em vez do daemon diretamente de SSH).

```cmd/sh
ssh <username>@localhost -p 2222
```

Neste ponto, será apresentada com o pedido de início de sessão SSH para introduzir as suas credenciais.

Consola de saída no proxy do dispositivo local que o liga para o daemon de SSH em `IP_address:22`: ![Texto alternativo](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "saída de proxy local do dispositivo")

Saída do programa de cliente SSH da consola (cliente SSH comunica com o daemon de SSH conectando-se a porta 22, que o proxy de serviço local está a escutar): ![Texto alternativo](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "saída do cliente SSH")

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, ter configurar um hub IoT, registou um dispositivo, implementado um dispositivo - e um programa de proxy de serviço local para estabelecer um fluxo do dispositivo através do IoT Hub e usado os proxies para encaminhar o tráfego SSH.

Use os links abaixo para saber mais sobre fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
