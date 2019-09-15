---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (Python) | Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um hub IoT do Azure usando os SDKs do IoT do Azure para Python. Você modifica um aplicativo de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviar as mensagens da nuvem para o dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 4cda59448856630468076ef63c51b8a216a31bd0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001957"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Enviar mensagens da nuvem para o dispositivo com o Hub IoT (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a habilitar comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. O guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md) mostra como criar um hub IOT, provisionar uma identidade do dispositivo nele e codificar um aplicativo de dispositivo simulado que envia mensagens do dispositivo para a nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia no [envio de telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md). Ele mostra como:

* De seu back-end da solução, envie mensagens da nuvem para o dispositivo para um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

* Do seu back-end da solução, solicite a confirmação de entrega (*comentários*) para mensagens enviadas a um dispositivo do Hub IOT.

Você pode encontrar mais informações sobre as mensagens da nuvem para o dispositivo no [Guia do desenvolvedor do Hub IOT](iot-hub-devguide-messaging.md).

No final deste tutorial, você executará dois aplicativos de console do Python:

* **SimulatedDevice.py**, uma versão modificada do aplicativo criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md), que se conecta ao seu hub IOT e recebe mensagens da nuvem para o dispositivo.

* **SendCloudToDeviceMessage.py**, que envia uma mensagem da nuvem para o dispositivo para o aplicativo de dispositivo simulado por meio do Hub IOT e recebe sua confirmação de entrega.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens no aplicativo do dispositivo simulado

Nesta seção, você cria um aplicativo de console do Python para simular o dispositivo e receber mensagens da nuvem para o dispositivo do Hub IoT.

1. Usando um editor de texto, crie um arquivo **SimulatedDevice.py** .

2. Adicione as seguintes `import` instruções e variáveis ao início do arquivo **SimulatedDevice.py** :

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Adicione o código a seguir ao arquivo **SimulatedDevice.py** . Substitua o valor do espaço reservado "{deviceConnectionString}" pela cadeia de conexão do dispositivo do dispositivo que você criou no início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md) :

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Adicione a seguinte função para imprimir mensagens recebidas no console:

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

5. Adicione o seguinte código para inicializar o cliente e aguarde para receber a mensagem da nuvem para o dispositivo:

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

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

7. Salve e feche o arquivo **SimulatedDevice.py** .

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens da nuvem para o dispositivo por meio do Hub IoT criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md). Para enviar mensagens da nuvem para o dispositivo, seu serviço precisa da permissão de **conexão de serviço** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem da nuvem para o dispositivo

Nesta seção, você cria um aplicativo de console do Python que envia mensagens da nuvem para o dispositivo para o aplicativo do dispositivo simulado. Você precisa da ID do dispositivo que adicionou no guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md) . Você também precisa da cadeia de conexão do Hub IoT que copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

1. Usando um editor de texto, crie um arquivo **SendCloudToDeviceMessage.py** .

2. Adicione as seguintes `import` instruções e variáveis ao início do arquivo **SendCloudToDeviceMessage.py** :

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

3. Adicione o código a seguir ao arquivo **SendCloudToDeviceMessage.py** . Substitua os valores de espaço reservado "{cadeia de conexão do Hub IOT}" e "{ID do dispositivo}" pela cadeia de conexão do Hub IoT e ID do dispositivo observado anteriormente:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Adicione a seguinte função para imprimir mensagens de comentários no console:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Adicione o código a seguir para enviar uma mensagem ao seu dispositivo e manipular a mensagem de comentários quando o dispositivo confirmar a mensagem da nuvem para o dispositivo:

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

7. Salve e feche o arquivo **SendCloudToDeviceMessage.py** .

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Abra um prompt de comando e instale o **SDK do dispositivo do Hub IOT do Azure para Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. No prompt de comando, execute o seguinte comando para escutar mensagens da nuvem para o dispositivo:

    ```shell
    python SimulatedDevice.py
    ```

    ![Executar o aplicativo de dispositivo simulado](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Abra um novo prompt de comando e instale o **SDK do serviço de Hub IOT do Azure para Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Em um prompt de comando, execute o seguinte comando para enviar uma mensagem da nuvem para o dispositivo e aguarde os comentários da mensagem:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Executar o aplicativo para enviar o comando da nuvem para o dispositivo](./media/iot-hub-python-python-c2d/send-command.png)

5. Observe a mensagem recebida pelo dispositivo.

    ![Mensagem recebida](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas de ponta a ponta que usam o Hub IoT, consulte [acelerador de solução de monitoramento remoto de IOT do Azure](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre como desenvolver soluções com o Hub IoT, confira o [Guia do desenvolvedor do Hub IOT](iot-hub-devguide.md).
