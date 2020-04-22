---
title: Mensagens cloud-to-device com Azure IoT Hub (Python) [ Microsoft Docs
description: Como enviar mensagens cloud-to-device para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para Python. Modifica uma aplicação simulada de dispositivopara receber mensagens cloud-to-device e modificar uma aplicação de back-end para enviar as mensagens cloud-to-device.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: f0760f6e61904295771ba349f8101e2d6dc6afe3
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759738"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Envie mensagens cloud-to-device com IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. A [telemetria Enviar de um dispositivo para um quickstart de hub IoT](quickstart-send-telemetry-python.md) mostra como criar um hub IoT, fornecer uma identidade de dispositivo no mesmo, e codificar uma aplicação simulada de dispositivo que envia mensagens dispositivo-para-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se em [Enviar a telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md). Mostra como:

* A partir da sua solução traseira, envie mensagens cloud-to-device para um único dispositivo através do IoT Hub.

* Receba mensagens cloud-to-device num dispositivo.

Pode encontrar mais informações sobre mensagens cloud-to-device no guia de [desenvolvimento do IoT Hub](iot-hub-devguide-messaging.md).

No final deste tutorial, executa duas aplicações de consola Python:

* **SimulatedDevice.py**, uma versão modificada da app criada no [Send telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), que se conecta ao seu hub IoT e recebe mensagens cloud-to-device.

* **SendCloudToDeviceMessage.py**, que envia mensagens cloud-to-device para a aplicação simulada do dispositivo através do IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação de dispositivo simulado

Nesta secção, cria-se uma aplicação de consola Python para simular o dispositivo e receber mensagens cloud-to-device a partir do hub IoT.

1. A partir de um pedido de comando no seu diretório de trabalho, instale o **Dispositivo Hub Azure IoT SDK para Python:**

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Utilizando um editor de texto, crie um ficheiro chamado **SimulatedDevice.py**.

1. Adicione as `import` seguintes declarações e variáveis no início do ficheiro **SimulatedDevice.py:**

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Adicione o seguinte código a **SimulatedDevice.py** ficheiro. Substitua `{deviceConnectionString}` o valor do espaço reservado pela cadeia de ligação do dispositivo para o dispositivo que criou na telemetria Enviar de um dispositivo para um arranque rápido do [hub IoT:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adicione a seguinte função à impressão de mensagens recebidas à consola:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Adicione o seguinte código para inicializar o cliente e aguarde para receber a mensagem cloud-to-device:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Adicione a seguinte função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Guarde e feche o ficheiro **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

Neste artigo, cria um serviço de backend para enviar mensagens cloud-to-device através do hub IoT que criou em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md). Para enviar mensagens cloud-to-device, o seu serviço necessita da permissão de ligação do **serviço.** Por padrão, cada Hub IoT é criado com uma política de acesso partilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem cloud-to-device

Nesta secção, cria-se uma aplicação de consola Python que envia mensagens cloud-to-device para a aplicação simulada do dispositivo. Precisa da identificação do dispositivo que adicionou na [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md) arranque rápido. Você também precisa da cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string).

1. No seu diretório de trabalho, abra um pedido de comando e instale o **Azure IoT Hub Service SDK para Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Utilizando um editor de texto, crie um ficheiro chamado **SendCloudToDeviceMessage.py**.

1. Adicione as `import` seguintes declarações e variáveis no início do ficheiro **SendCloudToDeviceMessage.py:**

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Adicione o seguinte código ao **ficheiro SendCloudToDeviceMessage.py.** Substitua `{iot hub connection string}` `{device id}` os valores e os valores do espaço reservado pela cadeia de ligação do hub IoT e identificação do dispositivo que tenha notado anteriormente:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Adicione o seguinte código para enviar mensagens ao seu dispositivo:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Adicione a seguinte função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Guarde e feche **SendCloudToDeviceMessage.py** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. No pedido de comando no seu diretório de trabalho, execute o seguinte comando para ouvir mensagens cloud-to-device:

    ```shell
    python SimulatedDevice.py
    ```

    ![Executar a aplicação de dispositivo simulado](./media/iot-hub-python-python-c2d/device-1.png)

1. Abra uma nova solicitação de comando no seu diretório de trabalho e execute o seguinte comando para enviar mensagens cloud-to-device:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Executar a aplicação para enviar o comando cloud-to-device](./media/iot-hub-python-python-c2d/service.png)

1. Note as mensagens recebidas pelo dispositivo.

    ![Mensagem recebida](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e a receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte o acelerador de solução de [monitorização remota Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de [desenvolvimento do IoT Hub.](iot-hub-devguide.md)
