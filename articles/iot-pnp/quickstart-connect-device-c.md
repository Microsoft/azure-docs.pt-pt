---
title: Ligue o código do dispositivo IoT Plug e Play Preview da amostra C ao IoT Hub Microsoft Docs
description: Construa e execute o código do dispositivo de amostra IoT Plug e Play Preview no Linux ou Windows que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9011c56096d61e50ae3655a76a396ec3f2dd97c5
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352972"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-or-windows-to-iot-hub-c"></a>Quickstart: Conecte uma aplicação de dispositivo IoT Plug e Reprodução de amostras em funcionamento no Linux ou Windows para IoT Hub (C)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. A aplicação da amostra está escrita em C e está incluída no dispositivo Azure IoT SDK para C. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Pode executar este arranque rápido no Linux ou Windows. Os comandos da concha neste quickstart seguem a convenção linux para separadores `/` de caminhos' ', se você estiver seguindo o Windows certifique-se de trocar estes separadores por `\` '

Os pré-requisitos diferem por sistema operativo:

### <a name="linux"></a>Linux

Este quickstart assume que estás a usar o Ubuntu Linux. Os passos neste quickstart foram testados com ubuntu 18.04.

Para completar este arranque rápido no Linux, instale o seguinte software no seu ambiente Linux local:

Instale **GCC,** **Git,** **cmake,** e todas as dependências necessárias utilizando o `apt-get` comando:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão é `cmake` superior a **2.8.12** e a versão do **GCC** é superior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Para completar este arranque rápido no Windows, instale o seguinte software no ambiente local do Windows:

* [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o Desktop Development com carga de trabalho **C++** quando [instalar](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) o Visual Studio.
* [Git.](https://git-scm.com/download/)
* [CMake.](https://cmake.org/download/)

### <a name="azure-iot-explorer"></a>Explorador de Azure IoT

Para interagir com o dispositivo de amostra na segunda parte deste arranque rápido, utilize a ferramenta **exploradora Azure IoT.** [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Também pode utilizar a ferramenta exploradora Azure IoT para encontrar a cadeia de ligação do hub IoT.

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Transferir o código

Neste arranque rápido, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device C SDK.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [repositório Azure IoT C SDKs e Bibliotecas](https://github.com/Azure/azure-iot-sdk-c) GitHub neste local:

```cmd\bash
git clone --depth 1 --recurse-submodules https://github.com/Azure/azure-iot-sdk-c.git
```

Espere que esta operação leve vários minutos para ser concluída.

## <a name="build-the-code"></a>Compilar o código

Utiliza o dispositivo SDK para construir o código de amostra incluído:

1. Crie um subdiretório _cmake_ na pasta raiz do dispositivo SDK e navegue para essa pasta:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para construir o SDK e amostras:

    ```cmd\bash
    cmake ..
    cmake --build .
    ```

> [!TIP]
> No Windows, pode abrir a solução gerada pelo `cmake` comando no Visual Studio 2019. Abra o arquivo do projeto *azure_iot_sdks.sln* no diretório _cmake_ e coloque o projeto **pnp_simple_thermostat** como o projeto de arranque na solução. Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Para executar a aplicação da amostra no SDK que simula um dispositivo IoT Plug and Play enviando telemetria para o seu hub IoT:

Crie uma variável ambiental chamada **IOTHUB_DEVICE_CONNECTION_STRING** para armazenar a cadeia de ligação do dispositivo que fez anteriormente.

A partir da pasta _cmake,_ navegue para a pasta que contém o ficheiro executável e execute-o:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd  iothub_client\samples\pnp\pnp_simple_thermostat\Debug\pnp_simple_thermostat.exe
```

> [!TIP]
> Para rastrear a execução de código no Estúdio Visual no Windows, adicione um ponto de rutura à `main` função no ficheiro _pnp_simple_thermostat.c._

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um simples dispositivo de termóstato IoT Plug e Play. O modelo que esta amostra implementa não utiliza [componentes](concepts-components.md)IoT Plug e Play . O [ficheiro do modelo DTDL para o dispositivo termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) define a telemetria, as propriedades e o comando dos dispositivos.

O código do dispositivo utiliza a função padrão para ligar ao seu hub IoT:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

O dispositivo envia o ID modelo do modelo DTDL que implementa no pedido de ligação. Um dispositivo que envia um ID modelo é um dispositivo IoT Plug and Play:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

O código que atualiza propriedades, lida com comandos e envia telemetria é idêntico ao código de um dispositivo que não utiliza as convenções IoT Plug and Play.

O código utiliza a biblioteca Parson para analisar os objetos JSON nas cargas enviadas do seu hub IoT:

```c
// JSON parser
#include "parson.h"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Como fazer: Ligar e interagir com um dispositivo](howto-develop-solution.md)
