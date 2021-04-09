---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 2eff30333362d461f196972fbaedbeac8f2ae7c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033900"
---
## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma aplicação Azure IoT Central criada usando o modelo **de aplicação personalizado.** Para obter mais informações, veja [criar um início rápido da aplicação](../articles/iot-central/core/quick-deploy-iot-central.md). O pedido deve ter sido criado em ou depois de 14 de julho de 2020.
* Uma máquina de desenvolvimento com a versão [Python](https://www.python.org/) 3.7 ou posteriormente instalada. Pode correr `python --version` na linha de comando para verificar a sua versão. Python está disponível para uma grande variedade de sistemas operativos. As instruções neste tutorial assumem que está a executar o comando **python** no pedido de comando do Windows.
* Uma cópia local do repositório [Microsoft Azure IoT SDK para Python](https://github.com/Azure/azure-iot-sdk-python) GitHub que contém o código de amostra. Utilize este link para descarregar uma cópia do repositório: [Descarregue ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Em seguida, desaperte o ficheiro para um local adequado na sua máquina local.

## <a name="review-the-code"></a>Rever o código

Na cópia do Ficheiro Microsoft Azure IoT SDK para Python que descarregou anteriormente, abra o ficheiro *azure-iot-sdk-python/azure-iot-device/samples/pnp/simple_thermostat.py* num editor de texto.

Quando executar a amostra para ligar à IoT Central, utiliza o Serviço de Provisionamento de Dispositivos (DPS) para registar o dispositivo e gerar uma cadeia de ligação. A amostra recolhe a informação de ligação DPS de que necessita do ambiente da linha de comando.

A `main` função:

* Utiliza DPS para o fornecimento do dispositivo. A informação de fornecimento inclui o ID do modelo. A IoT Central utiliza o ID do modelo para identificar ou gerar o modelo do dispositivo para este dispositivo. Para saber mais, consulte [Associar um dispositivo com um modelo de dispositivo](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Cria um `Device_client` objeto e define o ID do modelo antes de abrir a `dtmi:com:example:Thermostat;1` ligação.
* Envia a `maxTempSinceLastReboot` propriedade para a IoT Central.
* Cria um ouvinte para o `getMaxMinReport` comando.
* Cria o ouvinte de propriedade, para ouvir atualizações de propriedade sujeira.
* Inicia um loop para enviar telemetria de temperatura a cada 10 segundos.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

A `provision_device` função utiliza DPS para forrar o dispositivo e registá-lo com a IoT Central. A função inclui o ID do modelo do dispositivo, que a IoT Central utiliza para [associar um dispositivo a um modelo de dispositivo,](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)na carga útil do provisionamento:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

A `execute_command_listener` função lida com pedidos de comando, executa a `max_min_handler` função quando o dispositivo recebe o `getMaxMinReport` comando e executa a `create_max_min_report_response` função para gerar a resposta:

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

O `async def execute_property_listener` handles writable propriedades atualizações de propriedade, tais como `targetTemperature` e gera a resposta JSON:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

A `send_telemetry_from_thermostat` função envia as mensagens de telemetria para a IoT Central:

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>Obter informações da ligação

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Executar o código

Para executar a aplicação da amostra, abra um ambiente de linha de comando e navegue para a pasta *azure-iot-sdk-python/azure-iot-device/samples/pnp* pasta que contém o ficheiro de amostra *simple_thermostat.py.*

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Instalar as embalagens necessárias:

```cmd/sh
pip install azure-iot-device
```

Execute o exemplo:

```cmd/sh
python simple_thermostat.py
```

A seguinte saída mostra o dispositivo a registar-se e a ligar-se à IoT Central. A amostra envia a `maxTempSinceLastReboot` propriedade antes de começar a enviar telemetria:

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Pode ver como o dispositivo responde a comandos e atualizações de propriedade:

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```
