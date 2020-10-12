---
title: Simulação de dispositivo com monitorização remota IoT - Azure / Microsoft Docs
description: Este guia de como usar o simulador do dispositivo com o acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 21d0587b951d909f0091eccf9a7f86a484bc5051
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324047"
---
# <a name="create-and-test-a-new-simulated-device"></a>Criar e testar um novo dispositivo simulado

O acelerador de solução de monitorização remota permite definir os seus próprios dispositivos simulados. Este artigo mostra-lhe como definir um novo dispositivo de lâmpada simulado e, em seguida, testá-lo localmente. O acelerador de solução inclui dispositivos simulados, como refrigeradores e camiões. No entanto, pode definir os seus próprios dispositivos simulados para testar as suas soluções IoT antes de implementar dispositivos reais.

> [!NOTE]
> Este artigo descreve como usar dispositivos simulados alojados no serviço de simulação do dispositivo. Se pretender criar um dispositivo real, consulte [ligar o seu dispositivo ao acelerador de solução de monitorização remota.](iot-accelerators-connecting-devices.md)

Este guia de como personalizar o microserviço de simulação do dispositivo. Este microserviço faz parte do acelerador de soluções de monitorização remota. Para mostrar as capacidades de simulação do dispositivo, este guia de como guiar utiliza dois cenários na aplicação Contoso IoT:

No primeiro cenário, adiciona-se um novo tipo de telemetria ao tipo de dispositivo **Chiller** existente na Contoso.

No segundo cenário, a Contoso quer testar um novo dispositivo de lâmpada inteligente. Para realizar os testes, cria-se um novo dispositivo simulado com as seguintes características:

*Propriedades*

| Nome                     | Valores                      |
| ------------------------ | --------------------------- |
| Color                    | Branco, Vermelho, Azul            |
| Luminosidade               | 0 a 100                    |
| Vida estimada | Contagem regressiva de 10.000 horas |

*Telemetria*

A tabela seguinte mostra os dados que a lâmpada reporta à nuvem como um fluxo de dados:

| Nome   | Valores      |
| ------ | ----------- |
| Estado | "on", "off" |
| Temperatura | Graus F |
| online | TRUE, false |

> [!NOTE]
> O valor da telemetria **online** é obrigatório para todos os tipos simulados.

*Métodos*

A tabela a seguir mostra as ações que o novo dispositivo suporta:

| Nome        |
| ----------- |
| Ligar   |
| Desligar  |

*Estado inicial*

A tabela a seguir mostra o estado inicial do dispositivo:

| Nome                     | Valores |
| ------------------------ | -------|
| Cor inicial            | Branco  |
| Brilho inicial       | 75     |
| Vida remanescente inicial   | 10,000 |
| Estado inicial da telemetria | "on"   |
| Temperatura inicial da telemetria | 200   |

Para completar os passos neste guia de como fazer, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este guia, precisa:

* Visual Studio Code. Você pode [baixar Visual Studio Code para Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. Pode baixar [.NET Core para Mac, Linux e Windows.](https://www.microsoft.com/net/download)
* [C# para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Carteiro. Você pode baixar [Carteiro para Mac, Windows ou Linux](https://www.getpostman.com/apps).
* Um [hub IoT implantado na sua subscrição Azure](../../articles/iot-hub/iot-hub-create-through-portal.md). Você precisa da cadeia de ligação do hub IoT para completar os passos neste guia. Pode obter a cadeia de ligação do portal Azure.
* Uma base de dados Cosmos DB que utiliza a API SQL e que está configurada para [uma forte consistência.](../../articles/cosmos-db/how-to-manage-database-account.md) Precisa da cadeia de ligação da base de dados Cosmos DB para completar os passos deste guia. Pode obter a cadeia de ligação do portal Azure.

## <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Complete as seguintes tarefas para preparar o seu ambiente de desenvolvimento:

* Descarregue a fonte para o microserviço de simulação do dispositivo.
* Descarregue a fonte para o microserviço do adaptador de armazenamento.
* Executar o microserviço do adaptador de armazenamento localmente.

As instruções deste artigo pressupõem que está a usar o Windows. Se estiver a utilizar outro sistema operativo, poderá ter de ajustar alguns dos caminhos e comandos de ficheiros para se adequar ao seu ambiente.

### <a name="download-the-microservices"></a>Descarregue os microserviços

Faça o download e desaperte os [microserviços](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) de monitorização remota do GitHub para uma localização adequada na sua máquina local. O artigo pressupõe que o nome desta pasta é **serviços de monitorização remota-dotnet-master**.

Faça o download e desaperte o [microserviço](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) de simulação do dispositivo do GitHub para uma localização adequada na sua máquina local. O artigo assume que o nome desta pasta é **device-simulation-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Executar o microserviço do adaptador de armazenamento

Abra a pasta **de adaptação de serviços de monitorização remota-dotnet-master\storage-adapter** no Código do Estúdio Visual. Clique em quaisquer botões **De restauro** para corrigir quaisquer dependências não resolvidas.

Abra o ficheiro **adaptador de armazenamento/WebService/appsettings.ini** e atribua a sua cadeia de ligação Cosmos DB à variável **documentDBConnectionString.**

Para executar o microserviço localmente, clique em **Debug > Start Debugging**.

A janela **terminal** em Visual Studio Code mostra a saída do microserviço em execução, incluindo um URL para a verificação de saúde do serviço web: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status) . Quando navegar para este endereço, o estado deve ser "OK: Vivo e bem".

Deixe o microserviço do adaptador de armazenamento em funcionamento neste caso de Visual Studio Code enquanto completa os próximos passos.

## <a name="modify-the-chiller"></a>Modifique o refrigerador

Nesta secção, adicione um novo tipo de telemetria **de temperatura interna** ao tipo de dispositivo **chiller** existente:

1. Crie uma nova pasta **C:\temp\devicemodels** na sua máquina local.

1. Copie os seguintes ficheiros para a sua nova pasta a partir da cópia descarregada do microserviço de simulação do dispositivo:

    | Origem | Destino |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.jsem | C:\temp\devicemodels\chiller-01.jsem |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Abra a **C:\temp\devicemodels\chiller-01.jsarquivada.**

1. Na secção **Estado Inicial,** adicione as seguintes duas definições:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Na matriz de **telemetria,** adicione a seguinte definição:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Guarde o **C:\temp\devicemodels\chiller-01.jsarquivado.**

1. Abra o ficheiro **C:\temp\devicemodels\scripts\chiller-01-state.js.**

1. Adicione os seguintes campos à variável **do estado:**

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Atualizar a função **principal** da seguinte forma:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Guarde o ficheiro **C:\temp\devicemodels\scripts\chiller-01-state.js.**

## <a name="create-the-lightbulb"></a>Criar a lâmpada

Nesta secção, define-se um novo tipo de dispositivo **de lâmpada:**

1. Crie um ficheiro **C:\temp\devicemodels\lightbulb-01.js** e adicione o seguinte conteúdo:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Guarde as alterações para **C:\temp\devicemodels\lightbulb-01.jsligados**.

1. Crie um **C:\temp\devicemodels\scripts\lightbulb-01-state.js** de ficheiros e adicione o seguinte conteúdo:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Guarde as alterações para **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Crie um **C:\temp\devicemodels\scripts\SwitchOn-method.js** de ficheiros e adicione o seguinte conteúdo:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Guarde as alterações para **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Crie um **C:\temp\devicemodels\scripts\SwitchOff-method.js** de ficheiros e adicione o seguinte conteúdo:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Guarde as alterações para **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Criou agora uma versão personalizada do tipo dispositivo **Chiller** e criou um novo tipo de dispositivo **lightbulb.**

## <a name="test-the-devices"></a>Testar os dispositivos

Nesta secção, teste os tipos de dispositivo que criou nas secções anteriores localmente.

### <a name="run-the-device-simulation-microservice"></a>Executar o microserviço de simulação do dispositivo

Abra a pasta **de simulação-dotnet-master** que descarregou do GitHub num novo exemplo do Visual Studio Code. Clique em quaisquer botões **De restauro** para corrigir quaisquer dependências não resolvidas.

Abra o ficheiro **WebService/appsettings.ini** e atribua a sua cadeia de ligação Cosmos DB à variável **documentdb_connstring** e também modifique as definições da seguinte forma:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Para executar o microserviço localmente, clique em **Debug > Start Debugging**.

A janela **Terminal** em Visual Studio Code mostra a saída do microserviço em execução.

Deixe o microserviço de simulação do dispositivo em funcionamento neste caso de Visual Studio Code enquanto completa os próximos passos.

### <a name="set-up-a-monitor-for-device-events"></a>Criar um monitor para eventos de dispositivos

Nesta secção, utiliza o CLI Azure para configurar um monitor de eventos para visualizar a telemetria enviada dos dispositivos ligados ao seu hub IoT.

O seguinte script pressupõe que o nome do seu hub IoT é **teste de simulação de dispositivo**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Deixe o monitor de eventos em funcionamento enquanto testa os dispositivos simulados.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Criar uma simulação com o tipo de dispositivo de refrigerador atualizado

Nesta secção, utilize a ferramenta Postman para solicitar o microserviço de simulação do dispositivo para executar uma simulação utilizando o tipo de dispositivo de refrigeração atualizado. O Carteiro é uma ferramenta que permite enviar pedidos DE REST para um serviço web. Os ficheiros de configuração do Carteiro que precisa estão na sua cópia local do repositório **de simulação-dotnet** do dispositivo.

Para configurar o Carteiro:

1. Abra o Carteiro na sua máquina local.

1. Clique **em "File > Import**". Em seguida, clique em **Escolher Ficheiros**.

1. Navegue para a pasta **device-simulation-dotnet-master/docs/postman.** Selecione **a solução de simulação de dispositivoS Azure IoT accelerator.postman_collection** e **solução de simulação de dispositivo Azure IoT accelerator.postman_environment** e clique em **Abrir**.

1. Expanda o acelerador de **simulação de dispositivoS Azure IoT** para os pedidos que pode enviar.

1. Clique **em No Environment** e selecione o acelerador de **simulação de dispositivoS Azure IoT**.

Tem agora uma coleção e ambiente carregados no seu espaço de trabalho do Carteiro que pode usar para interagir com o microserviço de simulação do dispositivo.

Para configurar e executar a simulação:

1. Na coleção Postman, **selecione Criar simulação de refrigeração modificada** e clique em **Enviar**. Este pedido cria quatro instâncias do tipo de dispositivo de refrigeração simulado.

1. A saída do monitor do evento na janela Azure CLI mostra a telemetria dos dispositivos simulados, incluindo os novos **valores internal_temperature.**

Para interromper a simulação, selecione o pedido **de simulação Stop** no Carteiro e clique em **Enviar**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Criar uma simulação com o tipo de dispositivo de lâmpada

Nesta secção, utilize a ferramenta Postman para solicitar o microserviço de simulação do dispositivo para executar uma simulação utilizando o tipo de dispositivo de lâmpada. O Carteiro é uma ferramenta que permite enviar pedidos DE REST para um serviço web.

Para configurar e executar a simulação:

1. Na coleção Postman, **selecione Criar simulação de lâmpada** e clique em **Enviar**. Este pedido cria duas instâncias do tipo de dispositivo de lâmpada simulado.

1. A saída do monitor do evento na janela Azure CLI mostra a telemetria das lâmpadas simuladas.

Para interromper a simulação, selecione o pedido **de simulação Stop** no Carteiro e clique em **Enviar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode parar os dois microserviços de funcionamento local nas suas instâncias do Código do Estúdio Visual **(Debug > Stop Debugging).**

Se já não necessitar das instâncias IoT Hub e Cosmos DB, elimine-as da sua assinatura Azure para evitar eventuais encargos desnecessários.

## <a name="next-steps"></a>Passos seguintes

Este guia mostrou-lhe como criar um tipo de dispositivo simulado personalizado e testá-los executando o microserviço de simulação do dispositivo localmente.

O próximo passo sugerido é aprender a implementar os seus tipos de dispositivos simulados personalizados para o [acelerador de solução de monitorização remota.](iot-accelerators-remote-monitoring-deploy-simulated-device.md)
