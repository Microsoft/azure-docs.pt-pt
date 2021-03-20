---
title: Mensagens em nuvem para dispositivo com Azure IoT Hub (Python) | Microsoft Docs
description: Como enviar mensagens nuvem-para-dispositivo para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para Python. Modifica uma aplicação de dispositivo simulada para receber mensagens nuvem-a-dispositivo e modifica uma aplicação de back-end para enviar as mensagens cloud-to-device.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: ad6399a4713520ca0550d143cf3f19f87d55337c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87876806"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Envie mensagens nuvem-para-dispositivo com IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações biducionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. O [envio de telemetria de um dispositivo para um quickstart do hub IoT](quickstart-send-telemetry-python.md) mostra como criar um hub IoT, fornecê-lo e codificar uma aplicação de dispositivo simulado que envia mensagens dispositivo-a-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se no [Envio de telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md). Mostra como:

* A partir da parte de trás da sua solução, envie mensagens nuvem-dispositivo para um único dispositivo através do IoT Hub.

* Receba mensagens nuvem-dispositivo num dispositivo.

Pode encontrar mais informações sobre mensagens nuvem-dispositivo no [guia de desenvolvimento do IoT Hub.](iot-hub-devguide-messaging.md)

No final deste tutorial, executam duas aplicações de consola Python:

* **SimulatedDevice.py**, uma versão modificada da app criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), que se conecta ao seu hub IoT e recebe mensagens nuvem-dispositivo.

* **SendCloudToDeviceMessage.py**, que envia mensagens nuvem-para-dispositivo para a aplicação do dispositivo simulado através do IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receba mensagens na aplicação do dispositivo simulado

Nesta secção, cria-se uma aplicação para consola Python para simular o dispositivo e receber mensagens nuvem-dispositivo a partir do hub IoT.

1. A partir de um pedido de comando no seu diretório de trabalho, instale o **Azure IoT Hub Device SDK para Python:**

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

1. Adicione o seguinte código ao **SimulatedDevice.py** ficheiro. Substitua o `{deviceConnectionString}` valor do espaço reservado pela cadeia de ligação do dispositivo pelo dispositivo criado na [telemetria Enviar telemetria de um dispositivo para um arranque rápido do hub IoT:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adicione a seguinte função para imprimir mensagens recebidas à consola:

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

1. Adicione o seguinte código para inicializar o cliente e esperar para receber a mensagem nuvem-para-dispositivo:

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

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

Neste artigo, cria um serviço de backend para enviar mensagens nuvem-a-dispositivo através do hub IoT que criou Enviar por email o artigo [Telemetria para um hub IoT](quickstart-send-telemetry-python.md). Para enviar mensagens nuvem-para-dispositivo, o seu serviço necessita da permissão **de ligação de serviço.** Por padrão, cada IoT Hub é criado com uma política de acesso compartilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem nuvem-a-dispositivo

Nesta secção, cria-se uma aplicação para consola Python que envia mensagens nuvem-para-dispositivo para a aplicação do dispositivo simulado. Precisa do dispositivo ID do dispositivo que adicionou na [telemetria Enviar telemetria de um dispositivo para um quickstart do hub IoT.](quickstart-send-telemetry-python.md) Também precisa da cadeia de ligação do hub IoT que copiou anteriormente na [cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string).

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

1. Adicione o seguinte código ao **SendCloudToDeviceMessage.py** ficheiro. Substitua os `{iot hub connection string}` valores e `{device id}` os lugares reservados pela cadeia de ligação do hub IoT e pelo ID do dispositivo que observou anteriormente:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Adicione o seguinte código para enviar mensagens para o seu dispositivo:

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

1. No comando do seu diretório de trabalho, executar o seguinte comando para ouvir mensagens nuvem-dispositivo:

    ```shell
    python SimulatedDevice.py
    ```

    ![Executar a aplicação de dispositivo simulado](./media/iot-hub-python-python-c2d/device-1.png)

1. Abra uma nova solicitação de comando no seu diretório de trabalho e execute o seguinte comando para enviar mensagens cloud-to-device:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Executar a aplicação para enviar o comando nuvem-para-dispositivo](./media/iot-hub-python-python-c2d/service.png)

1. Note as mensagens recebidas pelo dispositivo.

    ![Mensagem recebida](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte o [acelerador de solução de monitorização remota Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvimento do [IoT Hub.](iot-hub-devguide.md)
