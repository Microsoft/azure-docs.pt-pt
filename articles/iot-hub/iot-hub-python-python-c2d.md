---
title: Mensagens de cloud para o dispositivo com o IoT Hub do Azure (Python) | Documentos da Microsoft
description: Como enviar mensagens da cloud para dispositivo a um dispositivo de um hub de IoT do Azure com os SDKs IoT do Azure para Python. Modificar uma aplicação de dispositivo simulado para receber mensagens da cloud para o dispositivo e modificar uma aplicação de back-end para enviar as mensagens de cloud-para-dispositivo.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: 0feff40aff4db65104cb2531881119086dc813a7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541915"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Enviar mensagens da cloud para o dispositivo com o IoT Hub (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Introdução
O IoT Hub do Azure é um serviço totalmente gerido que ajuda a habilitar comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução de back-end. O [introdução ao IoT Hub] tutorial mostra como criar um hub IoT, aprovisionar uma identidade de dispositivo no mesmo e código de uma aplicação de dispositivo simulado que envia mensagens do dispositivo para a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se nas [introdução ao IoT Hub]. Mostra-lhe como para:

* Da sua solução back-end, envie mensagens de cloud-para-dispositivo para um único dispositivo através do IoT Hub.
* Receba mensagens da cloud para o dispositivo num dispositivo.
* Da sua solução back-end, pedir confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens de cloud para o dispositivo na [Guia do programador do IoT Hub](iot-hub-devguide-messaging.md).

No final deste tutorial, executa duas aplicações de consola Python:

* **SimulatedDevice.py**, uma versão modificada da aplicação que criou no [introdução ao IoT Hub], que liga ao seu hub IoT e recebe mensagens de cloud-para-dispositivo.
* **SendCloudToDeviceMessage.py**, que envia uma mensagem de cloud-para-dispositivo para a aplicação de dispositivo simulado através do IoT Hub e, em seguida, recebe a confirmação de entrega.

> [!NOTE]
> IoT Hub tem suporte SDK para muitas plataformas de dispositivos e linguagens (incluindo C, Java e Javascript) através de SDKs de dispositivo do IoT do Azure. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código do tutorial e, em geral, hub IoT do Azure, consulte a [Centro de programadores de IoT do Azure](https://www.azure.com/develop/iot).
> 

Para concluir este tutorial, precisa do seguinte:

* [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python à variável de ambiente específica da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python](https://pip.pypa.io/en/stable/installing/).
* Se estiver a utilizar o SO Windows, o [pacote redistribuível Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145), para permitir a utilização de DLLs nativas de Python.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

> [!NOTE]
> Os pacotes *pip* para `azure-iothub-service-client` e `azure-iothub-device-client` estão atualmente disponíveis apenas para o SO Windows. Para o SO Linux/Mac, consulte as secções de Linux e Mac específicas do sistema operacional sobre o [preparar o ambiente de desenvolvimento para Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) publicar.
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação do dispositivo simulado
Nesta secção, vai criar uma aplicação de consola Python para simular o dispositivo e receber mensagens da cloud para dispositivo do IoT hub.

1. Com um editor de texto, crie uma **SimulatedDevice.py** ficheiro.

1. Adicione as seguintes `import` afirmações e variáveis no início do **SimulatedDevice.py** ficheiro:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. Adicione o seguinte código para **SimulatedDevice.py** ficheiro. Substitua o valor de marcador de posição de "{deviceConnectionString}" com a cadeia de ligação do dispositivo para o dispositivo que criou o [introdução ao IoT Hub] tutorial:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adicione a seguinte função para imprimir mensagens recebidas para a consola:
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. Adicione o seguinte código para inicializar o cliente e esperando para receber a mensagem de cloud para o dispositivo:
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. Adicione a seguinte função principal:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Guardar e fechar **SimulatedDevice.py** ficheiro.


## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de cloud-para-dispositivo
Nesta secção, vai criar uma aplicação de consola Python que envia mensagens de cloud-para-dispositivo para a aplicação de dispositivo simulado. Precisa do ID de dispositivo do dispositivo que adicionou no [introdução ao IoT Hub] tutorial. Também precisa da cadeia de ligação do IoT Hub para o hub que pode encontrar no [portal do Azure](https://portal.azure.com).

1. Com um editor de texto, crie uma **SendCloudToDeviceMessage.py** ficheiro.

1. Adicione as seguintes `import` afirmações e variáveis no início do **SendCloudToDeviceMessage.py** ficheiro:
   
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

1. Adicione o seguinte código para **SendCloudToDeviceMessage.py** ficheiro. Substitua o valor de marcador de posição de "{IoTHubConnectionString}" com a cadeia de ligação do IoT Hub para o hub que criou na [introdução ao IoT Hub] tutorial. Substitua o marcador de posição de "{deviceId}" com o ID de dispositivo do dispositivo que adicionou no [introdução ao IoT Hub] tutorial:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Adicione a seguinte função para imprimir mensagens de comentários para a consola:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Adicione o seguinte código para enviar uma mensagem para o seu dispositivo e manipular a mensagem de comentários, quando o dispositivo reconhece a mensagem de cloud para o dispositivo:
   
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

1. Adicione a seguinte função principal:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. Guardar e fechar **SendCloudToDeviceMessage.py** ficheiro.


## <a name="run-the-applications"></a>Executar as aplicações
Pode agora executar as aplicações.

1. Abra uma linha de comandos e instale o **SDK do dispositivo de Hub de IoT do Azure para Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. No prompt de comando, execute o seguinte comando para escutar as mensagens da cloud para o dispositivo:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![Executar a aplicação de dispositivo simulado][img-simulated-device]

1. Abra uma nova linha de comandos e instale o **SDK do serviço de Hub de IoT do Azure para Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. No prompt de comando, execute o seguinte comando para enviar uma mensagem de cloud-para-dispositivo e espere até que os comentários de mensagem:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Executar a aplicação para enviar o comando do cloud-para-dispositivo][img-send-command]
   
1. Tenha em atenção a mensagem recebida pelo dispositivo.

    ![Foi recebida uma mensagem][img-message-received]


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a enviar e receber mensagens da cloud para o dispositivo. 

Para ver exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, veja [solution accelerator do monitoramento remoto do Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, veja a [Guia do programador do IoT Hub](iot-hub-devguide.md).

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-received]: media/iot-hub-python-python-c2d/message-received.png

<!-- Links -->
[Introdução ao IoT Hub]: quickstart-send-telemetry-python.md
