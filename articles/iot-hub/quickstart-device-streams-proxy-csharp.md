---
title: Fluxos de dispositivo do IoT Hub do Azure C# início rápido para SSH e RDP (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, executa duas de exemplo C# aplicações que ativar cenários RDP e SSH através de um fluxo de dispositivo do IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 914568ee4b669605807c8a0e386cd540145c9522
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446102"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Início rápido: Ativar o RDP e SSH ao longo de um fluxo de dispositivo do IoT Hub utilizando um C# proxy de aplicações (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Atualmente, o IoT Hub do Microsoft Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Este guia de início rápido envolve dois C# aplicações que permitam o tráfego de aplicativo de cliente-servidor (como o Secure Shell [SSH] e Remote Desktop Protocol [RDP] para ser enviado por um fluxo de dispositivo é estabelecido através de um hub IoT. Para uma descrição geral da configuração, consulte [exemplo de aplicação de Local proxy para SSH ou RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Este artigo descreve primeiro a configuração de SSH (utilizar a porta 22) e, em seguida, descreve como modificar a porta da configuração de RDP. Como os fluxos de dispositivo são aplicações - e indiferente a protocolo, o mesmo exemplo pode ser modificado para acomodar outros tipos de tráfego de aplicativo. Essa modificação normalmente envolve alterar apenas a porta de comunicação para o que é utilizada pela aplicação pretendida.

## <a name="how-it-works"></a>Como funciona

A figura a seguir ilustra como as aplicações de proxy local do dispositivo e de serviço local neste exemplo habilitar a conectividade de ponto a ponto entre o cliente SSH e processos de daemon SSH. Aqui, partimos do princípio de que o daemon está em execução no mesmo dispositivo, como a aplicação do proxy de dispositivo local.

![Configuração de aplicações de local proxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. A aplicação do proxy de serviço local se liga ao IoT hub e inicia um fluxo de dispositivo para o dispositivo de destino.

1. O local do dispositivo proxy de aplicações concluir o handshake de inicialização de fluxo e estabelece um túnel de transmissão em fluxo ponto-a-ponto através de ponto final de transmissão em fluxo do hub IoT para o lado do serviço.

1. A aplicação do proxy de dispositivo local liga-se para o daemon de SSH que está à escuta na porta 22 no dispositivo. Esta definição é configurável, conforme descrito na secção "Executar a aplicação do proxy de dispositivo-local".

1. A aplicação de proxy de serviço local aguarda em novas ligações de SSH de um utilizador através da escuta numa porta designada, que neste caso é porta 2222. Esta definição é configurável, conforme descrito na secção "Executar a aplicação do proxy de serviço-local". Quando o usuário se conecta por meio do cliente SSH, o túnel permite o tráfego de aplicativo de SSH a serem transferidos entre o aplicativo de cliente e servidor SSH.

> [!NOTE]
> Tráfego SSH que é enviado através de um fluxo de dispositivo está em túnel através de ponto final de transmissão em fluxo do hub IoT em vez de enviadas diretamente entre o serviço e dispositivo. Para obter mais informações, consulte a [vantagens da utilização de fluxos de dispositivo do Iot Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* A pré-visualização de fluxos de dispositivo é atualmente suportada apenas para os hubs IoT que são criados nas seguintes regiões:

  * EUA Central
  * E.U.A. central EUAP

* As duas aplicações de exemplo que executar neste início rápido são escritas utilizando C#. É necessário o .NET Core SDK 2.1.0 ou posterior no seu computador de desenvolvimento.

  Pode baixar o [SDK para .NET Core para várias plataformas do .NET](https://www.microsoft.com/net/download/all).

* Certifique-se a versão atual do C# no computador de desenvolvimento com o seguinte comando:

    ```
    dotnet --version
    ```

* Execute o seguinte comando para adicionar a extensão de IoT do Azure para a CLI do Azure à sua instância do Cloud Shell. A extensão de IOT adiciona o IoT Hub, o IoT Edge e o serviço aprovisionamento de dispositivos IoT (DPS)-comandos específicos para a CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [Transferir o exemplo C# projeto](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, utilize o Azure Cloud Shell para registar um dispositivo simulado.

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

1. Para ligar ao seu hub IoT e estabelecer um fluxo de dispositivo, também tem do *cadeia de ligação de serviço* do seu hub IoT para permitir que o aplicativo do lado do serviço. O comando seguinte obtém este valor para o seu hub IoT:

   > [!NOTE]
   > Substitua a *YourIoTHubName* espaço reservado com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Tenha em atenção o valor devolvido para utilizar mais tarde neste início rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

Nesta secção, estabelecer um fluxo de ponto-a-ponto para encaminhar o tráfego SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de proxy local do dispositivo

Vá para o *dispositivo fluxos proxy/dispositivo* diretório na pasta do projeto descompactado. Mantenha as seguintes informações úteis:

| Nome do argumento | Valor do argumento |
|----------------|-----------------|
| `deviceConnectionString` | A cadeia de ligação do dispositivo que criou anteriormente. |
| `targetServiceHostName` | O endereço IP em que o servidor SSH escuta. O endereço seria `localhost` se fosse o mesmo IP onde está a executar a aplicação do proxy de dispositivo local. |
| `targetServicePort` | A porta que é utilizada pelo seu protocolo de aplicação (para SSH, por predefinição, esta seria a porta 22).  |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Executar a aplicação do proxy de serviço local

Navegue para `device-streams-proxy/service` na pasta do projeto descompactado. Terá das seguintes informações úteis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `iotHubConnectionString` | A cadeia de ligação de serviço do IoT Hub. |
| `deviceId` | O identificador do dispositivo que criou anteriormente. |
| `localPortNumber` | Uma porta local que o cliente SSH se conectarão. Vamos utilizar a porta 2222 neste exemplo, mas poderia usar outros números arbitrários. |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Executar o cliente SSH

Agora, utilize a sua aplicação de cliente SSH e ligar a aplicação de proxy de serviço local na porta 2222 (em vez do daemon diretamente de SSH).

```
ssh <username>@localhost -p 2222
```

Neste momento, a janela de início de sessão SSH pede-lhe para introduzir as suas credenciais.

Resultado da consola no lado do serviço (a aplicação do proxy de serviço local escuta na porta 2222):

![Saída da aplicação de proxy de serviço local](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Saída no aplicativo local do dispositivo proxy, que liga-se para o daemon de SSH da consola *IP_address:22*:

![Saída da aplicação local do dispositivo proxy](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Resultado da consola da aplicação de cliente SSH. O cliente SSH comunica com o daemon SSH ao ligar-se a porta 22, que a aplicação do proxy de serviço local está a escutar:

![Saída da aplicação cliente SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP para um dispositivo por meio de fluxos de dispositivo

A configuração para RDP é muito semelhante à configuração de SSH (descrita acima). Utilizar o IP de destino de RDP e a porta de 3389 em vez disso e utilizar o cliente RDP (em vez do cliente SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Executar a aplicação de proxy local do dispositivo (RDP)

Vá para o *dispositivo fluxos proxy/dispositivo* diretório na pasta do projeto descompactado. Mantenha as seguintes informações úteis:

| Nome do argumento | Valor do argumento |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de ligação do dispositivo que criou anteriormente. |
| `targetServiceHostName` | O nome de anfitrião ou endereço IP em que o servidor RDP é executado. O endereço seria `localhost` se fosse o mesmo IP onde está a executar a aplicação do proxy de dispositivo local. |
| `targetServicePort` | A porta utilizada pelo seu protocolo de aplicação (para RDP, por predefinição, esta seria a porta 3389).  |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Executar a aplicação do proxy de serviço local (RDP)

Navegue para `device-streams-proxy/service` na pasta do projeto descompactado. Terá das seguintes informações úteis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `iotHubConnectionString` | A cadeia de ligação de serviço do IoT Hub. |
| `deviceId` | O identificador do dispositivo que criou anteriormente. |
| `localPortNumber` | Uma porta local que o cliente SSH se conectarão. Vamos utilizar a porta 2222 neste exemplo, mas poderia modificar isso para outros números arbitrários. |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-rdp-client"></a>Executar o cliente RDP

Agora, utilize a sua aplicação de cliente RDP e ligar-se para a aplicação do proxy de serviço local na porta 2222 (que era uma porta disponível arbitrária que escolheu anteriormente).

![RDP liga-se para a aplicação do proxy de serviço local](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, configurar um hub IoT, registou um dispositivo, implementou aplicações de proxy local do dispositivo e de serviço local para estabelecer um fluxo do dispositivo através do hub IoT e as aplicações de proxy utilizadas para encaminhar o tráfego SSH ou RDP. O mesmo paradigma capaz de acomodar outros protocolos de cliente-servidor, onde o servidor é executada no dispositivo (por exemplo, o daemon de SSH).

Para saber mais sobre fluxos de dispositivo, veja:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
