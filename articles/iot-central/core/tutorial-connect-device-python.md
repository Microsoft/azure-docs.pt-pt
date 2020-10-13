---
title: Tutorial - Ligue uma aplicação genérica do cliente Python ao Azure IoT Central Microsoft Docs
description: Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar um dispositivo que executa uma aplicação de cliente Python à sua aplicação Azure IoT Central. Cria um modelo de dispositivo importando um modelo de capacidade do dispositivo e adiciona vistas que lhe permitem interagir com um dispositivo conectado
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 1be7087e99ca2e4dc605a8d1c6b9821567aecfef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968125"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Tutorial: Criar e ligar uma aplicação de cliente à sua aplicação Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar uma aplicação do cliente Python à sua aplicação Azure IoT Central. A aplicação Python simula o comportamento de um dispositivo de sensor ambiental. Utilize um _modelo de capacidade do dispositivo_ de amostra para criar um modelo de _dispositivo_ na IoT Central. Adiciona vistas ao modelo do dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importe um modelo de capacidade do dispositivo para criar um modelo de dispositivo.
> * Adicione vistas padrão e personalizadas a um modelo de dispositivo.
> * Publique um modelo de dispositivo e adicione um dispositivo real à sua aplicação IoT Central.
> * Crie e execute o código do dispositivo Python e veja-o ligar-se à sua aplicação IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Utilize uma vista para gerir as propriedades do dispositivo.
> * Chame comandos sincronizados e assíncronos para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma aplicação Azure IoT Central criada usando o modelo **de aplicação personalizado.** Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md). A aplicação deve ter sido criada em ou depois de 07/14/2020.
* Uma máquina de desenvolvimento com a versão [Python](https://www.python.org/) 3.7 ou posteriormente instalada. Pode correr `python3 --version` na linha de comando para verificar a sua versão. Python está disponível para uma grande variedade de sistemas operativos. As instruções neste tutorial assumem que está a executar o comando **python3** no pedido de comando do Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Criar uma aplicação Python

Os passos seguintes mostram-lhe como criar uma aplicação do cliente Python que se conecta ao dispositivo real que adicionou à aplicação. Esta aplicação Python simula o comportamento de um dispositivo real.

1. No seu ambiente de linha de comando, navegue para a `environmental-sensor` pasta que criou anteriormente.

1. Para instalar as bibliotecas necessárias, execute os seguintes comandos:

    ```cmd
    pip install azure-iot-device
    ```

1. Crie um ficheiro chamado **environmental_sensor.py** na `environmental-sensor` pasta.

1. Adicione as `import` seguintes declarações no início do ficheiro **environmental_sensor.py:**

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Adicione ao ficheiro a seguinte função assíncronea `main` e declarações variáveis:

    ```python
    async def main():
        # In a production environment, don't store
        # connection information in the code.
        provisioning_host = 'global.azure-devices-provisioning.net'
        id_scope = '{your Scope ID}'
        registration_id = '{your Device ID}'
        symmetric_key = '{your Primary Key}'
  
        delay = 2
  
        # All the remaining code is nested within this main function

    if __name__ == '__main__':
        asyncio.run(main())
    ```

    Atualize os espaços `{your Scope ID}` reservados, e com os `{your Device ID}` `{your Primary Key}` valores que fez anteriormente. Numa aplicação real, não código duro esta informação na aplicação.

    Todas as seguintes definições de funções e código estão aninhados na `main` função.

1. Adicione as duas funções seguintes dentro da `main` função para registar o dispositivo e conectá-lo à sua aplicação IoT Central. O registo utiliza o Serviço de Provisionamento de Dispositivos Azure:

    ```python
        async def register_device():
            provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
                provisioning_host=provisioning_host,
                registration_id=registration_id,
                id_scope=id_scope,
                symmetric_key=symmetric_key,
            )

            registration_result = await provisioning_device_client.register()

            print(f'Registration result: {registration_result.status}')

            return registration_result
  
        async def connect_device():
            device_client = None
            try:
                registration_result = await register_device()
                if registration_result.status == 'assigned':
                    device_client = IoTHubDeviceClient.create_from_symmetric_key(
                        symmetric_key=symmetric_key,
                        hostname=registration_result.registration_state.assigned_hub,
                        device_id=registration_result.registration_state.device_id
                    )
                    # Connect the client.
                    await device_client.connect()
                    print('Device connected successfully')
            finally:
                return device_client
    ```

1. Adicione a seguinte função dentro da `main` função para enviar telemetria à sua aplicação IoT Central:

    ```python
        async def send_telemetry():
            print(f'Sending telemetry from the provisioned device every {delay} seconds')
            while True:
                temp = random.randrange(1, 75)
                humid = random.randrange(30, 99)
                payload = json.dumps(
                    {
                        'temp': temp,
                        'humid': humid
                    })
                msg = Message(payload)
                await device_client.send_message(msg, )
                print(f'Sent message: {msg}')
                await asyncio.sleep(delay)
    ```

    Os nomes dos artigos de telemetria `temp` `humid` (e) devem corresponder aos nomes utilizados no modelo do dispositivo.

1. Adicione as seguintes funções dentro da `main` função para manusear comandos chamados da sua aplicação IoT Central:

    ```python
        async def blink_command(request):
            print('Received synchronous call to blink')
            response = MethodResponse.create_from_method_request(
                request,
                status = 200,
                payload = {'description': f'Blinking LED every {request.payload} seconds'}
            )
            await device_client.send_method_response(response)  # send response
            print(f'Blinking LED every {request.payload} seconds')

        async def diagnostics_command(request):
            print('Starting asynchronous diagnostics run...')
            response = MethodResponse.create_from_method_request(
                request,
                status = 202
            )
            await device_client.send_method_response(response)  # send response
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Sending property update to confirm command completion')
            await device_client.patch_twin_reported_properties(
                {
                    'rundiagnostics':
                    {
                        'value': f'Diagnostics run complete at {datetime.datetime.today()}.'
                    }
                })

        async def turnon_command(request):
            print('Turning on the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        async def turnoff_command(request):
            print('Turning off the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        commands = {
            'blink': blink_command,
            'rundiagnostics': diagnostics_command,
            'turnon': turnon_command,
            'turnoff': turnoff_command,
        }

        # Define behavior for handling commands
        async def command_listener():
            while True:
                method_request = await device_client.receive_method_request()  # Wait for commands
                await commands[method_request.name](method_request)
    ```

    Os nomes dos comandos ( `blink` , , , e ) devem corresponder aos `turnon` `turnoff` `rundiagnostics` nomes utilizados no modelo do dispositivo.

    Atualmente, a IoT Central não utiliza o esquema de resposta definido no modelo de capacidade do dispositivo. Para um comando sincronizado, a carga útil de resposta pode ser qualquer JSON válido. Para um comando assíncrona, o dispositivo deve devolver imediatamente uma resposta 202, seguida de uma atualização de propriedade reportada quando o trabalho estiver concluído. O formato da atualização de propriedade reportada é:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Um operador pode ver a carga útil de resposta no histórico de comando.

1. Adicione as seguintes funções dentro da `main` função para lidar com as atualizações de propriedade enviadas a partir da sua aplicação IoT Central. A mensagem que o dispositivo envia em resposta à [atualização de propriedade escrita](concepts-telemetry-properties-commands.md#writeable-property-types) deve incluir os `av` campos e `ac` campos. O `ad` campo é opcional:

    ```python
        async def name_setting(value, version):
            await asyncio.sleep(1)
            print(f'Setting name value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'name' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        async def brightness_setting(value, version):
            await asyncio.sleep(5)
            print(f'Setting brightness value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'brightness' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        settings = {
            'name': name_setting,
            'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
            while True:
                patch = await device_client.receive_twin_desired_properties_patch() # blocking
                to_update = patch.keys() & settings.keys()
                await asyncio.gather(
                    *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
                )
    ```

    Quando o operador define uma propriedade escrita na aplicação IoT Central, a aplicação utiliza um dispositivo que dois propriedades desejadas para enviar o valor para o dispositivo. Em seguida, o dispositivo responde utilizando uma propriedade reportada por gémeos do dispositivo. Quando a IoT Central recebe o valor da propriedade reportada, atualiza a vista da propriedade com um estado de **sincronização.**

    Os nomes das propriedades `name` `brightness` (e) devem corresponder aos nomes utilizados no modelo do dispositivo.

1. Adicione as seguintes funções dentro da `main` função para controlar a aplicação:

    ```python
        # Define behavior for halting the application
        def stdin_listener():
            while True:
                selection = input('Press Q to quit\n')
                if selection == 'Q' or selection == 'q':
                    print('Quitting...')
                    break
  
        device_client = await connect_device()
  
        if device_client is not None and device_client.connected:
            print('Send reported properties on startup')
            await device_client.patch_twin_reported_properties(
                {
                    'state': 'true',
                    'processorArchitecture': 'ARM',
                    'swVersion': '1.0.0'
                })
            tasks = asyncio.gather(
                send_telemetry(),
                command_listener(),
                twin_patch_listener(),
            )

            # Run the stdin listener in the event loop
            loop = asyncio.get_running_loop()
            user_finished = loop.run_in_executor(None, stdin_listener)

            # Wait for user to indicate they are done listening for method calls
            await user_finished

            # Cancel tasks
            tasks.add_done_callback(lambda r: r.exception())
            tasks.cancel()
            await device_client.disconnect()
  
        else:
            print('Device could not connect')
    ```

1. Guarde o ficheiro **environmental_sensor.py.**

## <a name="run-your-python-application"></a>Executar a sua aplicação Python

Para iniciar a aplicação do cliente do dispositivo, execute o seguinte comando no seu ambiente de linha de comando:

```cmd
python3 environmental_sensor.py
```

Pode ver que o dispositivo se conecta à sua aplicação Azure IoT Central e começa a enviar telemetria:

![Executar a aplicação do cliente](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Pode ver como o dispositivo responde a comandos e atualizações de propriedade:

![Observar a aplicação do cliente](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>Ver dados brutos

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Passos seguintes

Se preferir continuar através do conjunto de tutoriais IoT Central e aprender mais sobre a construção de uma solução IoT Central, consulte:

> [!div class="nextstepaction"]
> [Criar um modelo de dispositivo de gateway](./tutorial-define-gateway-device-type.md)

Como desenvolvedor de dispositivos, agora que aprendeu o básico de como criar um dispositivo usando Python, alguns dos próximos passos sugeridos são:

* Leia [Quais são os modelos do dispositivo para](./concepts-device-templates.md) saber mais sobre o papel dos modelos do dispositivo quando estiver a implementar o código do dispositivo.
* Leia [Se ligar à Azure IoT Central](./concepts-get-connected.md) para saber mais sobre como registar os dispositivos com a IoT Central e como a IoT Central assegura as ligações do dispositivo.