---
title: Interaja com um dispositivo IoT Plug and Play ligado à sua solução Azure IoT (Python) Microsoft Docs
description: Utilize python para ligar e interagir com um dispositivo IoT Plug and Play que esteja ligado à sua solução Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574976"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Quickstart: Interaja com um dispositivo IoT Plug and Play que está ligado à sua solução (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

O IoT Plug and Play simplifica o IoT, permitindo-lhe interagir com o modelo de um dispositivo sem ter conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Python para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este arranque rápido, precisa do Python 3.7 na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [python.org](https://www.python.org/). Pode verificar a sua versão Python com o seguinte comando:  

```cmd/sh
python --version
```

O pacote **azure-iot-device** é publicado como PIP.

No seu ambiente pitão local, instale o pacote da seguinte forma:

```cmd/sh
pip install azure-iot-device
```

Instale o pacote **azure-iot-hub** executando o seguinte comando:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)da amostra .

Neste arranque rápido, utilize um dispositivo termóstato de amostra, escrito em Python, como o dispositivo IoT Plug and Play. Para executar o dispositivo de amostragem:

1. Abra uma janela de terminal numa pasta à sua escolha. Executar o seguinte comando para clonar o repositório [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub para este local:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Esta janela de terminal é utilizada como terminal do seu **dispositivo.** Vá à pasta do seu repositório clonado e navegue para a pasta */azure-iot-sdk-python/azure-iot-device/samples/pnp.*

1. Executar o dispositivo termóstato da amostra com o seguinte comando:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Estas mensagens indicam que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade. Não feche este terminal, precisa dele para confirmar que a amostra de serviço está a funcionar.

## <a name="run-the-sample-solution"></a>Executar a solução de amostra

Neste arranque rápido, você usa uma solução de IoT de amostra em Python para interagir com o dispositivo de amostra que acabou de configurar.

1. Abra outra janela do terminal para utilizar como terminal **de serviço.** 

1. Navegue para a pasta */azure-iot-sdk-python/azure-iot-hub/samples* do repositório Python SDK clonado.

1. Na pasta das amostras, existem quatro ficheiros de amostras que demonstram as operações com a classe Digital Twin Manager: *get_digital_twin_sample.py, update_digitial_twin_sample.py, invoke_command_sample.py e invoke_component_command_sample-py*.  Estas amostras mostram como utilizar cada API para interagir com dispositivos IoT Plug e Play:

### <a name="get-digital-twin"></a>Obter twin digital

Em [Configurar o seu ambiente para os quickstarts e tutoriais IoT Plug and Play](set-up-environment.md) criou duas variáveis ambientais para configurar a amostra para ligar ao seu hub e dispositivo IoT:

* **IOTHUB_CONNECTION_STRING**: a cadeia de ligação do hub IoT que fez uma nota anterior.
* **IOTHUB_DEVICE_ID:** `"my-pnp-device"` .

Utilize o seguinte comando no terminal **de serviço** para executar esta amostra:

```cmd/sh
python get_digital_twin_sample.py
```

A saída mostra o gémeo digital do dispositivo e imprime o seu ID modelo:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

O seguinte corte mostra o código de amostra de *get_digital_twin_sample.py:*

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Atualize um gémeo digital

Esta amostra mostra-lhe como usar um *patch* para atualizar propriedades através do twin digital do seu dispositivo. O seguinte corte de *update_digital_twin_sample.py* mostra como construir o patch:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Utilize o seguinte comando no terminal **de serviço** para executar esta amostra:

```cmd/sh
python update_digital_twin_sample.py
```

Pode verificar se a atualização é aplicada no terminal do **dispositivo** que mostra a seguinte saída:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

O terminal **de serviço** confirma que o patch foi bem sucedido:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Invocar um comando

Para invocar um comando, executar a amostra *de invoke_command_sample.py.* Esta amostra mostra como invocar um comando num simples dispositivo termóstato. Antes de executar esta amostra, desaprote as `IOTHUB_COMMAND_NAME` variáveis e `IOTHUB_COMMAND_PAYLOAD` ambientais no terminal **de serviço:**

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

No terminal **de serviço,** utilize o seguinte comando para executar a amostra:
  
```cmd/sh
python invoke_command_sample.py
```

O terminal **de serviço** mostra uma mensagem de confirmação do dispositivo:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

No terminal do **dispositivo,** vê-se que o dispositivo recebe o comando:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a uma solução IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de desenvolvedores de modelação IoT Plug e Play](concepts-developer-guide-device-csharp.md)
