---
title: Agendar tarefas com o IoT Hub do Azure (Python) | Documentos da Microsoft
description: Como agendar um trabalho do IoT Hub do Azure para invocar um método direto em vários dispositivos. Utilize os SDKs IoT do Azure para Python para implementar as aplicações de dispositivo simulado e uma aplicação de serviço para executar a tarefa.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: de3eef64f708f711d66c2bce344be0aef06fa209
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673875"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Agendar e difundir tarefas (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

O IoT Hub do Azure é um serviço totalmente gerido que permite uma aplicação de back-end criar e controlar tarefas que agendem e atualizar milhões de dispositivos.  Tarefas podem ser utilizadas para as seguintes ações:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Conceitualmente, uma tarefa encapsula uma destas ações e controla o progresso de execução com um conjunto de dispositivos, que é definido por uma consulta do dispositivo duplo.  Por exemplo, uma aplicação de back-end pode utilizar uma tarefa para invocar um método de reinício em 10 000 dispositivos, especificado por uma consulta do dispositivo duplo e agendado num momento futuro.  Esse aplicativo, em seguida, pode controlar o progresso à medida que cada um desses dispositivos receber e executar o método de reinício.

Saiba mais sobre cada uma destas capacidades nestes artigos:

* Dispositivo duplo e propriedades: [Introdução aos dispositivos duplos](iot-hub-python-twin-getstarted.md) e [Tutorial: Como utilizar propriedades dos dispositivos duplos](tutorial-device-twins.md)
* Métodos diretos: [Guia do programador do IoT Hub - métodos diretos](iot-hub-devguide-direct-methods.md) e [Tutorial: métodos diretos](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Criar uma aplicação de dispositivo simulado de Python que possui um método direto, que permite **lockDoor**, que pode ser chamado pela solução de back-end.
* Criar uma aplicação de consola Python que chama o **lockDoor** método direto na aplicação do dispositivo simulado utilizando uma tarefa e atualizações as propriedades pretendidas, usar um trabalho do dispositivo.

No final deste tutorial, tem duas aplicações Python:

**simDevice.py**, que liga ao seu hub IoT com a identidade de dispositivo e recebe um **lockDoor** método direto.

**scheduleJobService.py**, que chama um método direto na aplicação do dispositivo simulado e atualiza o dispositivo duplo pretendido propriedades usando uma tarefa.

Para concluir este tutorial, precisa do seguinte:

* [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python à variável de ambiente específica da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python](https://pip.pypa.io/en/stable/installing/).
* Se estiver a utilizar o SO Windows, o [pacote redistribuível Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145), para permitir a utilização de DLLs nativas de Python.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

> [!NOTE]
> O **do Azure IoT SDK para Python** não suporta diretamente **tarefas** funcionalidade. Em vez disso, este tutorial oferece uma solução alternativa que threads assíncronas e temporizadores. Para obter mais atualizações, consulte a **SDK de cliente do serviço** lista de funcionalidades no [SDK do IoT do Azure para Python](https://github.com/Azure/azure-iot-sdk-python) página. 
> 
> 

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, vai criar uma aplicação de consola Python que responde a um método direto chamado pela cloud, que dispara um simulado **lockDoor** método.

1. Na sua linha de comandos, execute o seguinte comando para instalar o **azure-iot-device-client** pacote:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Com um editor de texto, crie um novo **simDevice.py** ficheiro no diretório de trabalho.

1. Adicione as seguintes `import` afirmações e variáveis no início do **simDevice.py** ficheiro. Substitua `deviceConnectionString` com a cadeia de ligação do dispositivo que criou acima:
   
    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adicionar o retorno de chamada de função seguinte para lidar com o **lockDoor** método:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Adicione outro retorno de chamada de função para lidar com atualizações de gémeos de dispositivo:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Adicione o seguinte código para registrar o manipulador para o **lockDoor** método. Também incluem o `main` rotina:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

1. Guarde e feche o **simDevice.py** ficheiro.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar tarefas para chamar um método direto e a atualizar as propriedades de um dispositivo duplo
Nesta secção, vai criar uma aplicação de consola Python que inicia um remoto **lockDoor** num dispositivo através de um método direto e atualizar as propriedades do dispositivo duplo.

1. Na sua linha de comandos, execute o seguinte comando para instalar o **azure-iot-service-client** pacote:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Com um editor de texto, crie um novo **scheduleJobService.py** ficheiro no diretório de trabalho.

1. Adicione as seguintes `import` afirmações e variáveis no início do **scheduleJobService.py** ficheiro:
   
    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

1. Adicione a seguinte função que é utilizada para consultar os dispositivos:
   
    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
    
        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)
    
        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

1. Adicione os seguintes métodos para executar as tarefas que chamam o twin do dispositivo e o método direto:
   
    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
    
            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
        
            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )
        
    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
    
            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
        
            print ( "" )
            print ( "Device twin updated." )
    ```

1. Adicione o seguinte código para agendar as tarefas e atualizar o estado da tarefa. Também incluem o `main` rotina:
   
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

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
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

1. Guarde e feche o **scheduleJobService.py** ficheiro.


## <a name="run-the-applications"></a>Executar as aplicações
Pode agora executar as aplicações.

1. No prompt de comando no diretório de trabalho, execute o seguinte comando para começar a escutar o método direto de reinício:
   
    ```cmd/sh
    python simDevice.py
    ```

1. Outra linha de comandos no diretório de trabalho, execute o seguinte comando para acionar as tarefas para a porta de bloqueio e atualizar o duplo:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. Verá as respostas de dispositivo para o método direto e dispositivos duplos de atualização na consola do.

    ![saída de dispositivo][1]

    ![saída de serviço][2]


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, utilizou uma tarefa para agendar um método direto a um dispositivo e a atualização das propriedades do dispositivo duplo.

Para continuar a introdução ao IoT Hub e padrões de gestão de dispositivos como remota sobre a atualização de firmware do ar, veja [como fazer uma atualização de firmware](tutorial-firmware-update.md).

<!-- images -->
[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
