---
title: Tutorial - Ligue uma aplicação genérica de cliente Python à Central Azure IoT [ Microsoft Docs
description: Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar um dispositivo que executa uma aplicação de cliente Python à sua aplicação Azure IoT Central. Cria um modelo de dispositivo importando um modelo de capacidade do dispositivo e adiciona ndo pontos de vista que lhe permitem interagir com um dispositivo conectado
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d9928a50f1f77cd59b83bd1af5a7a40715b6fa98
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673951"
---
# <a name="tutorial-create-and-connect-a-python-client-application-to-your-azure-iot-central-application-python"></a>Tutorial: Crie e ligue uma aplicação de cliente Python à sua aplicação Central Azure IoT (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar uma aplicação de cliente Python à sua aplicação Azure IoT Central. A aplicação Python simula o comportamento de um dispositivo de sensor ambiental. Você usa um modelo de capacidade de _dispositivo_ de amostra para criar um modelo de _dispositivo_ na IoT Central. Adiciona vistas ao modelo do dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar um modelo de capacidade de dispositivo para criar um modelo de dispositivo.
> * Adicione vistas padrão e personalizadas a um modelo de dispositivo.
> * Publique um modelo de dispositivo e adicione um dispositivo real à sua aplicação IoT Central.
> * Crie e execute o código do dispositivo Python e consulte-o ligado à sua aplicação IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Utilize uma vista para gerir as propriedades do dispositivo.
> * Ligue para comandos sincronizados e assíncronos para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma aplicação Azure IoT Central criada com o modelo de **aplicação Personalizado.** Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Uma máquina de desenvolvimento com a versão 3.7 da [Python](https://www.python.org/) ou posteriormente instalada. Pode correr `python3 --version` na linha de comando para verificar a sua versão. Python está disponível para uma grande variedade de sistemas operativos. As instruções deste tutorial pressupõem que está a executar o comando **python3** no pedido de comando do Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Criar uma aplicação Python

Os seguintes passos mostram-lhe como criar uma aplicação de cliente Python que se conecta ao dispositivo real que adicionou à aplicação. Esta aplicação Python simula o comportamento de um dispositivo real.

1. No ambiente da linha de `environmental-sensor` comando, navegue para a pasta que criou anteriormente.

1. Para instalar as bibliotecas necessárias, executar os seguintes comandos:

    ```cmd
    pip install azure-iot-device
    ```

1. Crie um ficheiro chamado `environmental-sensor` **environmental_sensor.py** na pasta.

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

1. Adicione ao ficheiro a seguinte `main` função assíncrona e declarações variáveis:

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

    Atualize os `{your Scope ID}`espaços `{your Device ID}`reservados, e `{your Primary Key}` com os valores que fez anteriormente. Numa aplicação real, não código duro esta informação na aplicação.

    Todas as seguintes definições de função e código estão aninhadas dentro da `main` função.

1. Adicione as seguintes duas `main` funções dentro da função para registar o dispositivo e conectá-lo à sua aplicação IoT Central. O registo utiliza o Serviço de Provisionamento de Dispositivos Azure:

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
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Adicione a seguinte `main` função dentro da função para enviar telemetria para a sua aplicação IoT Central:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    Os nomes dos artigos de`temp` `humid`telemetria (e) devem coincidir com os nomes utilizados no modelo do dispositivo.

1. Adicione as seguintes `main` funções dentro da função para manusear comandos chamados da sua aplicação IoT Central:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

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

    Os nomes dos`blink`comandos `turnoff`( `rundiagnostics`, `turnon`, e ) devem coincidir com os nomes utilizados no modelo do dispositivo.

    Atualmente, a IoT Central não utiliza o esquema de resposta definido no modelo de capacidade do dispositivo. Para um comando sincronizado, a carga útil de resposta pode ser qualquer JSON válido. Para um comando assíncrono, o dispositivo deve devolver imediatamente uma resposta de 202, seguida de atualização de propriedade reportada quando a obra estiver concluída. O formato da atualização de propriedade reportada é:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Um operador pode ver a carga de resposta no histórico do comando.

1. Adicione as seguintes `main` funções dentro da função para lidar com as atualizações de propriedade enviadas a partir da sua aplicação IoT Central:

    ```python
        sync def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

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

    Quando o operador define uma propriedade reempregável na aplicação IoT Central, a aplicação utiliza uma propriedade desejada por dispositivo para enviar o valor para o dispositivo. O dispositivo responde então usando uma propriedade reportada de gémeos dispositivos. Quando a IoT Central recebe o valor de propriedade reportado, atualiza a vista do imóvel com um estado de **sincronização.**

    Os nomes das`name` `brightness`propriedades (e) devem coincidir com os nomes utilizados no modelo do dispositivo.

1. Adicione as seguintes `main` funções dentro da função para controlar a aplicação:

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
        await device_client.patch_twin_reported_properties({'state': 'true'})
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

Para iniciar a aplicação do cliente do dispositivo, execute o seguinte comando no ambiente da linha de comando:

```cmd
python3 environmental_sensor.py
```

Pode ver que o dispositivo se liga à sua aplicação Central Azure IoT e começa a enviar telemetria:

![Executar a aplicação do cliente](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Pode ver como o dispositivo reage a comandos e atualizações de propriedades:

![Observe a aplicação do cliente](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os modelos de capacidade do dispositivo e como criar os seus próprios modelos de dispositivo, continue a orientar:

> [!div class="nextstepaction"]
> [Defina um novo tipo de dispositivo IoT](./howto-set-up-template.md)
