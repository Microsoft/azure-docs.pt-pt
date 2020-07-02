---
title: 'Início Rápido: Enviar telemetria para o Hub IoT do Azure | Microsoft Docs'
description: Neste quickstart, você executou uma aplicação de Xamarin Forms de amostra para enviar telemetria simulada para um hub IoT e para ler telemetria do hub IoT para processamento na nuvem.
author: cmaneu
manager: philmea
ms.author: chmaneu
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 05/20/2020
ms.openlocfilehash: d2292a65ef25f6450d6810b50366daa622b92bc8
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802742"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-xamarin-forms"></a>Quickstart: Enviar telemetria de um dispositivo para um hub IoT (Formas Xamarin)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste artigo, vai enviar telemetria a partir de uma aplicação de dispositivo simulado para o Hub IoT. Em seguida, pode ver os dados de uma aplicação de back-end.

Este artigo utiliza uma aplicação pré-escrita de Formulários Xamarin para enviar a telemetria e um utilitário CLI para ler a telemetria do IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- Transferir o código de exemplo a partir dos [exemplos do Azure](https://github.com/Azure-Samples/azure-iot-samples-xamarin/archive/master.zip)

- A mais recente versão do [Visual Studio 2019](https://visualstudio.microsoft.com/) ou [Visual Studio para Mac](https://visualstudio.microsoft.com/) com ferramentas Xamarin Forms instaladas. Este quickstart foi testado com Visual Studio 16.6.0.

- Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

- Executar o seguinte comando para adicionar a extensão IoT do Microsoft Azure para Azure CLI à sua instância Cloud Shell. A extensão IoT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   **myXamarinDevice**: Este é o nome do dispositivo que está a registar. Recomenda-se que use **o myXamarinDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myXamarinDevice
   ```

1. Executar o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myXamarinDevice --output table
   ```

   Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myXamarinDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no arranque rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação da amostra é feita no Windows - através de uma aplicação UWP - um dispositivo iOS ou simulador e no dispositivo ou simulador Android, que se conecta a um ponto final específico do dispositivo no seu hub IoT e envia telemetria simulada de temperatura e humidade. 

1. Abra o espaço de trabalho da amostra no Visual Studio ou no Visual Studio para Mac.
2. Expandir o projeto **SimulatedDevice.**  
3. Open **IoTHubService.cs** para edição no Visual Studio. 
4. Procure a **variável _iotHubConnectionString** e atualize o valor com a cadeia de ligação do dispositivo que fez uma nota anterior.
5. Guarde as alterações. 
6. Executar o projeto no emulador do dispositivo ou num dispositivo real com o botão **Construir e executar** ou o atalho de chave **F5** no Windows ou **comando + r** no Mac. 

   ![Executar o projeto](media/quickstart-send-telemetry-xamarinforms/run-sample.png)

7. Quando o emulador se abrir, selecione **Start** (Iniciar) na aplicação de exemplo.

A imagem que se segue mostra alguma saída de exemplo à medida que a aplicação envia telemetria simulada para o seu hub IoT: ![ Executar o dispositivo simulado](media/quickstart-send-telemetry-xamarinforms/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

A aplicação de exemplo que executou no emulador XCode mostra os dados das mensagens enviadas a partir do dispositivo. Também pode ver os dados através do hub IoT, à medida que são recebidos. A extensão da CLI do Hub IoT pode ligar ao ponto final de **Eventos** do lado do serviço no seu Hub IoT. A extensão recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

Execute os seguintes comandos no Azure Cloud Shell, ao substituir `YourIoTHubName` pelo nome do hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myXamarinDevice --hub-name {YourIoTHubName}
```

A seguinte captura de ecrã mostra a saída à medida que a extensão recebe telemetria enviada pelo dispositivo simulado para o hub:

A imagem que se segue mostra o tipo de telemetria que se vê na janela do terminal local: ![ Ver telemetria](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub a partir de uma aplicação Xamarin Forms, e leu a telemetria a partir do centro. 

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-node.md)
