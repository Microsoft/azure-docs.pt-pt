---
title: Começar com gémeos de dispositivo Shub Azure IoT (Python) / Microsoft Docs
description: Como utilizar gémeos de dispositivo SoT Hub Azure ioT para adicionar tags e, em seguida, usar uma consulta IoT Hub. Utiliza os SDKs Azure IoT para python para implementar a aplicação simulada de dispositivos e uma aplicação de serviço que adiciona as tags e executa a consulta IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: a6210c4672042801350e56ef6c8e8a2c02420a81
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110390"
---
# <a name="get-started-with-device-twins-python"></a>Começar com gémeos dispositivo (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terás duas aplicações de consola Python:

* **AddTagsAndQuery.py**, uma app python back-end, que adiciona tags e consultas de gémeos dispositivos.

* **ReportConnectivity.py**, uma aplicação Python, que simula um dispositivo que se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, e relata a sua condição de conectividade.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar a app de serviço

Nesta secção, cria-se uma aplicação de consola Python que adiciona metadados de localização ao dispositivo twin associado ao seu **{Device ID}** . Em seguida, questiona os gémeos do dispositivo armazenados no hub IoT selecionando os dispositivos localizados em Redmond, e depois os que estão reportando uma ligação celular.

1. No seu diretório de trabalho, abra um pedido de comando e instale o **Azure IoT Hub Service SDK para Python**.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > O pacote pip para o cliente de serviço azure-iothub está atualmente disponível apenas para o Windows OS. Para O Sistema Linux/Mac OS, consulte as secções específicas do Linux e do Mac OS no posto de [desenvolvimento prepare o seu ambiente](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) de desenvolvimento para python.
   >

2. Utilizando um editor de texto, crie um novo ficheiro **AddTagsAndQuery.py.**

3. Adicione o código seguinte para importar os módulos necessários do SDK do serviço:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Adicione o seguinte código. Substitua `[IoTHub Connection String]` com a cadeia de ligação do hub IoT que copiou na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string). Substitua `[Device Id]` com o ID do dispositivo registado no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Adicione o seguinte código ao ficheiro **AddTagsAndQuery.py:**

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

    O objeto **de registo** expõe todos os métodos necessários para interagir com gémeos dispositivos do serviço. O código inicializa primeiro o objeto **de Registo,** depois atualiza o dispositivo twin para **o dispositivoId**, e finalmente executa duas consultas. O primeiro seleciona apenas os gémeos dispositivos localizados na fábrica **redmond43,** e o segundo refina a consulta para selecionar apenas os dispositivos que também estão ligados através da rede celular.

6. Adicione o seguinte código no final do **AddTagsAndQuery.py** para implementar a função **iothub_service_sample_run:**

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Executar a aplicação com:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Deve ver um dispositivo nos resultados da consulta que pede todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que utilizam uma rede celular.

    ![primeira consulta mostrando todos os dispositivos em Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

Na secção seguinte, cria-se uma aplicação de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação do dispositivo

Nesta secção, cria-se uma aplicação de consola Python que se conecta ao seu hub como **o id} {Device}** e, em seguida, atualiza as propriedades reportadas do seu dispositivo twin para conter a informação de que está conectado usando uma rede celular.

1. A partir de um pedido de comando no seu diretório de trabalho, instale o **Dispositivo Hub Azure IoT SDK para Python:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Utilizando um editor de texto, crie um novo ficheiro **ReportConnectivity.py.**

3. Adicione o seguinte código para importar os módulos necessários do dispositivo SDK:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Adicione o seguinte código. Substitua o valor do espaço reservado `[IoTHub Device Connection String]` com a cadeia de ligação do dispositivo que copiou no [Registo um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Adicione o seguinte código ao ficheiro **ReportConnectivity.py** para implementar a funcionalidade dos gémeos do dispositivo:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    O objeto **Cliente** expõe todos os métodos necessários para interagir com gémeos dispositivos do dispositivo. O código anterior, depois de inicializar o objeto **Cliente,** recupera o dispositivo twin para o seu dispositivo e atualiza a sua propriedade reportada com a informação de conectividade.

6. Adicione o seguinte código no final do **ReportConnectivity.py** para implementar a função **iothub_client_sample_run:**

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Executar a aplicação do dispositivo:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Devias ver a confirmação de que os gémeos do dispositivo foram atualizados.

    ![atualizar gémeos](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Agora que o dispositivo reportou a sua informação de conectividade, deve aparecer em ambas as consultas. Volte e volte a fazer as consultas:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Desta vez, o **ID do {Dispositivo deve** aparecer em ambos os resultados da consulta.

    ![segunda consulta](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionou metadados de dispositivos como tags de uma aplicação de back-end e escreveu uma aplicação simulada de dispositivo para relatar informações de conectividade do dispositivo no dispositivo twin. Também aprendeu a consultar esta informação usando o registo.

Utilize os seguintes recursos para aprender a:

* Envie telemetria a partir de dispositivos com o [Get started com ioT Hub](quickstart-send-telemetry-python.md) tutorial.

* Configure os dispositivos utilizando as propriedades desejadas pelo dispositivo Twin com as [propriedades desejadas para configurar](tutorial-device-twins.md) o tutorial dos dispositivos.

* dispositivos de controlo interactivamente (como ligar um ventilador a partir de uma aplicação controlada pelo utilizador), com o tutorial [de métodos diretos use.](quickstart-control-device-python.md)
