---
title: Tutorial - Use mQTT para criar um cliente de dispositivo de plug e reprodução Azure IoT Microsoft Docs
description: Tutorial - Utilize o protocolo MQTT diretamente para criar um cliente de dispositivo IoT Plug and Play sem utilizar os SDKs de dispositivo IoT Azure
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6852b0532b23e46c7b986926b21cd0b7e9f9736d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421384"
---
# <a name="tutorial---use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>Tutorial - Use mQTT para desenvolver um cliente de dispositivo ioT plug e play

Deverá utilizar um dos SDKs de dispositivoS Azure IoT para construir os seus clientes ioT plug e play, se possível. No entanto, em cenários como a utilização de um dispositivo constrangido à memória, poderá ser necessário utilizar uma biblioteca MQTT para comunicar com o seu hub IoT.

A amostra deste tutorial utiliza a biblioteca [Eclipse Mosquitto](http://mosquitto.org/) MQTT e o Visual Studio. Os passos neste tutorial assumem que está a usar o Windows na sua máquina de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este tutorial no Windows, instale o seguinte software no seu ambiente local do Windows:

* [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o Desenvolvimento do Ambiente de Trabalho com carga de trabalho **C++** quando [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio
* [Rio Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

Utilize a ferramenta *exploradora Azure IoT* para adicionar um novo dispositivo ao seu IoT Hub. Configuraste o teu hub IoT e a ferramenta exploradora Azure IoT quando completaste [Configurar o teu ambiente para os quickstarts e tutoriais do IoT Plug e Play:](set-up-environment.md)

1. Lance a ferramenta **exploradora Azure IoT.**
1. Na página de **hubs IoT,** selecione **Ver dispositivos neste centro**.
1. Na página **Dispositivos,** selecione **+ Novo**.
1. Crie um dispositivo chamado *my-mqtt-dispositivo* que usa uma chave simétrica autogerada.
1. Na página de identidade do **dispositivo,** expanda **a cadeia de ligação com o token SAS**.
1. Escolha a **chave primária** para usar como **tecla simétrica,** desajuste o tempo de validade para 60 minutos e selecione **Gerar**.
1. Copie a cadeia de **conexão de token SAS** gerada, use este valor mais tarde no tutorial.

## <a name="clone-sample-repo"></a>Repo de amostra de clone

Utilize o seguinte comando para clonar o repositório de amostras para um local adequado na sua máquina local:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Instalar biblioteca MQTT

Utilize a `vcpkg` ferramenta para descarregar e construir a biblioteca Eclipse Mosquitto.

Utilize o seguinte comando para clonar o repositório **Vcpkg** para um local adequado na sua máquina local:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Utilize os seguintes comandos para preparar o `vcpkg` ambiente. Precisa de um pedido de comando elevado quando `vcpkg integrate install` correr:

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Utilize o seguinte comando para descarregar e construir a biblioteca Eclipse Mosquitto:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Migrar a amostra para IoT Plug and Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Reveja o código de amostra não IoT Plug and Play

Atualize o código com detalhes do seu hub e dispositivo IoT antes de o construir e executar.

Para visualizar o código de amostra no Visual Studio, abra o ficheiro de solução *MQTTWin32.sln* na pasta *IoTMQTTSample\src\Windows.*

No **Solution Explorer,** clique à direita no projeto **TelemetryMQTTWin32** e selecione **set as Startup Project**.

No projeto **TelemetryMQTTWin32,** abra o ficheiro de origem **MQTT_Mosquitto.cpp.** Atualize as definições de informação de ligação com os detalhes do dispositivo que fez anteriormente. Substitua os espaços reservados para:

* `IOTHUBNAME` identificador com o nome do seu hub IoT.
* `DEVICEID` identificador com `my-mqtt-device` .
* `PWD` identificar com a parte correta da cadeia de ligação sasken que gerou para o dispositivo. Utilize a parte da cadeia de ligação `SharedAccessSignature sr=` até ao fim.

Verifique se o código está a funcionar corretamente, iniciando o explorador Azure IoT, comece a ouvir a telemetria.

Executar a aplicação (Ctrl+F5), após alguns segundos vê saída que se parece com:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Saída da aplicação da amostra MQTT":::

No explorador Azure IoT, pode ver que o dispositivo não é um dispositivo IoT Plug and Play:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Dispositivo de plug e play não IoT no explorador Azure IoT":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Faça do dispositivo um dispositivo IoT Plug and Play

O dispositivo IoT Plug and Play deve seguir um conjunto de convenções simples. Se um dispositivo enviar um ID do modelo quando se liga, torna-se um dispositivo IoT Plug and Play.

Nesta amostra, adicione um ID do modelo ao pacote de ligação MQTT. Você passa o ID do modelo como parâmetro de consulta no `USERNAME` e alterar o `api-version` `2020-09-30` para:

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Reconstrua e execute a amostra.

O twin do dispositivo agora inclui o ID do modelo:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Ver o ID do modelo no explorador Azure IoT":::

Agora pode navegar no componente IoT Plug and Play:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Ver componentes no explorador Azure IoT":::

Pode agora modificar o código do dispositivo para implementar a telemetria, propriedades e comandos definidos no seu modelo. Para ver um exemplo de implementação do dispositivo termóstato utilizando a biblioteca Mosquitto, consulte utilizar o [MQTT PnP com Azure IoTHub sem o IoT SDK no Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) no GitHub.

> [!NOTE]
>O cliente utiliza o `IoTHubRootCA_Baltimore.pem` ficheiro de certificado de raiz para verificar a identidade do hub IoT a que se conecta.

### <a name="mqtt-topics"></a>Tópicos de MQTT

As seguintes definições são para os tópicos MQTT que o dispositivo utiliza para enviar informações para o hub IoT. Para saber mais, consulte [Comunicar com o seu hub IoT utilizando o protocolo MQTT](../iot-hub/iot-hub-mqtt-support.md):

* Define `DEVICE_CAPABILITIES_MESSAGE` o tópico que o dispositivo utiliza para reportar as interfaces que implementa.
* Define `DEVICETWIN_PATCH_MESSAGE` o tópico que o dispositivo utiliza para reportar atualizações de propriedade ao hub IoT.
* Define `DEVICE_TELEMETRY_MESSAGE` o tópico que o dispositivo utiliza para enviar telemetria para o seu hub IoT.

Para mais informações sobre o MQTT, visite as Amostras MQTT para o repositório [Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) GitHub.
  
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a modificar um cliente de dispositivo MQTT para seguir as convenções IoT Plug and Play. Para saber mais sobre ioT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Arquitetura](concepts-architecture.md)

Para saber mais sobre o suporte do IoT Hub para o protocolo MQTT, consulte:

> [!div class="nextstepaction"]
> [Comunique com o seu hub IoT usando o protocolo MQTT](../iot-hub/iot-hub-mqtt-support.md)