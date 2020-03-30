---
title: Agendar trabalhos com o Azure IoT Hub (Python) [ Microsoft Docs
description: Como agendar um trabalho do Azure IoT Hub para invocar um método direto em vários dispositivos. Usa os SDKs Azure IoT para python para implementar as aplicações simuladas do dispositivo e uma aplicação de serviço para executar o trabalho.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 1d721e89534c09a5572e5674796f28355f652165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527406"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Agendae trabalhos de transmissão (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

O Azure IoT Hub é um serviço totalmente gerido que permite a uma aplicação back-end criar e rastrear empregos que programam e atualizam milhões de dispositivos.  Os postos de trabalho podem ser utilizados para as seguintes ações:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Conceptualmente, um trabalho envolve uma destas ações e acompanha o progresso da execução contra um conjunto de dispositivos, que é definido por uma consulta gémea do dispositivo.  Por exemplo, uma aplicação back-end pode usar um trabalho para invocar um método de reinicialização em 10.000 dispositivos, especificado por uma consulta de gémeos dispositivos e agendado em um futuro.  Esta aplicação pode então acompanhar o progresso à medida que cada um desses dispositivos recebe e executar o método de reinicialização.

Saiba mais sobre cada uma destas capacidades nestes artigos:

* Dispositivo twin e propriedades: [Começar com gémeos dispositivo](iot-hub-python-twin-getstarted.md) e [Tutorial: Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)

* Métodos diretos: Guia de [desenvolvimento do IoT Hub - métodos diretos](iot-hub-devguide-direct-methods.md) e [Tutorial: métodos diretos](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Crie uma aplicação de dispositivo simulada Python que tenha um método direto, que permite o **lockDoor**, que pode ser chamado pela extremidade traseira da solução.

* Crie uma aplicação de consola Python que chame o método direct **lockDoor** na aplicação simulada do dispositivo utilizando um trabalho e atualiza as propriedades desejadas usando um trabalho de dispositivo.

No final deste tutorial, você tem duas aplicações Python:

**simDevice.py**, que se conecta ao seu hub IoT com a identidade do dispositivo e recebe um método direto **lockDoor.**

**scheduleJobService.py**, que chama um método direto na aplicação simulada do dispositivo e atualiza as propriedades desejadas pelo dispositivo twin usando um trabalho.

> [!NOTE]
> O **Azure IoT SDK para Python** não suporta diretamente a funcionalidade **Jobs.** Em vez disso, este tutorial oferece uma solução alternativa utilizando fios e temporizadores assíncronos. Para mais atualizações, consulte a lista de funcionalidades **SDK** do Cliente de Serviço na página [Azure IoT SDK para Python.](https://github.com/Azure/azure-iot-sdk-python)
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, cria-se uma aplicação de consola Python que responde a um método direto chamado pela nuvem, que desencadeia um método de **lockDoor** simulado.

1. Ao seu pedido de comando, execute o seguinte comando para instalar o pacote de **dispositivos azure-iot:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Utilizando um editor de texto, crie um novo ficheiro **simDevice.py** no seu diretório de trabalho.

3. Adicione as `import` seguintes declarações e variáveis no início do ficheiro **simDevice.py.** Substitua-a `deviceConnectionString` pela cadeia de ligação do dispositivo acima criado:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Adicione a seguinte chamada de função para lidar com o método **lockDoor:**

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Adicione outra função de chamada para lidar com atualizações de gémeos dispositivo:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Adicione o seguinte código para registar o manipulador para o método **lockDoor.** Inclua `main` também a rotina:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Guarde e feche o ficheiro **simDevice.py.**

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar políticas de retry (como um backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

Neste artigo, cria-se um serviço de backend que invoca um método direto num dispositivo e atualiza o dispositivo twin. O serviço precisa da permissão de **ligação** do serviço para chamar um método direto num dispositivo. O serviço também precisa do **registo lido** e **registo de** permissões para ler e escrever o registo de identidade. Não existe uma política de acesso partilhado por defeito que contenha apenas estas permissões, pelo que precisa de criar uma.

Para criar uma política de acesso partilhado que conceda **o serviço de ligação,** **leitura de registos**e permissões **de escrita** de registo e para obter uma cadeia de ligação para esta política, siga estes passos:

1. Abra o seu hub IoT no [portal Azure.](https://portal.azure.com) A maneira mais fácil de chegar ao seu hub IoT é selecionar **grupos de Recursos,** selecionar o grupo de recursos onde o seu hub IoT está localizado, e, em seguida, selecionar o seu hub IoT da lista de recursos.

2. No painel do lado esquerdo do seu hub IoT, selecione políticas de **acesso partilhado**.

3. A partir do menu superior acima da lista de políticas, selecione **Adicionar**.

4. No painel de política de **acesso partilhado,** insira um nome descritivo para a sua política; por exemplo: *serviçoAndRegistryReadWrite*. Em **Permissões**, selecione **ligação de serviço** e escrita de **registo** (a leitura**do registo** é automaticamente selecionada quando selecionar o **registo escrever).** Em seguida, selecione **Criar**.

    ![Mostre como adicionar uma nova política de acesso partilhado](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. De volta ao painel de políticas de **acesso partilhado,** selecione a sua nova política a partir da lista de políticas.

6. Em **teclas de acesso partilhadas,** selecione o ícone de cópia para a **cadeia de ligação -- chave primária** e guarde o valor.

    ![Mostre como recuperar a corda de ligação](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso partilhadas do IoT Hub, consulte [o controlo de acesso e as permissões.](./iot-hub-devguide-security.md#access-control-and-permissions)

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar trabalhos para chamar um método direto e atualizar as propriedades de um dispositivo twin

Nesta secção, cria-se uma aplicação de consola Python que inicia um **bloqueio remotoDoor** num dispositivo utilizando um método direto e também atualiza as propriedades desejadas pelo dispositivo twin.

1. Ao seu pedido de comando, execute o seguinte comando para instalar o pacote **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Utilizando um editor de texto, crie um novo ficheiro **scheduleJobService.py** no seu diretório de trabalho.

3. Adicione as `import` seguintes declarações e variáveis no início do ficheiro **scheduleJobService.py.** Substitua `{IoTHubConnectionString}` o espaço reservado pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string). Substitua `{deviceId}` o espaço reservado pelo ID do dispositivo registado no [Registo de um novo dispositivo no hub IoT:](#register-a-new-device-in-the-iot-hub)

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Adicione a seguinte função que é usada para consultar dispositivos:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Adicione os seguintes métodos para executar os trabalhos que chamam o método direto e o dispositivo twin:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Adicione o seguinte código para agendar os trabalhos e atualizar o estado do trabalho. Inclua `main` também a rotina:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Guarde e feche o ficheiro **scheduleJobService.py.**

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. No pedido de comando no seu diretório de trabalho, execute o seguinte comando para começar a ouvir o método direto de reinicialização:

    ```cmd/sh
    python simDevice.py
    ```

2. Em outra solicitação de comando no seu diretório de trabalho, execute o seguinte comando para desencadear os trabalhos para trancar a porta e atualizar o gémeo:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Vê as respostas do dispositivo ao método direto e atualização de gémeos dispositivos na consola.

    ![IoT Hub Job amostra 1 - saída do dispositivo](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub Job amostra 2-- saída do dispositivo](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do dispositivo twin.

Para continuar a começar com o IoT Hub e padrões de gestão de dispositivos, como remotamente sobre a atualização do firmware de ar, consulte [como fazer uma atualização](tutorial-firmware-update.md)de firmware .