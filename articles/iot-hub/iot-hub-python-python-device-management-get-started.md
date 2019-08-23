---
title: Introdução ao gerenciamento de dispositivo do Hub IoT do Azure (Python) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT para iniciar uma reinicialização remota do dispositivo. Você usa o SDK do IoT do Azure para Python para implementar um aplicativo de dispositivo simulado que inclui um método direto e um aplicativo de serviço que invoca o método direto.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 287dbd3d6da4aa2bf5bd1da652cdeaeda3136321
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907748"
---
# <a name="get-started-with-device-management-python"></a>Introdução ao gerenciamento de dispositivos (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Use o portal do Azure para criar um hub IoT e criar uma identidade de dispositivo no Hub IoT.

* Crie um aplicativo de dispositivo simulado que contenha um método direto que reinicie esse dispositivo. Os métodos diretos são invocados da nuvem.

* Crie um aplicativo de console do Python que chama o método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

No final deste tutorial, você tem dois aplicativos de console do Python:

* **dmpatterns_getstarted_device. py**, que se conecta ao seu hub IOT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e informa a hora da última reinicialização.

* **dmpatterns_getstarted_service. py**, que chama um método direto no aplicativo de dispositivo simulado, exibe a resposta e exibe as propriedades relatadas atualizadas.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Veja a seguir as instruções de instalação para os pré-requisitos.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, pode:

* Criar um aplicativo de console do Python que responde a um método direto chamado pela nuvem

* Simular uma reinicialização do dispositivo

* Usar as propriedades relatadas para habilitar consultas de dispositivo de dispositivos para identificar os dispositivos e quando eles foram reinicializados pela última vez

1. No prompt de comando, execute o seguinte comando para instalar o pacote **Azure-IOT-Device-Client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > Os pacotes Pip para Azure-iothub-Service-Client e Azure-iothub-Device-Client estão atualmente disponíveis apenas para o sistema operacional Windows. Para o Linux/Mac OS, consulte as seções específicas do Linux e do Mac OS na postagem [preparar seu ambiente de desenvolvimento para o Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Usando um editor de texto, crie um arquivo chamado **dmpatterns_getstarted_device. py** em seu diretório de trabalho.

3. Adicione as instruções `import` a seguir no início do arquivo **dmpatterns_getstarted_device. py** .

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

4. Adicione variáveis, incluindo uma variável **seqüência_conexão** e a inicialização do cliente.  Substitua o `{deviceConnectionString}` valor do espaço reservado pela cadeia de conexão do dispositivo. Você copiou essa cadeia de conexão anteriormente no [registro de um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

5. Adicione os seguintes retornos de chamada de função para implementar o método direto no dispositivo.

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

6. Inicie o ouvinte de método direto e aguarde.

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. Salve e feche o arquivo **dmpatterns_getstarted_device. py** .

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você cria um aplicativo de console do Python que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo de dispositivos para descobrir a hora da última reinicialização para esse dispositivo.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **Azure-IOT-Service-Client** :

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Os pacotes Pip para Azure-iothub-Service-Client e Azure-iothub-Device-Client estão atualmente disponíveis apenas para o sistema operacional Windows. Para o Linux/Mac OS, consulte as seções específicas do Linux e do Mac OS na postagem [preparar seu ambiente de desenvolvimento para o Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Usando um editor de texto, crie um arquivo chamado **dmpatterns_getstarted_service. py** em seu diretório de trabalho.

3. Adicione as instruções `import` a seguir no início do arquivo **dmpatterns_getstarted_service. py** .

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

4. Adicione as seguintes declarações de variável. Substitua o `{IoTHubConnectionString}` valor do espaço reservado pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string). Substitua o `{deviceId}` valor do espaço reservado pela ID do dispositivo que você registrou em [registrar um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub).

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
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
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

6. Salve e feche o arquivo **dmpatterns_getstarted_service. py** .

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