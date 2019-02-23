---
title: Introdução à gestão de dispositivos do IoT Hub do Azure (Python) | Documentos da Microsoft
description: Como utilizar a gestão de dispositivos do IoT Hub para iniciar um reinício do dispositivo remoto. Utilize o SDK do IoT do Azure para Python para implementar uma aplicação de dispositivo simulado que inclui um método direto e um serviço de aplicações que invoca o método direto.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7b2876f30af23f73be5a53770ac75ef323d90c37
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668982"
---
# <a name="get-started-with-device-management-python"></a>Introdução à gestão de dispositivo (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal do Azure para criar um IoT Hub e criar uma identidade de dispositivo no IoT hub.
* Crie uma aplicação de dispositivo simulado que contém um método direto que reinicia o que o dispositivo. Os métodos diretos são invocados a partir da cloud.
* Crie uma aplicação de consola Python que chama o método direto de reinício no aplicativo do dispositivo simulado através do IoT hub.

No final deste tutorial, tem duas aplicações de consola Python:

**dmpatterns_getstarted_device.PY**, que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e reporta o tempo para a última reinicialização.

**dmpatterns_getstarted_service.PY**, que chama um método direto na aplicação do dispositivo simulado, apresenta a resposta e apresenta a atualização de propriedades comunicadas.

Para concluir este tutorial, precisa do seguinte:

* [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python à variável de ambiente específica da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python](https://pip.pypa.io/en/stable/installing/).
    * Instalar o [azure-iothub-dispositivo-cliente](https://pypi.org/project/azure-iothub-device-client/) empacotar, utilizando o comando   `pip install azure-iothub-device-client`
    * Instalar o [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) empacotar, utilizando o comando   `pip install azure-iothub-service-client`
* Se estiver a utilizar o SO Windows, o [pacote redistribuível Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145), para permitir a utilização de DLLs nativas de Python.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, irá:

* Criar uma aplicação de consola Python que responde a um método direto chamado pela cloud
* Simular um reinício do dispositivo
* Utilize as propriedades comunicadas para ativar consultas de gémeos de dispositivo identificar os dispositivos e quando duram reiniciada

1. Com um editor de texto, crie uma **dmpatterns_getstarted_device.py** ficheiro.

1. Adicione as seguintes `import` instruções no início do **dmpatterns_getstarted_device.py** ficheiro.
   
    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

1. Adicionar variáveis, incluindo uma **CONNECTION_STRING** variável e a inicialização do cliente.  Substitua a cadeia de ligação com a cadeia de ligação do dispositivo.  
   
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

1. Adicione os retornos de chamada de função seguinte para implementar o método direto no dispositivo.
   
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

1. Iniciar o serviço de escuta do método direto e aguarde.
   
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

1. Guarde e feche o **dmpatterns_getstarted_device.py** ficheiro.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Acionar a reinicialização remota no dispositivo com um método direto
Nesta secção, vai criar uma aplicação de consola Python que inicia uma reinicialização remota num dispositivo através de um método direto. A aplicação utiliza consultas de dispositivo duplo para detetar a última hora de reinício desse dispositivo.

1. Com um editor de texto, crie uma **dmpatterns_getstarted_service.py** ficheiro.

1. Adicione as seguintes `import` instruções no início do **dmpatterns_getstarted_service.py** ficheiro.
   
    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

1. Adicione as seguintes declarações de variável. Apenas substituir valores de marcador de posição para _IoTHubConnectionString_ e _deviceId_.
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Adicione a seguinte função para invocar o método de dispositivo para reiniciar o dispositivo de destino, em seguida, consultar os dispositivos duplos e obtenha a última hora de reinício.
   
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

1. Guarde e feche o **dmpatterns_getstarted_service.py** ficheiro.


## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.

1. No prompt de comando, execute o seguinte comando para começar a escutar o método direto de reinício.
   
    ```
    python dmpatterns_getstarted_device.py
    ```

1. Outra linha de comandos, execute o seguinte comando para acionar a reinicialização remota e a consulta para o dispositivo duplo localizar a última hora de reinício.
   
    ```
    python dmpatterns_getstarted_service.py
    ```

1. Ver a resposta de dispositivo para o método direto na consola do.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
