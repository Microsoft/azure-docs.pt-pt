---
title: Utilize o MQTT para criar um cliente IoT Plug e Play Preview Microsoft Docs
description: Utilize o protocolo MQTT diretamente para criar um cliente de dispositivo IoT Plug e Play Preview sem utilizar os SDKs de dispositivo IoT Azure
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535692"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>Utilize o MQTT para desenvolver um cliente de dispositivo de pré-visualização IoT Plug e Play

Deverá utilizar um dos SDKs de dispositivoS Azure IoT para construir os seus clientes ioT plug e play, se possível. No entanto, em cenários como a utilização de um dispositivo constrangido à memória, poderá ser necessário utilizar uma biblioteca MQTT para comunicar com o seu hub IoT.

A amostra deste tutorial utiliza a biblioteca [Eclipse Mosquitto](http://mosquitto.org/) MQTT e o Visual Studio. Os passos neste tutorial assumem que está a usar o Windows na sua máquina de desenvolvimento.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial no Windows, instale o seguinte software no seu ambiente local do Windows:

* [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o Desenvolvimento do Ambiente de Trabalho com carga de trabalho **C++** quando [instalar](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) o Visual Studio
* [Rio Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Explorador de Azure IoT](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a assinatura de acesso partilhado do dispositivo para ligar ao seu hub. Tome nota desta corda, use-a mais tarde neste tutorial:

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Utilize o fio de ligação do hub IoT para configurar a ferramenta **exploradora Azure IoT:**

1. Lance a ferramenta **exploradora Azure IoT.**
1. Na página **Definições,** cole a cadeia de ligação do hub IoT nas **definições de configurações** da App.
1. **Selecione Guardar e Ligar**.
1. O dispositivo que adicionou anteriormente está na lista de dispositivos na página principal.

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

No projeto **TelemetryMQTTWin32,** abra o ficheiro de origem **MQTT_Mosquitto.cpp.** Atualize as definições de informação de ligação com os detalhes do dispositivo que fez anteriormente. Substitua o espaço reservado para o:

* `IOTHUBNAME`identificador com o nome do hub IoT que criou.
* `DEVICEID`identificador com o nome do dispositivo que criou.
* `PWD`identifier com o valor de assinatura de acesso partilhado que gerou para o dispositivo.

Verifique se o código está a funcionar corretamente, iniciando o explorador Azure IoT, comece a ouvir a telemetria.

Executar a aplicação (Ctrl+F5), após alguns segundos vê saída que se parece com:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Saída da aplicação da amostra MQTT":::

No explorador Azure IoT, pode ver que o dispositivo não é um dispositivo IoT Plug and Play:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Dispositivo de plug e play não IoT no explorador Azure IoT":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Faça do dispositivo um dispositivo IoT Plug and Play

O dispositivo IoT Plug and Play deve seguir um conjunto de convenções simples. Se um dispositivo enviar um ID do modelo quando se liga, torna-se um dispositivo IoT Plug and Play.

Nesta amostra, adicione um ID** do modelo ao pacote de ligação MQTT. Você passa o ID do modelo como parâmetro de consulta no `USERNAME` e alterar o `api-version` `2020-05-31-preview` para:

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
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
> Por predefinição, uma assinatura de acesso partilhado é válida apenas por 60 minutos.

> [!NOTE]
>O cliente utiliza o `IoTHubRootCA_Baltimore.pem` ficheiro de certificado de raiz para verificar a identidade do hub IoT a que se conecta.

### <a name="mqtt-topics"></a>Tópicos de MQTT

As seguintes definições são para os tópicos MQTT que o dispositivo utiliza para enviar informações para o hub IoT. Para saber mais, consulte [Comunicar com o seu hub IoT utilizando o protocolo MQTT](../iot-hub/iot-hub-mqtt-support.md):

* Define `DEVICE_CAPABILITIES_MESSAGE` o tópico que o dispositivo utiliza para reportar as interfaces que implementa.
* Define `DEVICETWIN_PATCH_MESSAGE` o tópico que o dispositivo utiliza para reportar atualizações de propriedade ao hub IoT.
* Define `DEVICE_TELEMETRY_MESSAGE` o tópico que o dispositivo utiliza para enviar telemetria para o seu hub IoT.

Para mais informações sobre o MQTT, visite as Amostras MQTT para o repositório [Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) GitHub.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a modificar um cliente de dispositivo MQTT para seguir as convenções IoT Plug and Play. Para saber mais sobre ioT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Arquitetura](concepts-architecture.md)

Para saber mais sobre o suporte do IoT Hub para o protocolo MQTT, consulte:

> [!div class="nextstepaction"]
> [Comunique com o seu hub IoT usando o protocolo MQTT](../iot-hub/iot-hub-mqtt-support.md)
