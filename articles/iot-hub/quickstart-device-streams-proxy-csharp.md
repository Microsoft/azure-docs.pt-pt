---
title: IoT Hub dispositivo fluxos C início rápido do Azure para SSH/RDP (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, irá executar dois exemplos C# aplicações que ativar cenários SSH/RDP através de um fluxo de dispositivo do IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: ffd5f4baf3bbd2b7f0fe90272f896e438a30a35f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079235"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-applications-preview"></a>Início rápido: SSH/RDP sobre dispositivos no IoT Hub transmite em fluxo através de C# aplicações de proxy (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Atualmente, o IoT Hub do Microsoft Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Este guia de início rápido envolve dois C# programas que permitem o tráfego de aplicativo de cliente/servidor (como RDP e SSH) para ser enviado por um fluxo de dispositivo estabelecido através do IoT Hub. Ver [aqui](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) para uma descrição geral da configuração.

Descrevemos primeiro a configuração de SSH (utilizar a porta 22). Descrevemos, em seguida, como modificar a porta da configuração de RDP. Uma vez que os fluxos de dispositivo são o aplicativo e independente de protocolo, o mesmo exemplo pode ser modificado para acomodar outros tipos de tráfego de aplicativo. Isso normalmente envolve somente a alteração da porta de comunicação aquele usado pelo aplicativo pretendido.


## <a name="how-it-works"></a>Como funciona?

A figura abaixo ilustra a configuração de como os programas de proxy local do dispositivo e do serviço neste exemplo irão ativar a conectividade de ponto a ponto entre o cliente SSH e o daemon de SSH. Aqui, partimos do princípio de que o daemon está em execução no mesmo dispositivo, como o proxy de dispositivo local.

![Texto alternativo](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "configuração de Local proxy")

1. Proxy de serviço local liga ao IoT hub e inicia um fluxo de dispositivo para o dispositivo de destino com o seu ID de dispositivo.

2. Proxy local do dispositivo concluir o handshake de inicialização de fluxo e estabelece um túnel de transmissão em fluxo ponto-a-ponto através de ponto final de transmissão em fluxo do IoT Hub para o lado do serviço.

3. Proxy de dispositivo local liga-se para o daemon SSH (SSHD) à escuta na porta 22 no dispositivo (esta porta é configurável, conforme descrito [abaixo](#run-the-device-local-proxy)).

4. Proxy de serviço local awaits em novas ligações de SSH do utilizador através da escuta numa porta designada, que neste caso é porta 2222 (isso também é configurável, conforme descrito [abaixo](#run-the-service-local-proxy)). Quando o usuário se conecta por meio de cliente SSH, o túnel permite o tráfego de aplicativo a serem trocados entre os programas de cliente e servidor SSH.

> [!NOTE]
> A ser enviado através do fluxo de tráfego SSH irá ser encapsulado por meio de ponto final de transmissão em fluxo do IoT Hub, em vez de que está a ser enviadas diretamente entre o serviço e dispositivo. Isso fornece [esses benefícios](./iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

A pré-visualização de fluxos de dispositivo é atualmente suportado apenas para os Hubs IoT criado nas seguintes regiões:

  - **E.U.A. central**
  - **E.U.A. central EUAP**

As duas aplicações de exemplo que executa neste guia de início rápido são escritas com C#. Precisa do SDK de .NET Core 2.1.0 ou posterior no seu computador de desenvolvimento.

Pode transferir o SDK de .NET Core para múltiplas plataformas em [.NET](https://www.microsoft.com/net/download/all).

Pode verificar qual a versão atual do C# no seu computador de desenvolvimento através do seguinte comando:

```
dotnet --version
```

Transfira o projeto C# de exemplo de https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP.


## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

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

3. Também tem do _cadeia de ligação de serviço_ do seu hub IoT para permitir que o aplicativo do lado do serviço para se ligar ao seu hub IoT e estabelecer um fluxo de dispositivo. O comando seguinte obtém este valor para o seu hub IoT:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anote o valor retornado, o que é semelhante a este:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`
    

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

### <a name="run-the-device-local-proxy"></a>Execute o proxy local do dispositivo

Navegue para `device-streams-proxy/device` na pasta do projeto descompactado. Terá das seguintes informações úteis:

| Nome do argumento | Valor do argumento |
|----------------|-----------------|
| `deviceConnectionString` | A cadeia de ligação do dispositivo que criou anteriormente. |
| `targetServiceHostName` | O endereço IP em que o servidor SSH escuta na (isso seria `localhost` se o mesmo IP onde proxy local com o dispositivo está em execução). |
| `targetServicePort` | A porta utilizada pelo seu protocolo de aplicação (por predefinição, esta seria a porta 22 para SSH).  |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

### <a name="run-the-service-local-proxy"></a>Execute o proxy de serviço local

Navegue para `device-streams-proxy/service` na pasta do projeto descompactado. Terá das seguintes informações úteis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `iotHubConnectionString` | A cadeia de ligação de serviço do IoT Hub. |
| `deviceId` | O identificador do dispositivo que criou anteriormente. |
| `localPortNumber` | Uma porta local onde o cliente SSH irá ligar ao. Vamos utilizar a porta 2222 neste exemplo, mas poderia modificar isso para outros números arbitrários. |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-ssh-client"></a>Executar o cliente SSH

Agora, use o seu programa de cliente SSH e ligue ao proxy de serviço local na porta 2222 (em vez do daemon diretamente de SSH). 

```
ssh <username>@localhost -p 2222
```

Neste ponto, será apresentada com o pedido de início de sessão SSH para introduzir as suas credenciais.

Console de saída no lado do serviço (o proxy local com o serviço de escuta na porta 2222):

![Texto alternativo](./media/quickstart-device-streams-proxy-csharp/service-console-output.png "saída de proxy de serviço local")

Consola de saída no proxy do dispositivo local que o liga para o daemon de SSH em `IP_address:22`:

![Texto alternativo](./media/quickstart-device-streams-proxy-csharp/device-console-output.png "saída de proxy local do dispositivo")

Saída do programa de cliente SSH da consola (cliente SSH comunica com o daemon de SSH ao ligar-se a porta 22, em que o proxy de serviço local está a escutar):

![Texto alternativo](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "saída do programa de cliente SSH")


## <a name="rdp-to-a-device-via-device-streams"></a>RDP para um dispositivo por meio de fluxos de dispositivo

A configuração para RDP é muito semelhante para encaminhar o SSH (descrito acima). Basicamente, precisamos de utilizar o IP de destino de RDP e a porta 3389 em vez disso e utilizar o cliente RDP (em vez de cliente SSH).

### <a name="run-the-device-local-proxy-rdp"></a>Execute o proxy local do dispositivo (RDP)

Navegue para `device-streams-proxy/device` na pasta do projeto descompactado. Terá das seguintes informações úteis:

| Nome do argumento | Valor do argumento |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de ligação do dispositivo que criou anteriormente. |
| `targetServiceHostName` | O nome de anfitrião ou o endereço IP em que o servidor RDP é executada (isso seria `localhost` se o mesmo IP onde proxy local com o dispositivo está em execução). |
| `targetServicePort` | A porta utilizada pelo seu protocolo de aplicação (por predefinição, esta seria a porta 3389 para RDP).  |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux/MacOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

### <a name="run-the-service-local-proxy-rdp"></a>Execute o proxy de serviço local (RDP)

Navegue para `device-streams-proxy/service` na pasta do projeto descompactado. Terá das seguintes informações úteis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `iotHubConnectionString` | A cadeia de ligação de serviço do IoT Hub. |
| `deviceId` | O identificador do dispositivo que criou anteriormente. |
| `localPortNumber` | Uma porta local onde o cliente SSH irá ligar ao. Vamos utilizar a porta 2222 neste exemplo, mas poderia modificar isso para outros números arbitrários. |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-rdp-client"></a>Executar o cliente RDP

Agora, use o seu programa de cliente RDP e estabelecer ligação ao proxy de serviço local na porta 2222 (que era uma porta disponível arbitrária que selecionou anteriormente).

![Texto alternativo](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.PNG "RDP estabelece ligação ao proxy de serviço local")

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, ter de configurar um hub IoT, registou um dispositivo, implementado um dispositivo - e um programa de proxy de serviço local para estabelecer um fluxo do dispositivo através do IoT Hub e, utilizado proxies para túnel SSH ou tráfego RDP. O mesmo paradigma capaz de acomodar outros protocolos de cliente/servidor (em que servidor é executada no dispositivo, por exemplo, o daemon de SSH).

Use os links abaixo para saber mais sobre fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
