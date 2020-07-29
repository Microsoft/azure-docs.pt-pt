---
title: Interaja com um dispositivo IoT Plug and Play Preview ligado à sua solução Azure IoT (Python) Microsoft Docs
description: Utilize python para ligar e interagir com um dispositivo IoT Plug e Play Preview que esteja ligado à sua solução Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87353007"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Quickstart: Interaja com um dispositivo IoT Plug e Play Preview que esteja ligado à sua solução (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug e Play Preview simplifica o IoT, permitindo-lhe interagir com o modelo de um dispositivo sem ter conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Python para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa do Python 3.7 na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [python.org](https://www.python.org/). Pode verificar a sua versão Python com o seguinte comando:  

```cmd/sh
python --version
```

Instale o [pacote de pré-visualização do serviço Python SDK](https://pypi.org/project/azure-iot-hub/2.2.1rc0/) executando o seguinte comando:

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, utilize um dispositivo termóstato de amostra, escrito em Python, como o dispositivo IoT Plug and Play. Para executar o dispositivo de amostragem:

1. Abra uma janela de terminal numa pasta à sua escolha. Executar o seguinte comando para clonar o repositório [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub para este local:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Esta janela de terminal é utilizada como terminal do seu **dispositivo.** Vá à pasta do seu repositório clonado e navegue para a pasta */azure-iot-sdk-python/azure-iot-device/samples/pnp.*

1. Configure a _cadeia de ligação_do dispositivo:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Executar o dispositivo termóstato da amostra com o seguinte comando:

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Estas mensagens indicam que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade. Não feche este terminal, precisa dele para confirmar que a amostra de serviço está a funcionar.

## <a name="run-the-sample-solution"></a>Executar a solução de amostra

Neste arranque rápido, você usa uma solução de IoT de amostra em Python para interagir com o dispositivo de amostra que acabou de configurar.

1. Abra outra janela do terminal para utilizar como terminal **de serviço.** O serviço SDK está em pré-visualização, por isso precisa clonar as amostras de um [ramo de pré-visualização do Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Vá à pasta deste ramo de repositório clonado e navegue para a pasta */azure-iot-sdk-python/azure-iot-hub/samples.*

1. Configure variáveis ambientais para o seu dispositivo ID e _ioT Hub cadeia de conexão_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. Na pasta das amostras, existem quatro ficheiros de amostra com um `pnp` prefixo. Estas amostras mostram como utilizar cada API para interagir com dispositivos IoT Plug e Play:

### <a name="get-digital-twin"></a>Obter twin digital

Utilize o seguinte comando no terminal **de serviço** para executar esta amostra:

```cmd/sh
python pnp_get_digital_twin_sample.py
```

A saída mostra o gémeo digital do dispositivo e imprime o seu ID modelo:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

O seguinte corte mostra o código de amostra de *pnp_get_digital_twin_sample.py:*

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

Esta amostra mostra-lhe como usar um *patch* para atualizar propriedades através do twin digital do seu dispositivo. O seguinte corte de *pnp_update_digital_twin_sample.py* mostra como construir o patch:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Utilize o seguinte comando no terminal **de serviço** para executar esta amostra:

```cmd/sh
python pnp_update_digital_twin_sample.py
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

Para invocar um comando, executar a amostra *de pnp_invoke_command_sample.py.* Esta amostra mostra como invocar um comando num simples dispositivo termóstato. Antes de executar esta amostra, desaprote as `IOTHUB_COMMAND_NAME` variáveis e `IOTHUB_COMMAND_PAYLOAD` ambientais no terminal **de serviço:**

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

No terminal **de serviço,** utilize o seguinte comando para executar a amostra:
  
```cmd/sh
python pnp_invoke_command_sample.py
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

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a uma solução IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de modelação IoT Plug e Play Preview](concepts-developer-guide.md)
