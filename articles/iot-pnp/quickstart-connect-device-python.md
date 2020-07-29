---
title: Ligue ioT plug e play preview sample Python device code to Azure IoT Hub / Microsoft Docs
description: Utilize python para construir e executar o código do dispositivo de amostra IoT Plug e Play Preview que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352984"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>Quickstart: Ligue uma aplicação de dispositivo de pré-visualização IoT Plug e Play Para reproduzir o IoT Hub (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. A aplicação da amostra está escrita para Python e está incluída no Azure IoT Hub Device SDK para Python. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa do Python 3.7 na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [python.org](https://www.python.org/). Pode verificar a sua versão Python com o seguinte comando:  

```cmd/sh
python --version
```

### <a name="azure-iot-explorer"></a>Explorador de Azure IoT

Para interagir com o dispositivo de amostra na segunda parte deste arranque rápido, utilize a ferramenta **exploradora Azure IoT.** [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Também pode utilizar a ferramenta exploradora Azure IoT para encontrar a cadeia de ligação do hub IoT.

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>Configurar o ambiente

Este pacote é publicado como PIP para a atualização de pré-visualização pública. A versão do pacote deve ser a mais recente ou`2.1.4`

No seu ambiente pitão local, instale o ficheiro da seguinte forma:

```cmd/sh
pip install azure-iot-device
```

Clone o repositório Python SDK IoT e confira **o mestre:**

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

A pasta *azure-iot-sdk-python\azure-iot-device\samples\pnp* pasta contém o código de amostra para o dispositivo IoT Plug and Play. Este quickstart utiliza o ficheiro *pnp_thermostat.py.* Este código de amostra implementa um dispositivo compatível com IoT Plug and Play e utiliza a Biblioteca do Cliente do Dispositivo Azure IoT Python.

Crie uma variável ambiental chamada **IOTHUB_DEVICE_CONNECTION_STRING** para armazenar a cadeia de ligação do dispositivo que fez anteriormente.

Abra o ficheiro **pnp_thermostat.py** num editor de texto. Reparem como:

1. Define um identificador de modelo duplo de dispositivo único (DTMI) que representa exclusivamente o [termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). Um DTMI deve ser conhecido do utilizador e varia de acordo com o cenário de implementação do dispositivo. Para a amostra atual, o modelo representa um termóstato que tem telemetria, propriedades e comandos associados à temperatura de monitorização.

1. Tem funções para definir implementações de manipulador de comando. Escreve estes manipuladores para definir como o dispositivo responde aos pedidos de comando.

1. Tem uma função para definir uma resposta de comando. Cria funções de resposta ao comando para enviar uma resposta ao seu hub IoT.

1. Define uma função de ouvinte de teclado de entrada para deixar sair da aplicação.

1. Tem uma função **principal.** A função **principal:**

    1. Utiliza o dispositivo SDK para criar um cliente do dispositivo e ligar-se ao seu hub IoT.

    1. Atualiza propriedades. O modelo que estamos a usar, **o Termóstato,** define `targetTemperature` e como as `maxTempSinceLastReboot` duas propriedades para o nosso termóstato, de modo que é isso que vamos usar. As propriedades são atualizadas utilizando o `patch_twin_reported_properties` método definido no `device_client` .

    1. Começa a ouvir pedidos de comando utilizando a função **execute_command_listener.** A função configura um 'ouvinte' para ouvir os comandos provenientes do serviço. Quando configuras o ouvinte, forneces um `method_name` `user_command_handler` , e `create_user_response_handler` . 
        - A `user_command_handler` função define o que o dispositivo deve fazer quando recebe um comando. Por exemplo, se o alarme disparar, o efeito de receber este comando é que acorda. Pense nisto como o "efeito" do comando ser invocado.
        - A `create_user_response_handler` função cria uma resposta a ser enviada para o seu hub IoT quando um comando executa com sucesso. Por exemplo, se o alarme disparar, responde batendo no soneca, que é o feedback do serviço. Pense nisto como a resposta que dá ao serviço. Pode ver esta resposta no portal.

    1. Começa a enviar telemetria. O **pnp_send_telemetry** é definido no ficheiro pnp_methods.py. O código de amostra utiliza um laço para chamar esta função a cada oito segundos.

    1. Desativa todos os ouvintes e tarefas, existindo o loop quando pressiona **Q** ou **q**.

Agora que viu o código, use o seguinte comando para executar a amostra:

```cmd/sh
python pnp_thermostat.py
```

Vê a seguinte saída, que indica que o dispositivo está a enviar dados de telemetria para o hub, e está agora pronto para receber comandos e atualizações de propriedade:

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Interaja com um dispositivo IoT Plug e Play Preview que esteja ligado à sua solução](quickstart-service-python.md)
