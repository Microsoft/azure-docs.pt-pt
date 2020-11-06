---
title: Quickstart - Connect IoT Plug and Play sample Python device code to Azure IoT Hub [ Connect IoT Plug and Play python device code to Azure IoT Hub ] Microsoft Docs
description: Quickstart - Use Python para construir e executar o código do dispositivo de amostra IoT Plug e Play que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 38c6b835f0a3ddaaf4c049ca6ea3aeb484ad3280
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421571"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-to-iot-hub-python"></a>Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play à IoT Hub (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. A aplicação da amostra está escrita para Python e está incluída no Azure IoT Hub Device SDK para Python. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este arranque rápido, precisa do Python 3.7 na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [python.org](https://www.python.org/). Pode verificar a sua versão Python com o seguinte comando:  

```cmd/sh
python --version
```

No seu ambiente pitão local, instale o pacote da seguinte forma:

```cmd/sh
pip install azure-iot-device
```

Clone o repositório Python SDK IoT e confira **o mestre:**

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

A pasta *azure-iot-sdk-python\azure-iot-device\samples\pnp* pasta contém o código de amostra para o dispositivo IoT Plug and Play. Este quickstart utiliza o ficheiro *simple_thermostat.py.* Este código de amostra implementa um dispositivo compatível com IoT Plug and Play e utiliza a Biblioteca do Cliente do Dispositivo Azure IoT Python.

Abra o ficheiro **simple_thermostat.py** num editor de texto. Reparem como:

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

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)da amostra .

Agora que viu o código, use o seguinte comando para executar a amostra:

```cmd/sh
python simple_thermostat.py
```

Vê a seguinte saída, que indica que o dispositivo está a enviar dados de telemetria para o hub, e está agora pronto para receber comandos e atualizações de propriedade:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Interaja com um dispositivo IoT Plug and Play que está ligado à sua solução](quickstart-service-python.md)
