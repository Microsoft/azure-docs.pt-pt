---
title: Tutorial - Ligue um módulo genérico de ficha e reprodução Azure IoT | Microsoft Docs
description: Tutorial - Utilize a amostra C# IoT Plug e o código do dispositivo De reprodução num módulo genérico.
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33eaa1ea928cc0650c91948c70d46daf499f3b4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831222"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Tutorial: Ligue um módulo IoT Plug and Play (C#)

Este tutorial mostra-lhe como ligar um [módulo](../iot-hub/iot-hub-devguide-module-twins.md)genérico IoT Plug and Play .

Um dispositivo é um dispositivo IoT Plug and Play se publicar o seu ID modelo quando se conecta a um hub IoT e implementa as propriedades e métodos descritos no modelo Digital Twins Definition Language (DTDL) identificado pelo ID do modelo. Para saber mais sobre como os dispositivos utilizam um DTDL e um ID de modelo, consulte [o guia de desenvolvimento IoT Plug and Play](./concepts-developer-guide-device.md). Os módulos utilizam iDs de modelo e modelos DTDL da mesma forma.

Para demonstrar como implementar um módulo IoT Plug and Play, este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Adicione um dispositivo com um módulo ao seu hub IoT.
> * Converta a amostra do dispositivo termóstato C# num módulo genérico.
> * Utilize o serviço SDK para interagir com o módulo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este tutorial no Windows, instale o seguinte software no seu ambiente local do Windows:

* [Estúdio Visual (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/).
* [Git.](https://git-scm.com/download/)

Utilize a ferramenta exploradora Azure IoT para adicionar um novo dispositivo chamado **meu módulo-dispositivo** ao seu hub IoT.

Adicione um módulo chamado **meu módulo** ao **meu módulo-dispositivo:**

1. Na ferramenta exploradora Azure IoT, navegue para o dispositivo **do meu módulo.**

1. Selecione **a identidade do módulo** e, em seguida, selecione + **Adicionar**.

1. Introduza **o meu módulo** como nome de identidade do módulo e selecione **Save**.

1. Na lista de identidades do módulo, selecione **o meu módulo.** Em seguida, copie a cadeia de ligação primária. Utilize esta cadeia de ligação do módulo mais tarde neste tutorial.

1. Selecione o **separador twin do Módulo** e note que não existem propriedades desejadas ou reportadas:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Transferir o código

Se ainda não o fez, clone o repositório Azure IoT Hub Device C# SDK GitHub para a sua máquina local:

Abra um pedido de comando numa pasta à sua escolha. Utilize o seguinte comando para clonar o repositório [Azure IoT C# Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub neste local:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Preparar o projeto

Para abrir e preparar o projeto da amostra:

1. Abra o ficheiro do projeto *azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* project file in Visual Studio 2019.

1. No Estúdio Visual, navegue para **Project > Thermostat Properties > Debug**. Em seguida, adicione as seguintes variáveis ambientais ao projeto:

    | Name | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | conexãoStragem |
    | IOTHUB_MODULE_CONNECTION_STRING | A cadeia de ligação do módulo que fez uma nota de anteriormente |

    Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md)da amostra .

## <a name="modify-the-code"></a>Modificar o código

Para modificar o código para funcionar como um módulo em vez de um dispositivo:

1. No Estúdio Visual, abra *o Parâmetro.cs* e modifique a linha que define a variável **PrimaryConnectionString** da seguinte forma:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. No Visual Studio, abra *o Programa.cs* e substitua as sete instâncias da `DeviceClient` aula pela `ModuleClient` turma.

    > [!TIP]
    > Utilize a pesquisa do Estúdio Visual e substitua a funcionalidade por **Match case** e Match toda a **palavra** ativada para substituir `DeviceClient` por `ModuleClient` .

1. No Visual Studio, abra *o termóstato.cs* e substitua ambas as instâncias da `DeviceClient` classe pela classe da seguinte `ModuleClient` forma.

1. Guarde as alterações nos ficheiros modificados.

Se executar o código e, em seguida, utilizar o explorador Azure IoT para ver o módulo gémeo atualizado, vê o dispositivo atualizado gémeo com o ID do modelo e a propriedade reportada pelo módulo:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Interaja com um módulo de dispositivo

Os SDKs de serviço permitem-lhe recuperar o ID do modelo de dispositivos e módulos IoT Plug e Play conectados. Pode utilizar os SDKs de serviço para definir propriedades e chamar comandos:

1. Em outro caso de Visual Studio, abra o projeto *azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj.*

1. No Estúdio Visual, navegue para **Project > Thermostat Properties > Debug**. Em seguida, adicione as seguintes variáveis ambientais ao projeto:

    | Name | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | meu módulo-dispositivo |
    | IOTHUB_CONNECTION_STRING | O valor que fez notar quando completou [Configurar o seu ambiente](set-up-environment.md) |

    > [!TIP]
    > Também pode encontrar a sua cadeia de ligação do hub IoT na ferramenta exploradora Azure IoT.

1. Abra o ficheiro *.cs Programa* e modifique a linha que chama um comando da seguinte forma:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. No ficheiro *Program.cs,* modifique a linha que recupera o twin do dispositivo da seguinte forma:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Certifique-se de que a amostra do cliente do módulo ainda está em funcionamento e, em seguida, execute esta amostra de serviço. A saída da amostra de serviço mostra o ID do modelo do twin do dispositivo e a chamada de comando:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    A saída do cliente do módulo mostra a resposta do manipulador de comando:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Converta-se num módulo IoT Edge

Para converter esta amostra para funcionar como um módulo IoT Plug e Play IoT Edge, tem de contentorizar a aplicação. Não precisas de fazer mais alterações de código. A variável ambiente de cadeia de ligação é injetada pelo tempo de execução IoT Edge no arranque. Para saber mais, consulte [o Use Visual Studio 2019 para desenvolver e depurar módulos para Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md).

Para aprender a implantar o seu módulo contentorizado, consulte:

* [Executar Azure IoT Edge em Máquinas Virtuais Ubuntu](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Instale o tempo de execução Azure IoT Edge nos sistemas Linux baseados em Debian](../iot-edge/how-to-install-iot-edge.md).

Pode utilizar a ferramenta Azure IoT Explorer para ver:

* O ID do modelo do seu dispositivo IoT Edge no módulo twin.
* Telemetria do dispositivo IoT Edge.
* IoT Edge módulo twin propriedades atualizações de propriedade desencadeando notificações IoT Plug e Play.
* O módulo IoT Edge reage aos comandos IoT Plug and Play.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play com módulos a um hub IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de desenvolvedores de modelação IoT Plug e Play](./concepts-developer-guide-device.md)