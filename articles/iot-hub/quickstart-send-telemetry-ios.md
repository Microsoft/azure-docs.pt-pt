---
title: 'Início Rápido: Enviar telemetria para o Hub IoT do Azure | Microsoft Docs'
description: Neste início rápido, vai executar uma aplicação iOS de exemplo para enviar telemetria simulada para um hub do IoT e ler telemetria do mesmo, para processamento na cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/03/2019
ms.openlocfilehash: 3bb51db139dbdafef63c0c2da71a1ca4ce582338
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675407"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste artigo, vai enviar telemetria a partir de uma aplicação de dispositivo simulado para o Hub IoT. Em seguida, pode ver os dados de uma aplicação de back-end.

Este artigo utiliza uma aplicação Swift pré-escrita para enviar a telemetria e um utilitário de CLI para lê-la a partir do Hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Transferir o código de exemplo a partir dos [exemplos do Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)

- A versão mais recente do [XCode](https://developer.apple.com/xcode/), com a última versão do SDK do iOS. Este quickstart foi testado com XCode 10.2 e iOS 12.2.

- A versão mais recente do [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

- Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste quickstart utiliza o protocolo MQTT, que comunica através da porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


- Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IoT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   **myiOSdevice**: Este é o nome do dispositivo que está a registar. É recomendado usar **o myiOSdevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Execute o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no início.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação de exemplo é executada num dispositivo iOS, que se liga a um ponto final de um dispositivo específico no seu hub IoT e envia a telemetria de humidade e de temperatura simulada. 

### <a name="install-cocoapods"></a>Instalar CocoaPods

O CocoaPods gere dependências para projetos de iOS que utilizam bibliotecas de terceiros.

Numa janela de terminal local, navegue para a pasta Azure-IoT-Samples-iOS que transferiu nos pré-requisitos. Depois, navegue para o projeto de exemplo:

```sh
cd quickstart/sample-device
```

Confirme que XCode está fechado e execute o seguinte comando para instalar os CocoaPods que são declarados no ficheiro **podfile**:

```sh
pod install
```

Para além de instalar os pods necessários para o projeto, o comando de instalação também criou um ficheiro de área de trabalho do XCode, que já está configurado para utilizar os pods para dependências. 

### <a name="run-the-sample-application"></a>Executar o exemplo de aplicação 

1. Abra a área de trabalho de exemplo no XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expanda o projeto **MQTT Client Sample** e, em seguida, expanda a pasta com o mesmo nome.  
3. Abra **ViewController.swift** para edição no XCode. 
4. Procure a variável **de ligaçãoString** e atualize o valor com a cadeia de ligação do dispositivo de que fez uma nota anterior.
5. Guarde as alterações. 
6. Execute o projeto no emulador do dispositivo com o botão **Build and run** (Compilar e executar) ou a combinação de teclas **comando + r**. 

   ![Executar o projeto](media/quickstart-send-telemetry-ios/run-sample.png)

7. Quando o emulador se abrir, selecione **Start** (Iniciar) na aplicação de exemplo.

A captura de ecrã abaixo mostra uma saída de exemplo à medida que a aplicação envia telemetria simulada para o hub IoT:

   ![Executar o dispositivo simulado](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

A aplicação de exemplo que executou no emulador XCode mostra os dados das mensagens enviadas a partir do dispositivo. Também pode ver os dados através do hub IoT, à medida que são recebidos. A extensão da CLI do Hub IoT pode ligar ao ponto final de **Eventos** do lado do serviço no seu Hub IoT. A extensão recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

Execute os seguintes comandos no Azure Cloud Shell, ao substituir `YourIoTHubName` pelo nome do hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

A seguinte captura de ecrã mostra a saída à medida que a extensão recebe telemetria enviada pelo dispositivo simulado para o hub:

A captura de ecrã abaixo mostra o tipo de telemetria que vai ver na janela de terminal local:

![Ver telemetria](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, instalou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub a partir de um dispositivo iOS, e leu a telemetria a partir do centro. 

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-node.md)
