---
title: Mensagens cloud-to-device com Azure IoT Hub (Python)  Microsoft Docs
description: Como enviar mensagens cloud-to-device para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para Python. Modifica uma aplicação simulada de dispositivopara receber mensagens cloud-to-device e modificar uma aplicação de back-end para enviar as mensagens cloud-to-device.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110433"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Envie mensagens cloud-to-device com IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. A [telemetria Enviar de um dispositivo para um quickstart de hub IoT](quickstart-send-telemetry-python.md) mostra como criar um hub IoT, fornecer uma identidade de dispositivo no mesmo, e codificar uma aplicação simulada de dispositivo que envia mensagens dispositivo-para-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se em [Enviar a telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md). Mostra como:

* A partir da sua solução traseira, envie mensagens cloud-to-device para um único dispositivo através do IoT Hub.

* Receba mensagens cloud-to-device num dispositivo.

* A partir da sua solução na parte de trás, solicite reconhecimento de entrega *(feedback)* para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens cloud-to-device no guia de [desenvolvimento do IoT Hub](iot-hub-devguide-messaging.md).

No final deste tutorial, executa duas aplicações de consola Python:

* **SimulatedDevice.py**, uma versão modificada da app criada no [Send telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), que se conecta ao seu hub IoT e recebe mensagens cloud-to-device.

* **SendCloudToDeviceMessage.py**, que envia uma mensagem cloud-to-device para a aplicação simulada do dispositivo através do IoT Hub, e recebe depois o seu reconhecimento de entrega.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação de dispositivo simulado

Nesta secção, cria-se uma aplicação de consola Python para simular o dispositivo e receber mensagens cloud-to-device a partir do hub IoT.

1. Utilizando um editor de texto, crie um ficheiro **SimulatedDevice.py.**

2. Adicione as seguintes declarações e variáveis `import` no início do ficheiro **SimulatedDevice.py:**

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Adicione o seguinte código a **SimulatedDevice.py** ficheiro. Substitua o valor do espaço reservado "{deviceConnectionString}" pela cadeia de ligação do dispositivo para o dispositivo que criou na telemetria Enviar de um dispositivo para um arranque rápido do [hub IoT:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Adicione a seguinte função à impressão de mensagens recebidas à consola:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Adicione o seguinte código para inicializar o cliente e aguarde para receber a mensagem cloud-to-device:

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
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Adicione a seguinte função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Guarde e feche **SimulatedDevice.py** ficheiro.

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo cria um serviço de backend para enviar mensagens cloud-to-device através do hub IoT que criou em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md). Para enviar mensagens cloud-to-device, o seu serviço necessita da permissão de ligação do **serviço.** Por padrão, cada Hub IoT é criado com uma política de acesso partilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem cloud-to-device

Nesta secção, cria-se uma aplicação de consola Python que envia mensagens cloud-to-device para a aplicação simulada do dispositivo. Precisa da identificação do dispositivo que adicionou na [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md) arranque rápido. Você também precisa da cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string).

1. Usando um editor de texto, crie um ficheiro **SendCloudToDeviceMessage.py.**

2. Adicione as seguintes declarações e variáveis `import` no início do ficheiro **SendCloudToDeviceMessage.py:**

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Adicione o seguinte código ao **ficheiro SendCloudToDeviceMessage.py.** Substitua os valores do espaço de ligação "{iot hub}" e "{device id}" com a cadeia de ligação do hub IoT e o ID do dispositivo que tenha notado anteriormente:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Adicione a seguinte função para imprimir mensagens de feedback à consola:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Adicione o seguinte código para enviar uma mensagem ao seu dispositivo e manuseie a mensagem de feedback quando o dispositivo reconhecer a mensagem cloud-to-device:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Adicione a seguinte função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Guarde e feche **SendCloudToDeviceMessage.py** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Abra um pedido de comando e instale o **Dispositivo Hub Azure IoT SDK para Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. No pedido de comando, execute o seguinte comando para ouvir as mensagens cloud-to-device:

    ```shell
    python SimulatedDevice.py
    ```

    ![Executar a aplicação de dispositivo simulado](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Abra um novo pedido de comando e instale o **Serviço De Hub Azure IoT SDK para Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Num pedido de comando, execute o seguinte comando para enviar uma mensagem cloud-to-device e aguarde pelo feedback da mensagem:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Executar a aplicação para enviar o comando cloud-to-device](./media/iot-hub-python-python-c2d/send-command.png)

5. Note a mensagem recebida pelo dispositivo.

    ![Mensagem recebida](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e a receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte o acelerador de solução de [monitorização remota Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de [desenvolvimento do IoT Hub.](iot-hub-devguide.md)
