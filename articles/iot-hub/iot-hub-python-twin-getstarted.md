---
title: Começa com os gémeos Azure IoT Hub (Python) Microsoft Docs
description: Como usar gémeos de dispositivo Azure IoT Hub para adicionar tags e, em seguida, usar uma consulta IoT Hub. Você usa os SDKs Azure IoT para Python para implementar a aplicação de dispositivo simulado e uma aplicação de serviço que adiciona as tags e executa a consulta IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 12b1d083ae1481f7c8b5fe60cac9156a56aeaa0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87875477"
---
# <a name="get-started-with-device-twins-python"></a>Começar com os gémeos do dispositivo (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terás duas aplicações para consolas Python:

* **AddTagsAndQuery.py**, uma aplicação de back-end Python, que adiciona etiquetas e consultas de dispositivo gémeos.

* **ReportConnectivity.py**, uma aplicação Python, que simula um dispositivo que se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, e relata a sua condição de conectividade.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar a app de serviço

Nesta secção, cria-se uma aplicação para consola Python que adiciona metadados de localização ao dispositivo twin associado ao seu **{Device ID}**. Em seguida, consulta os gémeos do dispositivo armazenados no hub IoT selecionando os dispositivos localizados em Redmond, e depois os que estão reportando uma ligação celular.

1. No seu diretório de trabalho, abra um pedido de comando e instale o **Azure IoT Hub Service SDK para Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Utilizando um editor de texto, crie um novo ficheiro **AddTagsAndQuery.py.**

3. Adicione o código seguinte para importar os módulos necessários do SDK do serviço:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Adicione o seguinte código. Substitua-a pela cadeia de `[IoTHub Connection String]` ligação do hub IoT copiada na [cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string). `[Device Id]`Substitua-o pelo ID do dispositivo que registou no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Adicione o seguinte código ao ficheiro **AddTagsAndQuery.py:**

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    O objeto **IoTHubRegistryManager** expõe todos os métodos necessários para interagir com os gémeos do dispositivo do serviço. O código inicializa primeiro o objeto **IoTHubRegistryManager,** depois atualiza o dispositivo twin para **DEVICE_ID**– e finalmente executa duas consultas. O primeiro seleciona apenas os gémeos do dispositivo de dispositivos localizados na fábrica **Redmond43,** e o segundo refina a consulta para selecionar apenas os dispositivos que também estão ligados através de uma rede celular.

6. Adicione o seguinte código no final de **AddTagsAndQuery.py** para implementar a função **iothub_service_sample_run:**

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Executar a aplicação com:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Deverá ver um dispositivo nos resultados da consulta pedindo todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que utilizam uma rede celular.

    ![primeira consulta mostrando todos os dispositivos em Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

Na secção seguinte, cria-se uma aplicação para dispositivos que relata as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação do dispositivo

Nesta secção, cria-se uma aplicação para consola Python que se conecta ao seu hub como o seu **{Device ID}** e, em seguida, atualiza as propriedades reportadas do seu dispositivo twin para conter a informação que está ligada através de uma rede celular.

1. A partir de um pedido de comando no seu diretório de trabalho, instale o **Azure IoT Hub Device SDK para Python:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Utilizando um editor de texto, crie um novo ficheiro **ReportConnectivity.py.**

3. Adicione o seguinte código para importar os módulos necessários do dispositivo SDK:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Adicione o seguinte código. Substitua o `[IoTHub Device Connection String]` valor do espaço reservado pela cadeia de ligação do dispositivo copiada no Registo de um novo dispositivo no hub [IoT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Adicione o seguinte código ao ficheiro **ReportConnectivity.py** para implementar a funcionalidade gémeas do dispositivo:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    O objeto **IoTHubModuleClient** expõe todos os métodos necessários para interagir com os gémeos do dispositivo do dispositivo. O código anterior, depois de rubricar o objeto **IoTHubModuleClient,** recupera o dispositivo twin para o seu dispositivo e atualiza a sua propriedade reportada com a informação de conectividade.

6. Adicione o seguinte código no final de **ReportConnectivity.py** para implementar a função **iothub_client_sample_run:**

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Executar a aplicação do dispositivo:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Deverá ver a confirmação de que as propriedades reportadas por twin do dispositivo foram atualizadas.

    ![atualizar propriedades reportadas a partir de app do dispositivo](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Agora que o dispositivo reportou as suas informações de conectividade, deve aparecer em ambas as consultas. Volte e corra as consultas novamente:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Desta vez, o **seu {Device ID}** deve aparecer em ambos os resultados de consulta.

    ![segunda consulta na aplicação de serviço](./media/iot-hub-python-twin-getstarted/service-2.png)

    Na aplicação do seu dispositivo, verá a confirmação de que o patch twin de propriedades pretendido enviado pela aplicação de serviço foi recebido.

    ![receber propriedades desejadas na aplicação do dispositivo](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionou metadados de dispositivo como tags de uma aplicação de back-end, e escreveu uma aplicação de dispositivo simulado para reportar informações de conectividade do dispositivo no twin do dispositivo. Também aprendeu a consultar esta informação usando o registo.

Utilize os seguintes recursos para aprender a:

* Envie telemetria a partir de dispositivos com o Tutorial Get start com o tutorial [IoT Hub.](quickstart-send-telemetry-python.md)

* Configure os dispositivos utilizando as propriedades desejadas do Device Twin com as [propriedades desejadas para configurar os dispositivos](tutorial-device-twins.md) tutoriais.

* Dispositivos de controlo interativamente (como ligar um ventilador a partir de uma aplicação controlada pelo utilizador), com o tutorial [de métodos diretos Use.](quickstart-control-device-python.md)
