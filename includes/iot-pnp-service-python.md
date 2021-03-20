---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b861baea93c2c57b8f66ebac928a7e4fd3adfa8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95510609"
---
O IoT Plug and Play simplifica o IoT, permitindo-lhe interagir com o modelo de um dispositivo sem ter conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Python para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

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

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

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

1. Abra o ficheiro *registry_manager_pnp_sample.py* e reveja o código. Esta amostra mostra como utilizar a classe **IoTHubRegistryManager** para interagir com o seu dispositivo IoT Plug and Play.

> [!NOTE]
> Estas amostras de serviço utilizam a classe **IoTHubRegistryManager** do cliente de **serviço IoT Hub.** Para saber mais sobre as APIs, incluindo as gémeas digitais API, consulte o [guia de desenvolvedores de serviços.](../articles/iot-pnp/concepts-developer-guide-service.md)

### <a name="get-the-device-twin"></a>Obter o dispositivo gémeo

Em [Configurar o seu ambiente para os quickstarts e tutoriais IoT Plug and Play](../articles/iot-pnp/set-up-environment.md) criou duas variáveis ambientais para configurar a amostra para ligar ao seu hub e dispositivo IoT:

* **IOTHUB_CONNECTION_STRING**: a cadeia de ligação do hub IoT que fez uma nota anterior.
* **IOTHUB_DEVICE_ID:** `"my-pnp-device"` .

Utilize o seguinte comando no terminal **de serviço** para executar esta amostra:

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Se estiver a analisar esta amostra no Linux, use `export` no lugar de `set` .

A saída mostra o dispositivo twin e imprime o seu ID modelo:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

O seguinte corte mostra o código de amostra de *registry_manager_pnp_sample.py:*

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Atualizar um dispositivo gémeo

Esta amostra mostra-lhe como atualizar a `targetTemperature` propriedade writable no dispositivo:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

Pode verificar se a atualização é aplicada no terminal do **dispositivo** que mostra a seguinte saída:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

O terminal **de serviço** confirma que o patch foi bem sucedido:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Invocar um comando

A amostra invoca então um comando:

O terminal **de serviço** mostra uma mensagem de confirmação do dispositivo:

```cmd/sh
The device method has been successfully invoked
```

No terminal do **dispositivo,** vê-se que o dispositivo recebe o comando:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
