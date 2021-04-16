---
title: Começa com a gestão de dispositivos Azure IoT Hub (Python) | Microsoft Docs
description: Como utilizar a gestão do dispositivo IoT Hub para iniciar um reboot remoto de dispositivos. Você usa o Azure IoT SDK para Python para implementar uma aplicação de dispositivo simulado que inclui um método direto e uma aplicação de serviço que invoca o método direto.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python, devx-track-azurecli
ms.openlocfilehash: c07d26715aff2c8dd5e00a7d7adbb548adf00a28
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482505"
---
# <a name="get-started-with-device-management-python"></a>Começar com a gestão de dispositivos (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal Azure para criar um Hub IoT e crie uma identidade de dispositivo no seu hub IoT.

* Crie uma aplicação de dispositivo simulado que contenha um método direto que reinicie esse dispositivo. Os métodos diretos são invocados da nuvem.

* Crie uma aplicação de consola Python que chame o método de reinício direto na aplicação do dispositivo simulado através do seu hub IoT.

No final deste tutorial, você tem duas aplicações de consola Python:

* **dmpatterns_getstarted_device.py**, que se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula um reboot físico e informa a hora do último reboot.

* **dmpatterns_getstarted_service.py**, que chama um método direto na aplicação do dispositivo simulado, exibe a resposta e exibe as propriedades relatadas atualizadas.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, pode:

* Crie uma aplicação de consola Python que responda a um método direto chamado pela nuvem

* Simular um reboot de dispositivo

* Utilize as propriedades reportadas para permitir consultas gémeas do dispositivo para identificar dispositivos e quando foram reiniciados pela última vez

1. No seu comando, executar o seguinte comando para instalar o pacote de **dispositivo azure-iot:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Utilizando um editor de texto, crie um ficheiro chamado **dmpatterns_getstarted_device.py** no seu diretório de trabalho.

3. Adicione as `import` seguintes declarações no início do ficheiro **dmpatterns_getstarted_device.py.**

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Adicione a variável **CONNECTION_STRING.** Substitua o `{deviceConnectionString}` valor do espaço reservado pela cadeia de ligação do dispositivo. Copiou esta cadeia de ligação anteriormente no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Adicione as seguintes chamadas de função para implementar o método direto no dispositivo.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Inicie o ouvinte do método direto e aguarde.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Guarde e feche o ficheiro **dmpatterns_getstarted_device.py.**

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar políticas de reequipante (como um backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Desencadeie um reboot remoto no dispositivo utilizando um método direto

Nesta secção, cria-se uma aplicação para consola Python que inicia um reboot remoto num dispositivo utilizando um método direto. A aplicação utiliza consultas gémeas do dispositivo para descobrir a última vez que reinicia o tempo para este dispositivo.

1. No seu comando, executar o seguinte comando para instalar o pacote **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Utilizando um editor de texto, crie um ficheiro chamado **dmpatterns_getstarted_service.py** no seu diretório de trabalho.

3. Adicione as `import` seguintes declarações no início do ficheiro **dmpatterns_getstarted_service.py.**

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Adicione as seguintes declarações variáveis. Substitua o `{IoTHubConnectionString}` valor do espaço reservado pela cadeia de ligação do hub IoT copiada anteriormente na cadeia de [ligação do hub IoT](#get-the-iot-hub-connection-string). Substitua o `{deviceId}` valor do espaço reservado pelo ID do dispositivo que registou no Registo de um novo dispositivo no hub [IoT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Adicione a seguinte função para invocar o método do dispositivo para reiniciar o dispositivo alvo, em seguida, consultar os gémeos do dispositivo e obter a última vez de reinicialização.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Guarde e feche o ficheiro **dmpatterns_getstarted_service.py.**

## <a name="run-the-apps"></a>Executar as aplicações

Agora está pronto para executar as aplicações.

1. Na pronta do comando, corram o seguinte comando para começar a ouvir o método direto de reinicialização.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Noutra solicitação de comando, executar o seguinte comando para ativar o reboot remoto e consultar o twin do dispositivo para encontrar a última hora de reinicialização.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Vê a resposta do dispositivo ao método direto na consola.

   O seguinte mostra a resposta do dispositivo ao método direto reiniciado:

   ![Saída de aplicação de dispositivo simulado](./media/iot-hub-python-python-device-management-get-started/device.png)

   O seguinte mostra o serviço que chama o método direto de reinicialização e sonda o dispositivo twin para o estado:

   ![Desencadear saída de serviço de reinicialização](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
