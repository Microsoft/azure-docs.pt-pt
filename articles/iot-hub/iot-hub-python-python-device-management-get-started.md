---
title: Introdução ao gerenciamento de dispositivo do Hub IoT do Azure (Python) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT para iniciar uma reinicialização remota do dispositivo. Você usa o SDK do IoT do Azure para Python para implementar um aplicativo de dispositivo simulado que inclui um método direto e um aplicativo de serviço que invoca o método direto.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.openlocfilehash: 6d6a50db42924d868b57cacc415246ee6990859c
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110468"
---
# <a name="get-started-with-device-management-python"></a>Introdução ao gerenciamento de dispositivos (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Use o portal do Azure para criar um hub IoT e criar uma identidade de dispositivo no Hub IoT.

* Crie um aplicativo de dispositivo simulado que contenha um método direto que reinicie esse dispositivo. Os métodos diretos são invocados da nuvem.

* Crie um aplicativo de console do Python que chama o método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

No final deste tutorial, você tem dois aplicativos de console do Python:

* **dmpatterns_getstarted_device.py**, que se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula um reboot físico e reporta o tempo para o último reboot.

* **dmpatterns_getstarted_service.py**, que chama um método direto na aplicação simulada do dispositivo, exibe a resposta e exibe as propriedades reportadas atualizadas.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, pode:

* Criar um aplicativo de console do Python que responde a um método direto chamado pela nuvem

* Simular uma reinicialização do dispositivo

* Usar as propriedades relatadas para habilitar consultas de dispositivo de dispositivos para identificar os dispositivos e quando eles foram reinicializados pela última vez

1. Ao seu pedido de comando, execute o seguinte comando para instalar o pacote de **dispositivos azure-iot:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Utilizando um editor de texto, crie um ficheiro chamado **dmpatterns_getstarted_device.py** no seu diretório de trabalho.

3. Adicione as seguintes declarações `import` no início do ficheiro **dmpatterns_getstarted_device.py.**

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Adicione a **variável CONNECTION_STRING.** Substitua o valor do espaço reservado `{deviceConnectionString}` pela corda de ligação do dispositivo. Copiou esta cadeia de ligação anteriormente no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Adicione os seguintes retornos de chamada de função para implementar o método direto no dispositivo.

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

6. Inicie o ouvinte de método direto e aguarde.

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
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar políticas de retry (como um backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você cria um aplicativo de console do Python que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo de dispositivos para descobrir a hora da última reinicialização para esse dispositivo.

1. Ao seu pedido de comando, execute o seguinte comando para instalar o pacote **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Utilizando um editor de texto, crie um ficheiro chamado **dmpatterns_getstarted_service.py** no seu diretório de trabalho.

3. Adicione as seguintes declarações `import` no início do ficheiro **dmpatterns_getstarted_service.py.**

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Adicione as seguintes declarações de variável. Substitua o valor do espaço reservado `{IoTHubConnectionString}` pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string). Substitua o valor do espaço reservado `{deviceId}` pelo ID do dispositivo registado no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Adicione a seguinte função para invocar o método de dispositivo para reinicializar o dispositivo de destino e, em seguida, consulte o dispositivo gêmeos e obtenha a hora da última reinicialização.

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

Agora você está pronto para executar os aplicativos.

1. No prompt de comando, execute o comando a seguir para começar a escutar o método direto de reinicialização.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Em outro prompt de comando, execute o comando a seguir para disparar a reinicialização e a consulta remotas do dispositivo para localizar a hora da última reinicialização.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Você vê a resposta do dispositivo para o método direto no console.

   O seguinte mostra a resposta do dispositivo para o método direto de reinicialização:

   ![Saída de aplicativo de dispositivo simulado](./media/iot-hub-python-python-device-management-get-started/device.png)

   O seguinte mostra o serviço que está chamando o método direto de reinicialização e sondando o dispositivo de conexão para o status:

   ![Disparar saída do serviço de reinicialização](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]