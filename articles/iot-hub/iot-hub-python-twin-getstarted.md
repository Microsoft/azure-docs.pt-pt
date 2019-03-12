---
title: Introdução aos duplos de dispositivo do IoT Hub do Azure (Python) | Documentos da Microsoft
description: Como utilizar o gémeos de dispositivo do IoT Hub do Azure para adicionar etiquetas e, em seguida, utilizar uma consulta do IoT Hub. Utilize os SDKs IoT do Azure para Python para implementar a aplicação de dispositivo simulado e uma aplicação de serviço que adiciona as etiquetas e executa a consulta do IoT Hub.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: kgremban
ms.openlocfilehash: edf6fa98224613ba31eeed871cbb0eaf4e614600
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535302"
---
# <a name="get-started-with-device-twins-python"></a>Introdução aos dispositivos duplos (Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terá duas aplicações de consola Python:

* **AddTagsAndQuery.py**, uma aplicação de back-end do Python, que adiciona as etiquetas e consultas de gémeos de dispositivo.
* **ReportConnectivity.py**, uma aplicação de Python, que simula um dispositivo que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente e reporta sua condição de conectividade.

> [!NOTE]
> O artigo [do Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivos e back-end.

Para concluir este tutorial, precisa do seguinte:

* [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python à variável de ambiente específica da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python](https://pip.pypa.io/en/stable/installing/).
* Se estiver a utilizar o SO Windows, o [pacote redistribuível Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145), para permitir a utilização de DLLs nativas de Python.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

> [!NOTE]
> Os pacotes *pip* para `azure-iothub-service-client` e `azure-iothub-device-client` estão atualmente disponíveis apenas para o SO Windows. Para o SO Linux/Mac, consulte as secções de Linux e Mac específicas do sistema operacional sobre o [preparar o ambiente de desenvolvimento para Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) publicar.
> 

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Criar a aplicação de serviço
Nesta secção, vai criar uma aplicação de consola Python que adiciona os metadados de localização para o dispositivo duplo associado com seu **{ID de dispositivo}**. Ele consulta, em seguida, os dispositivos duplos armazenados no hub IoT selecionar os dispositivos localizados em Redmond e, em seguida, aqueles que estiverem a comunicar uma conexão por celular.

1. Abra uma linha de comandos e instale o **SDK do Serviço Hub IoT do Azure para Python**. Depois de instalar o SDK, feche a linha de comandos.

    ```
    pip install azure-iothub-service-client
    ```

1. Com um editor de texto, crie um novo **AddTagsAndQuery.py** ficheiro.

3. Adicione o código seguinte para importar os módulos necessários do SDK do serviço:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. Adicione o código seguinte, substituindo o marcador de posição para `[IoTHub Connection String]` e `[Device Id]` com a cadeia de ligação para o hub IoT e o ID de dispositivo que criou nas secções anteriores.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

1. Adicione o seguinte código para o **AddTagsAndQuery.py** ficheiro:
   
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
   
    O **Registro** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do serviço. O código inicializa primeiro o **Registro** objeto, em seguida, as atualizações do dispositivo duplo para **deviceId**, e, finalmente, executa duas consultas. A primeira seleciona apenas os dispositivos duplos de dispositivos localizados no **Redmond43** plant e o segundo Refina a consulta para selecionar apenas os dispositivos que também estão ligados através de rede celular.
   
1. Adicione o seguinte código no final da **AddTagsAndQuery.py** para implementar a **iothub_service_sample_run** função:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

1. Execute a aplicação com:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Deverá ver um dispositivo nos resultados para fazer a consulta para todos os dispositivos localizados num **Redmond43** e nenhuma durante a consulta que restringe os resultados para os dispositivos que utilizam uma rede celular.
   
    ![primeira consulta][1]

A secção seguinte, vai criar uma aplicação de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação de dispositivo
Nesta secção, vai criar uma aplicação de consola Python que liga ao seu hub como sua **{ID de dispositivo}** e, em seguida, atualizações de seu dispositivo duplo do comunicado propriedades para conter as informações que está ligado através de uma rede celular.

1. Abra uma linha de comandos e instale o **SDK do Serviço Hub IoT do Azure para Python**. Depois de instalar o SDK, feche a linha de comandos.

    ```
    pip install azure-iothub-device-client
    ```

1. Com um editor de texto, crie um novo **ReportConnectivity.py** ficheiro.

3. Adicione o código seguinte para importar os módulos necessários do SDK do serviço:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. Adicione o código seguinte, substituindo o marcador de posição para `[IoTHub Device Connection String]` com a cadeia de ligação para o dispositivo do hub IoT que criou nas secções anteriores.
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. Adicione o seguinte código para o **ReportConnectivity.py** ficheiro para implementar o dispositivo twins funcionalidade:

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

    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. O código anterior, depois, ele inicializa o **cliente** de objeto, obtém o dispositivo duplo para o seu dispositivo e atualiza a sua propriedade comunicada com as informações de conectividade.

1. Adicione o seguinte código no final da **ReportConnectivity.py** para implementar a **iothub_client_sample_run** função:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. Executar a aplicação de dispositivo
   
    ```cmd/sh
    python ReportConnectivity.py
    ```
   
    Deverá ver os dispositivos duplos foram atualizados de confirmação.

    ![gémeos de atualização][2]

6. Agora que o dispositivo comunicado suas informações de conectividade, deverá aparecer em ambas as consultas. Voltar atrás e execute novamente as consultas:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Desta vez sua **{ID de dispositivo}** deve aparecer em ambos os resultados da consulta.
   
    ![segunda consulta][3]

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionados metadados do dispositivo como etiquetas a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo simulado para informações de conectividade do dispositivo de relatório no dispositivo duplo. Também aprendeu como consultar essas informações usando o Registro.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub](quickstart-send-telemetry-python.md) tutorial,
* configurar dispositivos com as propriedades pretendidas do duplo do dispositivo com o [utilização pretendida propriedades para configurar dispositivos](tutorial-device-twins.md) tutorial,
* Controlar dispositivos interativamente (como ativar um fã de uma aplicação controlada pelo utilizador), com o [utilizar métodos diretos](quickstart-control-device-python.md) tutorial.

<!-- images -->
[1]: media/iot-hub-python-twin-getstarted/1.png
[2]: media/iot-hub-python-twin-getstarted/2.png
[3]: media/iot-hub-python-twin-getstarted/3.png
