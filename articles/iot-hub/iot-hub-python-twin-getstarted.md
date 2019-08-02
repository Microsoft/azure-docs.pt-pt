---
title: Introdução ao dispositivo gêmeos (Python) do Hub IoT do Azure | Microsoft Docs
description: Como usar o dispositivo gêmeos do Hub IoT do Azure para adicionar marcas e, em seguida, usar uma consulta do Hub IoT. Use os SDKs do IoT do Azure para Python para implementar o aplicativo de dispositivo simulado e um aplicativo de serviço que adiciona as marcas e executa a consulta do Hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 62385f4bd07f4b80dc3d571d409e16c7e0dca205
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667835"
---
# <a name="get-started-with-device-twins-python"></a>Introdução ao dispositivo gêmeos (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, você terá dois aplicativos de console do Python:

* **AddTagsAndQuery.py**, um aplicativo de back-end Python, que adiciona marcas e consultas ao dispositivo gêmeos.

* **ReportConnectivity.py**, um aplicativo Python, que simula um dispositivo que se conecta ao Hub IOT com a identidade do dispositivo criada anteriormente e relata sua condição de conectividade.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Veja a seguir as instruções de instalação para os pré-requisitos.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo de serviço

Nesta seção, você criará um aplicativo de console do Python que adiciona metadados de local para o dispositivo de entrelaçamento associado à **{Device ID}** . Em seguida, ele consulta o dispositivo gêmeos armazenado no Hub IoT selecionando os dispositivos localizados em Redmond e, em seguida, aqueles que estão relatando uma conexão celular.

1. Abra uma linha de comandos e instale o **SDK do Serviço Hub IoT do Azure para Python**. Depois de instalar o SDK, feche a linha de comandos.

   ```
   pip install azure-iothub-service-client
   ```

2. Usando um editor de texto, crie um novo arquivo **AddTagsAndQuery.py** .

3. Adicione o código seguinte para importar os módulos necessários do SDK do serviço:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Adicione o código a seguir, substituindo o `[IoTHub Connection String]` espaço `[Device Id]` reservado para e pela cadeia de conexão do Hub IOT e a ID do dispositivo que você criou nas seções anteriores.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Adicione o seguinte código ao arquivo **AddTagsAndQuery.py** :

     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    O objeto **Registry** expõe todos os métodos necessários para interagir com o dispositivo gêmeos do serviço. O código inicializa o objeto **do registro** primeiro e, em seguida, atualizao dispositivo de atualização para DeviceID e, finalmente, executa duas consultas. O primeiro seleciona apenas o dispositivo gêmeos de dispositivos localizados na fábrica **Redmond43** e o segundo refina a consulta para selecionar apenas os dispositivos que também estão conectados por meio da rede celular.

6. Adicione o seguinte código ao final de **AddTagsAndQuery.py** para implementar a função **iothub_service_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Execute o aplicativo com:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Você deve ver um dispositivo nos resultados para a consulta solicitando todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.

    ![primeira consulta mostrando todos os dispositivos em Redmond](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

Na próxima seção, você cria um aplicativo de dispositivo que relata as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo

Nesta seção, você cria um aplicativo de console do Python que se conecta ao seu hub como **{Device ID}** e, em seguida, atualiza suas propriedades relatadas do seu dispositivo para conter as informações que ele está conectado usando uma rede de celular.

1. Abra uma linha de comandos e instale o **SDK do Serviço Hub IoT do Azure para Python**. Depois de instalar o SDK, feche a linha de comandos.

    ```
    pip install azure-iothub-device-client
    ```

2. Usando um editor de texto, crie um novo arquivo **ReportConnectivity.py** .

3. Adicione o código seguinte para importar os módulos necessários do SDK do serviço:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Adicione o código a seguir, substituindo o `[IoTHub Device Connection String]` espaço reservado por pela cadeia de conexão do dispositivo do Hub IOT criado nas seções anteriores.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Adicione o seguinte código ao arquivo **ReportConnectivity.py** para implementar a funcionalidade do dispositivo gêmeos:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    O objeto **cliente** expõe todos os métodos necessários para interagir com dispositivos gêmeos do dispositivo. O código anterior, depois de inicializar o objeto **Client** , recupera o dispositivo de atualização para o dispositivo e atualiza sua propriedade relatada com as informações de conectividade.

6. Adicione o seguinte código ao final de **ReportConnectivity.py** para implementar a função **iothub_client_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Execute o aplicativo do dispositivo:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Você deve ver a confirmação de que o dispositivo gêmeos foi atualizado.

    ![atualizar gêmeos](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. Agora que o dispositivo relatou suas informações de conectividade, ele deve aparecer em ambas as consultas. Volte e execute as consultas novamente:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Desta vez, o **{Device ID}** deve aparecer nos dois resultados da consulta.

    ![segunda consulta](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Você adicionou metadados de dispositivo como marcas de um aplicativo de back-end e gravou um aplicativo de dispositivo simulado para relatar informações de conectividade do dispositivo no dispositivo. Você também aprendeu como consultar essas informações usando o registro.

Use os seguintes recursos para aprender a:

* Envie telemetria de dispositivos com o tutorial introdução [ao Hub IOT](quickstart-send-telemetry-python.md) .

* Configure dispositivos usando as propriedades desejadas do dispositivo ' s ' com o tutorial [usar propriedades desejadas para configurar dispositivos](tutorial-device-twins.md) .

* Controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado pelo usuário), com o tutorial [usar métodos diretos](quickstart-control-device-python.md) .