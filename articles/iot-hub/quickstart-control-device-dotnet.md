---
title: Quickstart - Controle um dispositivo a partir de Azure IoT Hub quickstart (.NET) | Microsoft Docs
description: Neste guia de início rápido, irá executar duas aplicações C# de exemplo. Uma aplicação é uma aplicação de serviço que pode controlar remotamente dispositivos ligados ao seu hub. A outra aplicação simula um dispositivo ligado ao seu hub que pode ser controlado remotamente.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 03/04/2020
ms.openlocfilehash: 28d80a20c50f846146aed069028303e86426df27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072090"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Guia de Início Rápido: Controlar um dispositivo ligado a um hub IoT (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O IoT Hub é um serviço Azure que lhe permite gerir os seus dispositivos IoT a partir da nuvem e ingerir grandes volumes de telemetria do dispositivo na nuvem para armazenamento ou processamento. Neste guia de início rápido, irá utilizar um *método direto* para controlar um dispositivo simulado ligado ao seu hub IoT. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT.

O guia de início rápido utiliza duas aplicações .NET pré-escritas:

* Uma aplicação de dispositivo simulado que responde a métodos diretos chamados a partir de uma aplicação de serviço. Para receber as chamadas de método direto, esta aplicação liga-se a um ponto final específico do dispositivo no seu hub IoT.

* Uma aplicação de serviço que liga para os métodos diretos no dispositivo simulado. Para chamar um método direto num dispositivo, esta aplicação liga-se a um ponto final do lado do serviço no seu hub IoT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* As duas aplicações de exemplo que executa neste guia de início rápido são escritas com C#. Precisa do .NET Core SDK 3.1 ou superior na sua máquina de desenvolvimento.

    Pode transferir o SDK de .NET Core para múltiplas plataformas em [.NET](https://www.microsoft.com/net/download/all).

    Pode verificar qual a versão atual do C# no seu computador de desenvolvimento através do seguinte comando:

    ```cmd/sh
    dotnet --version
    ```
* Se ainda não o fez, descarregue as amostras Azure IoT C# https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   **MyDotnetDevice:** Este é o nome do dispositivo que está a registar. Recomenda-se a utilização **do MyDotnetDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também precisa de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="retrieve-the-service-connection-string"></a>Obter a cadeia de ligação do serviço

Também precisa do seu fio de _ligação de serviço_ ioT hub para permitir que a aplicação de serviço se conecte ao hub e recupere as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Irá utilizar este valor mais adiante no guia de início rápido. Esta cadeia de ligação de serviço é diferente da cadeia de ligação do dispositivo que notou no passo anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e aguarda chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia um reconhecimento de volta ao seu centro depois de executar o método direto.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\SimulatedDeviceWithCommand.**

2. Na janela de terminal local, execute os seguintes comandos para instalar os pacotes exigidos para a aplicação de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

3. Na janela do terminal local, executar o seguinte comando para construir e executar a aplicação do dispositivo simulado, `{DeviceConnectionString}` substituindo-a pela cadeia de ligação do dispositivo que observou anteriormente:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação de serviço liga-se a um ponto final do lado do serviço no seu Hub IoT. A aplicação faz chamadas de método direto para um dispositivo através do seu hub IoT e ouve para reconhecimentos. Uma aplicação de serviço IoT Hub normalmente corre na nuvem.

1. Noutra janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\InvokeDeviceMethod.**

2. Na janela do terminal local, executar os seguintes comandos para instalar as bibliotecas necessárias para a aplicação de serviço:

    ```cmd/sh
    dotnet restore
    ```

3. Na janela do terminal local, executar os seguintes comandos para construir e executar a aplicação de serviço, `{ServiceConnectionString}` substituindo-a pela cadeia de ligação de serviço que observou anteriormente:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    A imagem que se segue mostra a saída à medida que a aplicação faz uma chamada de método direto para o dispositivo e recebe um reconhecimento:

    ![Executar a aplicação de serviço](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Depois de executar a aplicação de serviço, vê uma mensagem na janela da consola a executar o dispositivo simulado e a taxa a que envia alterações de mensagens:

    ![Alteração no cliente simulado](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, chamou um método direto num dispositivo a partir de uma aplicação de serviço, e respondeu à chamada de método direto numa aplicação simulada do dispositivo.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)
