---
title: Início rápido de fluxos C# de dispositivos no Hub IOT do Azure para SSH e RDP
description: Neste guia de início rápido, você executa C# dois aplicativos de exemplo que habilitam cenários SSH e RDP em um fluxo de dispositivo do Hub IOT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: a1b6adc37d78e3fa73d7ae70237d678af6b9dc99
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769456"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Início rápido: habilitar SSH e RDP em um fluxo de dispositivo do Hub IoT C# usando um aplicativo proxy (versão prévia)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure Hub IoT atualmente dá suporte a fluxos de dispositivo como um [recurso de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivo do Hub IOT](iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e de dispositivo se comuniquem de maneira segura e amigável ao firewall. Este guia de arranque C# rápido envolve duas aplicações que permitem que o tráfego de aplicações do servidor cliente (como Secure Shell [SSH] e Remote Desktop Protocol [RDP] seja enviado através de um fluxo de dispositivos que é estabelecido através de um hub IoT. Para obter uma visão geral da configuração, consulte [exemplo de aplicativo de proxy local para SSH ou RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Este artigo descreve primeiro a instalação do SSH (usando a porta 22) e descreve como modificar a porta da instalação para o RDP. Como os fluxos de dispositivo são independentes de aplicativo e de protocolo, o mesmo exemplo pode ser modificado para acomodar outros tipos de tráfego de aplicativo. Essa modificação geralmente envolve apenas a alteração da porta de comunicação para aquela usada pelo aplicativo pretendido.

## <a name="how-it-works"></a>Como funciona

A figura a seguir ilustra como os aplicativos de proxy local do dispositivo e de serviço neste exemplo habilitam a conectividade de ponta a ponta entre o cliente SSH e os processos do daemon SSH. Aqui, supomos que o daemon está em execução no mesmo dispositivo que o aplicativo de proxy de dispositivo local.

![Instalação do aplicativo proxy local](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. O aplicativo de proxy de serviço local se conecta ao Hub IoT e inicia um fluxo de dispositivo para o dispositivo de destino.

1. O aplicativo de proxy de dispositivo local conclui o handshake de início de fluxo e estabelece um túnel de streaming de ponta a ponta por meio do ponto de extremidade de streaming do Hub IoT para o lado do serviço.

1. O aplicativo de proxy de dispositivo local se conecta ao daemon SSH que está escutando na porta 22 do dispositivo. Essa configuração é configurável, conforme descrito na seção "executar o aplicativo de proxy local do dispositivo".

1. O aplicativo de proxy de serviço local aguarda novas conexões SSH de um usuário ouvindo em uma porta designada, que nesse caso é a porta 2222. Essa configuração é configurável, conforme descrito na seção "executar o aplicativo de proxy local de serviço". Quando o usuário se conecta por meio do cliente SSH, o túnel permite que o tráfego do aplicativo SSH seja transferido entre o cliente SSH e o aplicativo de servidor.

> [!NOTE]
> O tráfego SSH enviado por um fluxo de dispositivo é encapsulado por meio do ponto de extremidade de streaming do Hub IoT em vez de ser enviado diretamente entre o serviço e o dispositivo. Para obter mais informações, consulte os [benefícios de usar fluxos de dispositivo do Hub IOT](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, há suporte para a visualização de fluxos de dispositivo apenas para os hubs IoT criados nas seguintes regiões:

  * Centro dos E.U.A.
  * EUA Central EUAP
  * Ásia Sudeste
  * Europa do Norte

* Os dois aplicativos de exemplo que você executa neste guia de início rápido C#são escritos em. Você precisa do SDK do .NET Core 2.1.0 ou posterior em seu computador de desenvolvimento.

  Você pode baixar o [SDK do .NET Core para várias plataformas do .net](https://www.microsoft.com/net/download/all).

* Verifique a versão atual do C# no computador de desenvolvimento usando o seguinte comando:

    ```
    dotnet --version
    ```

* Execute o comando a seguir para adicionar a extensão de IoT do Azure para CLI do Azure à sua instância do Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT ao CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [Descarregue as amostras Azure IoT C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)e extraia o arquivo ZIP.

* Uma conta de usuário e uma credencial válidas no dispositivo (Windows ou Linux) usadas para autenticar o usuário.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste guia de início rápido, você usa Azure Cloud Shell para registrar um dispositivo simulado.

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

1. Para se conectar ao Hub IoT e estabelecer um fluxo de dispositivo, você também precisa da *cadeia de conexão de serviço* do Hub IOT para habilitar o aplicativo do lado do serviço. O comando a seguir recupera esse valor para o Hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observe a cadeia de conexão de serviço retornada para uso posterior neste guia de início rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

Nesta seção, você estabelece um fluxo de ponta a ponta para encapsular o tráfego SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar o aplicativo de proxy de dispositivo local

Em uma janela de terminal local, navegue até o diretório `device-streams-proxy/device` em sua pasta de projeto descompactada. Mantenha as seguintes informações à mão:

| Nome do argumento | Valor do argumento |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de conexão do dispositivo do dispositivo que você criou anteriormente. |
| `targetServiceHostName` | O endereço IP onde o servidor SSH escuta. O endereço seria `localhost` se fosse o mesmo IP em que o aplicativo de proxy de dispositivo local está em execução. |
| `targetServicePort` | A porta usada pelo protocolo de aplicativo (para SSH, por padrão, seria a porta 22).  |

Compile e execute o código com os seguintes comandos:

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

### <a name="run-the-service-local-proxy-application"></a>Executar o aplicativo de proxy local de serviço

Em outra janela do terminal local, navegue até `iot-hub/quickstarts/device-streams-proxy/service` em sua pasta de projeto descompactada. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de conexão de serviço do Hub IoT. |
| `MyDevice` | O identificador do dispositivo que você criou anteriormente. |
| `localPortNumber` | Uma porta local à qual seu cliente SSH se conectará. Usamos a porta 2222 neste exemplo, mas você pode usar outros números arbitrários. |

Compile e execute o código com os seguintes comandos:

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

Agora, use seu aplicativo cliente SSH e conecte-se ao aplicativo de proxy de serviço local na porta 2222 (em vez do daemon SSH diretamente).

```
ssh {username}@localhost -p 2222
```

Neste ponto, a janela de entrada SSH solicita que você insira suas credenciais.

Saída do console no lado do serviço (o aplicativo de proxy local do serviço escuta na porta 2222):

![Saída do aplicativo de proxy local de serviço](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Saída de consola na aplicação proxy local do dispositivo, que se liga ao daemon SSH em *IP_address:22*:

![Saída do aplicativo de proxy local do dispositivo](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Saída do console do aplicativo cliente SSH. O cliente SSH se comunica com o daemon SSH conectando-se à porta 22, na qual o aplicativo proxy de serviço local está escutando:

![Saída do aplicativo cliente SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP para um dispositivo por meio de fluxos de dispositivo

A configuração para RDP é semelhante à configuração para SSH (descrita acima). Você usará o IP de destino RDP e a porta 3389 em vez disso e usará o cliente RDP (em vez do cliente SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Executar o aplicativo de proxy local do dispositivo (RDP)

Em uma janela de terminal local, navegue até o diretório `device-streams-proxy/device` em sua pasta de projeto descompactada. Mantenha as seguintes informações à mão:

| Nome do argumento | Valor do argumento |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de conexão do dispositivo do dispositivo que você criou anteriormente. |
| `targetServiceHostName` | O nome de host ou endereço IP onde o servidor RDP é executado. O endereço seria `localhost` se fosse o mesmo IP em que o aplicativo de proxy de dispositivo local está em execução. |
| `targetServicePort` | A porta usada pelo protocolo de aplicativo (para RDP, por padrão, seria a porta 3389).  |

Compile e execute o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Executar o aplicativo de proxy local do serviço (RDP)

Em outra janela do terminal local, navegue até `device-streams-proxy/service` em sua pasta de projeto descompactada. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de conexão de serviço do Hub IoT. |
| `MyDevice` | O identificador do dispositivo que você criou anteriormente. |
| `localPortNumber` | Uma porta local à qual seu cliente SSH se conectará. Usamos a porta 2222 neste exemplo, mas você pode modificá-la para outros números arbitrários. |

Compile e execute o código com os seguintes comandos:

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

### <a name="run-rdp-client"></a>Executar o cliente RDP

Agora, use seu aplicativo cliente RDP e conecte-se ao aplicativo de proxy local de serviço na porta 2222 (essa era uma porta arbitrária disponível que você escolheu anteriormente).

![O RDP se conecta ao aplicativo de proxy local de serviço](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você configura um hub IoT, registrou um dispositivo, implantou aplicativos proxy locais e de serviço local para estabelecer um fluxo de dispositivo por meio do Hub IoT e usou os aplicativos de proxy para encapsular o tráfego SSH ou RDP. O mesmo paradigma pode acomodar outros protocolos cliente-servidor, onde o servidor é executado no dispositivo (por exemplo, o daemon SSH).

Para saber mais sobre fluxos de dispositivo, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivo](./iot-hub-device-streams-overview.md)
