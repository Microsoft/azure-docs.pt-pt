---
title: Quickstart - Azure IoT Hub streams C# quickstart para SSH e RDP
description: Neste quickstart, você executou duas aplicações C# de amostra que permitem cenários SSH e RDP sobre um fluxo de dispositivo ioT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 5bd33a2da6b2f1ae775f088950f14ac4df465fbf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863952"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Quickstart: Ative o SSH e o RDP sobre um fluxo de dispositivo IoT Hub utilizando uma aplicação de procuração C# (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

O Microsoft Azure IoT Hub suporta atualmente os fluxos de dispositivos como [uma funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[As correntes de dispositivos IoT Hub](iot-hub-device-streams-overview.md) permitem que as aplicações de serviço e dispositivos se comuniquem de forma segura e amiga da firewall. Este guia de arranque rápido envolve duas aplicações C# que permitem que o tráfego de aplicações do servidor do cliente (como Secure Shell [SSH] e Remote Desktop Protocol [RDP] seja enviado através de um fluxo de dispositivos estabelecido através de um hub IoT. Para uma visão geral da configuração, consulte a [amostra de aplicação de procuração local para SSH ou RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Este artigo descreve primeiro a configuração para SSH (utilizando a porta 22) e, em seguida, descreve como modificar a porta da configuração para RDP. Como os fluxos de dispositivos são aplicações e protocolo-agnósticos, a mesma amostra pode ser modificada para acomodar outros tipos de tráfego de aplicações. Esta modificação geralmente envolve apenas a alteração da porta de comunicação para a que é usada pela aplicação pretendida.

## <a name="prerequisites"></a>Pré-requisitos

* A pré-visualização dos fluxos de dispositivos é atualmente suportada apenas para centros IoT que são criados nas seguintes regiões:

  * E.U.A. Central
  * EUA Central EUAP
  * Sudeste Asiático
  * Europa do Norte

* As duas aplicações de amostra que você executou neste quickstart estão escritas em C#. Precisa do .NET Core SDK 2.1.0 ou mais tarde na sua máquina de desenvolvimento.

    Pode baixar o [.NET Core SDK para várias plataformas a partir de .NET](https://dotnet.microsoft.com/download).

    Verifique a versão atual de C# na sua máquina de desenvolvimento utilizando o seguinte comando:

    ```
    dotnet --version
    ```

* [Descarregue as amostras Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)e extraia o arquivo ZIP.

* Uma conta de utilizador válida e credencial no dispositivo (Windows ou Linux) utilizada para autenticar o utilizador.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Como funciona

A figura a seguir ilustra como as aplicações de procuração local e local de dispositivo nesta amostra permitem a conectividade de ponta a ponta entre o cliente SSH e os processos de daemon SSH. Aqui, assumimos que o daemon está a funcionar no mesmo dispositivo que a aplicação de procuração local do dispositivo.

![Configuração de aplicação de procuração local](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. A aplicação de procuração local de serviço liga-se ao hub IoT e inicia um fluxo de dispositivo para o dispositivo alvo.

1. A aplicação proxy local do dispositivo completa o aperto de mão de iniciação do fluxo e estabelece um túnel de streaming de ponta a ponta através do ponto final de streaming do hub IoT para o lado de serviço.

1. A aplicação proxy local do dispositivo liga-se ao daemon SSH que está a ouvir na porta 22 do dispositivo. Esta definição é configurável, tal como descrito na secção "Executar a aplicação proxy local do dispositivo".

1. A aplicação de procuração local de serviço aguarda novas ligações SSH de um utilizador ouvindo numa porta designada, que neste caso é a porta 2222. Esta definição é configurável, tal como descrito na secção "Executar a aplicação de procuração local de serviço". Quando o utilizador se conecta através do cliente SSH, o túnel permite que o tráfego de aplicações SSH seja transferido entre o cliente SSH e a aplicação do servidor.

> [!NOTE]
> O tráfego SSH que é enviado através de um fluxo de dispositivo é escavado através do ponto de streaming do hub IoT em vez de ser enviado diretamente entre o serviço e o dispositivo. Para obter mais informações, consulte os [benefícios da utilização de fluxos de dispositivos Iot Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste arranque rápido, utiliza-se a Azure Cloud Shell para registar um dispositivo simulado.

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

1. Para ligar ao seu hub IoT e estabelecer um fluxo de dispositivo, também precisa da cadeia de *ligação* de serviço do seu hub IoT para ativar a aplicação do lado do serviço. O seguinte comando recupera este valor para o seu hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Note o fio de ligação de serviço devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo através de fluxos de dispositivos

Nesta secção, estabelece-se um fluxo de ponta a ponta para o tráfego do túnel SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de procuração local do dispositivo

Numa janela de terminal local, navegue para o `device-streams-proxy/device` diretório na sua pasta de projeto desapertado. Mantenha as seguintes informações à mão:

| Nome do argumento | Valor do argumento |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de ligação do dispositivo do dispositivo que criou anteriormente. |
| `targetServiceHostName` | O endereço IP onde o servidor SSH ouve. O endereço seria `localhost` se fosse o mesmo IP onde a aplicação de procuração local do dispositivo está em execução. |
| `targetServicePort` | A porta que é utilizada pelo seu protocolo de aplicação (para SSH, por padrão, esta seria a porta 22).  |

Compilar e executar o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Executar a aplicação de procuração local de serviço

Em outra janela do terminal local, navegue para `iot-hub/quickstarts/device-streams-proxy/service` a sua pasta de projeto desapertado. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de ligação de serviço do seu Hub IoT. |
| `MyDevice` | O identificador do dispositivo que criou anteriormente. |
| `localPortNumber` | Um porto local ao que o seu cliente SSH irá ligar. Usamos a porta 2222 nesta amostra, mas você pode usar outros números arbitrários. |

Compilar e executar o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Executar o cliente SSH

Agora use a sua aplicação de cliente SSH e ligue-se à aplicação de procuração local de serviço na porta 2222 (em vez do daemon SSH diretamente).

```
ssh {username}@localhost -p 2222
```

Neste ponto, a janela de entrada SSH pede-lhe que introduza as suas credenciais.

Saída da consola no lado do serviço (a aplicação de procuração local de serviço ouve na porta 2222):

![Saída de aplicação de procuração local de serviço](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Saída da consola na aplicação proxy local do dispositivo, que se conecta ao daemon SSH em *IP_address:22*:

![Saída de aplicação de procuração local do dispositivo](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Saída da consola da aplicação do cliente SSH. O cliente SSH comunica ao daemon SSH ligando-se à porta 22, que a aplicação de procuração local de serviço está a ouvir:

![Saída de aplicação do cliente SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP para um dispositivo através de fluxos de dispositivos

A configuração para RDP é semelhante à configuração para SSH (descrito acima). Utilize o IP de destino RDP e a porta 3389 e utilize o cliente RDP (em vez do cliente SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Executar a aplicação de procuração local do dispositivo (RDP)

Numa janela de terminal local, navegue para o `device-streams-proxy/device` diretório na sua pasta de projeto desapertado. Mantenha as seguintes informações à mão:

| Nome do argumento | Valor do argumento |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de ligação do dispositivo do dispositivo que criou anteriormente. |
| `targetServiceHostName` | O nome de anfitrião ou endereço IP onde funciona o servidor RDP. O endereço seria `localhost` se fosse o mesmo IP onde a aplicação de procuração local do dispositivo está em execução. |
| `targetServicePort` | A porta utilizada pelo protocolo de aplicação (para RDP, por defeito, esta seria a porta 3389).  |

Compilar e executar o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Executar a aplicação de procuração local de serviço (RDP)

Em outra janela do terminal local, navegue para `device-streams-proxy/service` a sua pasta de projeto desapertado. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de ligação de serviço do seu Hub IoT. |
| `MyDevice` | O identificador do dispositivo que criou anteriormente. |
| `localPortNumber` | Um porto local ao que o seu cliente SSH irá ligar. Usamos a porta 2222 nesta amostra, mas pode modificá-la para outros números arbitrários. |

Compilar e executar o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>Executar cliente RDP

Agora use a sua aplicação de cliente RDP e ligue-se à aplicação de procuração local de serviço na porta 2222 (esta era uma porta arbitrária disponível que escolheu anteriormente).

![RDP conecta-se à aplicação de procuração local de serviço](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você montou um hub IoT, registou um dispositivo, implementou aplicações de procuração local e local de dispositivos para estabelecer um fluxo de dispositivo através do hub IoT, e usou as aplicações de procuração para fazer o túnel de tráfego SSH ou RDP. O mesmo paradigma pode acomodar outros protocolos de servidor cliente, onde o servidor funciona no dispositivo (por exemplo, o daemon SSH).

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
