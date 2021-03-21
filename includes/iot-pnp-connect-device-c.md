---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f626d6058374d52b5a1fd07bfdb229cfb715ca45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612169"
---
Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. A aplicação da amostra está escrita em C e está incluída no dispositivo Azure IoT SDK para C. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

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

* [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o Desktop Development com carga de trabalho **C++** quando [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio.
* [Git.](https://git-scm.com/download/)
* [CMake.](https://cmake.org/download/)

## <a name="download-the-code"></a>Transferir o código

Neste arranque rápido, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device C SDK.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [repositório Azure IoT C SDKs e Bibliotecas](https://github.com/Azure/azure-iot-sdk-c) GitHub neste local:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
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
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> No Windows, pode abrir a solução gerada pelo `cmake` comando no Visual Studio 2019. Abra o arquivo *azure_iot_sdks.sln* do projeto no _diretório cmake_ e coloque o projeto **pnp_simple_thermostat** como o projeto de arranque na solução. Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)da amostra .

Para executar a aplicação da amostra no SDK que simula um dispositivo IoT Plug and Play enviando telemetria para o seu hub IoT:

A partir da pasta _cmake,_ navegue para a pasta que contém o ficheiro executável e execute-o:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Para rastrear a execução de código no Estúdio Visual no Windows, adicione um ponto de rutura à `main` função no ficheiro _pnp_simple_thermostat.c._

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um simples dispositivo de termóstato IoT Plug e Play. O modelo que esta amostra implementa não utiliza [componentes](../articles/iot-pnp/concepts-modeling-guide.md)IoT Plug e Play . O [ficheiro do modelo DTDL para o dispositivo termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) define a telemetria, as propriedades e o comando dos dispositivos.

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
