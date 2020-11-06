---
title: Tutorial - Ligar o código do dispositivo IoT Plug e Play C ao Azure IoT Hub Microsoft Docs
description: Tutorial - Construa e execute o código do dispositivo IoT Plug e Play que utiliza vários componentes e se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0bf0e45f6e9b088567dd48d3eb12ae979ba679e4
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421486"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-applications-running-on-linux-or-windows-to-iot-hub-c"></a>Tutorial: Ligue uma ficha IoT e reproduza várias aplicações de dispositivos componentes em execução no Linux ou Windows ao IoT Hub (C)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play com componentes, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita em C e está incluída no dispositivo Azure IoT SDK para C. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Pode completar este tutorial no Linux ou Windows. Os comandos da concha neste tutorial seguem a convenção de Linux para separadores `/` de caminhos' ', se estiver a seguir o Windows certifique-se de trocar estes separadores por `\` '

Os pré-requisitos diferem por sistema operativo:

### <a name="linux"></a>Linux

Este tutorial assume que estás a usar o Ubuntu Linux. Os passos deste tutorial foram testados com Ubuntu 18.04.

Para completar este tutorial no Linux, instale o seguinte software no seu ambiente Linux local:

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

Para completar este tutorial no Windows, instale o seguinte software no seu ambiente local do Windows:

* [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o Desktop Development com carga de trabalho **C++** quando [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio.
* [Git.](https://git-scm.com/download/)
* [CMake.](https://cmake.org/download/)

## <a name="download-the-code"></a>Transferir o código

Se tiver concluído o [Quickstart: Conecte uma aplicação de dispositivo IoT Plug e Play com amostras em funcionamento no Linux ou Windows para IoT Hub (C)](quickstart-connect-device-c.md) já descarregou o código.

Neste tutorial, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device C SDK.

Abra um pedido de comando numa pasta à sua escolha. Execute o seguinte comando para clonar o [repositório Azure IoT C SDKs e Bibliotecas](https://github.com/Azure/azure-iot-sdk-c) GitHub neste local:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Esta operação deve demorar vários minutos a ser concluída.

## <a name="build-and-run-the-code"></a>Construir e executar o código

Pode construir e executar o código utilizando o Visual Studio ou `cmake` na linha de comando.

### <a name="use-visual-studio"></a>Utilizar o Visual Studio

1. Abra a pasta de raiz do repositório clonado. Após alguns segundos, o suporte **CMake** no Visual Studio cria tudo o que precisa para executar e depurar o projeto.
1. Quando o Visual Studio estiver pronto, no **Solution Explorer,** navegue para a amostra *iothub_client/amostras/pnp/pnp_temperature_controller/*.
1. Clique com o botão direito no ficheiro *pnp_temperature_controller.c* e **selecione Adicionar Configuração Debug**. Selecione **Predefinição**.
1. O Visual Studio abre o *launch.vs.jsem* arquivo. Edite este ficheiro como mostrado no seguinte corte para definir as variáveis ambientais necessárias. Fez uma nota da chave principal de ID e inscrição quando completou [Configurar o seu ambiente para os quickstarts e tutoriais do IoT Plug and Play:](set-up-environment.md)

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Clique com o botão direito no ficheiro *pnp_temperature_controller.c* e selecione **set as Startup Item**.
1. Para rastrear a execução de código no Estúdio Visual, adicione um ponto de rutura à `main` função no ficheiro *pnp_temperature_controller.c.*
1. Agora pode correr e depurar a amostra do menu **Debug.**

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

### <a name="use-cmake-at-the-command-line"></a>Utilizar `cmake` na linha de comando

Para construir a amostra:

1. Crie uma sub-dobragem _de cmáke_ na pasta raiz do dispositivo clonado SDK e navegue nessa pasta:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para gerar e construir os ficheiros do projeto para SDK e amostras:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)da amostra .

Para executar a amostra:

1. A partir da pasta _cmake,_ navegue para a pasta que contém o ficheiro executável e execute-o:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utilize o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um dispositivo de controlador de temperatura IoT Plug e Play. Esta amostra implementa um modelo com [múltiplos componentes.](concepts-components.md) O ficheiro de definição de linguagem de [definição de Gémeos Digitais (DTDL) para o dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, propriedades e comanda os dispositivos.

### <a name="iot-plug-and-play-helper-functions"></a>IoT Plug e Play funções de ajudante

Para esta amostra, o código utiliza algumas funções de ajudante a partir da pasta */comum:*

*pnp_device_client_ll* contém o método de ligação para IoT Plug e Play com o `model-id` incluído como parâmetro: `PnP_CreateDeviceClientLLHandle` .

*pnp_protocol* : contém as funções de ajudante ioT plug e play:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Estas funções de ajudante são genéricas o suficiente para usar no seu próprio projeto. Esta amostra utiliza-as nos três ficheiros que correspondem a cada componente do modelo:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

Por exemplo, no ficheiro *pnp_deviceinfo_component,* a `SendReportedPropertyForDeviceInformation` função utiliza duas das funções de ajudante:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Cada componente da amostra segue este padrão.

### <a name="code-flow"></a>Fluxo de código

A `main` função inicializa a ligação e envia o ID do modelo:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

O código utiliza `PnP_CreateDeviceClientLLHandle` para ligar ao hub IoT, definido como uma `modelId` opção, e configurar o método do dispositivo e os manipuladores de chamadas duplos do dispositivo para métodos diretos e atualizações duplas do dispositivo:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` também contém a informação de ligação. A variável ambiental **IOTHUB_DEVICE_SECURITY_TYPE** determina se a amostra utiliza uma cadeia de ligação ou o serviço de fornecimento de dispositivos para ligar ao hub IoT.

Quando o dispositivo envia um ID modelo, torna-se um dispositivo IoT Plug and Play.

Com os manipuladores de retorno no lugar, o dispositivo reage a duas atualizações e chamadas de método direto:

* Para o dispositivo de chamada dupla, a `PnP_TempControlComponent_DeviceTwinCallback` função chama `PnP_ProcessTwinData` a função para processar os dados. `PnP_ProcessTwinData` usa o *padrão do visitante* para analisar o JSON e, em seguida, visitar cada propriedade, chamando `PnP_TempControlComponent_ApplicationPropertyCallback` cada elemento.

* Para o retorno dos comandos, a `PnP_TempControlComponent_DeviceMethodCallback` função utiliza a função de ajudante para analisar os nomes de comando e componentes:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    `PnP_TempControlComponent_DeviceMethodCallback`Em seguida, a função chama o comando do componente:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

A `main` função inicializa as propriedades apenas de leitura enviadas para o hub IoT:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

A `main` função introduz um loop para atualizar dados de eventos e telemetria para cada componente:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

A `PnP_ThermostatComponent_SendTelemetry` função mostra como usar a `PNP_THERMOSTAT_COMPONENT` estrutura. A amostra utiliza esta estrutura para armazenar informações sobre os dois termóstatos no controlador de temperatura. O código utiliza a `PnP_CreateTelemetryMessageHandle` função para preparar a mensagem e enviá-la:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

A `main` função finalmente destrói os diferentes componentes e fecha a ligação ao centro.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play com componentes a um hub IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de desenvolvedores de modelação IoT Plug e Play](concepts-developer-guide-device-csharp.md)