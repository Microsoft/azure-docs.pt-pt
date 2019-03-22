---
title: Introdução ao IoT Hub do Azure módulo identidade e o módulo duplo (Python) | Documentos da Microsoft
description: Saiba como criar a identidade do módulo e atualizar o módulo duplo com os SDKs para Python.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 1d7c8d8a02358b4eb9f52f1a9bd04b797fcd934f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110805"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>Introdução ao IoT Hub módulo identidade e o módulo duplo com Python back-end e de dispositivo Python

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo duplo do Hub IoT do Azure permitem que a aplicação de back-end configure um dispositivo e conferem visibilidade às condições do dispositivo, uma identidade de módulo e o módulo duplo fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem a configuração e condições isoladas para cada componente.
>

No final deste tutorial, tem duas aplicações Python:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e a chave de segurança associada para ligar os clientes do dispositivo e do módulo.
* **UpdateModuleTwinReportedProperties**, que envia as propriedades reportadas do módulo duplo atualizadas para o Hub IoT.

> [!NOTE]
> Para informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end, veja [Azure IoT SDKs][lnk-hub-sdks].
>

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)
* Um Hub IoT.
* Instalar a versão mais recente [SDK de Python](https://github.com/Azure/azure-iot-sdk-python).

Criou o seu hub IoT e tem agora o nome de anfitrião e a cadeia de ligação do Hub IoT de que precisa para concluir este tutorial.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Criar uma identidade de dispositivo e uma identidade de módulo no IoT Hub

Nesta secção, vai criar uma aplicação de Python que cria uma identidade de dispositivo e uma identidade de módulo no registo de identidade do IoT hub. Não é possível ligar um dispositivo ou módulo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para obter mais informações, veja a secção "Identity registry" (Registo de identidades) do [Hub IoT developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity]. Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. O dispositivo e o módulo utilizam estes valores para se identificarem quando enviam mensagens do dispositivo para cloud para o Hub IoT. Os IDs são sensíveis às maiúsculas e minúsculas.

Adicione o seguinte código ao seu ficheiro de Python:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

Esta aplicação cria uma identidade de dispositivo com o ID **myFirstDevice** e uma identidade de módulo com o ID **myFirstModule** em dispositivos **myFirstDevice**. (Se o ID desse módulo já existir no registo de identidade, o código apenas obtém as informações do módulo existente.) De seguida, a aplicação irá apresentar a chave primária para essa identidade. Esta chave vai ser utilizada na aplicação do módulo simulado para ligar ao seu hub IoT.

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos e módulos para permitir um acesso seguro ao hub IoT. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity].
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Atualizar o módulo duplo usando o SDK de dispositivo Python

Nesta secção, vai criar um Python propriedades comunicadas de aplicação no seu dispositivo simulado que atualiza o duplo do módulo.

1. **Obtenha a sua cadeia de ligação do módulo** – agora, se iniciar sessão no [portal do Azure][lnk-portal]. Navegue até ao seu Hub IoT e clique em Dispositivos IoT. Encontre myFirstDevice, abra-lo e ver myFirstModule foi criado com êxito. Copie a cadeia de ligação do módulo. É necessária para o próximo passo.

   ![Detalhe do módulo no portal do Azure][15]

1. **Criar aplicação UpdateModuleTwinReportedProperties** adicione as seguintes `using` declarações na parte superior a **Program.cs** ficheiro:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

Este código de exemplo mostra como obter o módulo duplo e atualizar as propriedades reportadas com o protocolo AMQP. 

## <a name="get-updates-on-the-device-side"></a>Obter atualizações no lado do dispositivo

Além do código acima, pode adicionar abaixo do bloco de código para colocar a atualização de duplo mensagem no seu dispositivo.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
```


## <a name="next-steps"></a>Passos Seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Getting started with device management (Introdução à gestão de dispositivos)][lnk-device-management]
* [Introdução ao IoT Edge][lnk-iot-edge]


<!-- Images. -->
[15]:./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
