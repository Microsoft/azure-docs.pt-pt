---
title: incluir ficheiro
description: incluir ficheiro
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 09/28/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 5eb3c08792b760bf66e443f79762d91210706c92
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435117"
---
No primeiro cenário, vai adicionar um novo tipo de telemetria para a Contoso existentes **Chiller** tipo de dispositivo.

No segundo cenário, a Contoso quer testar um novo dispositivo de lâmpada inteligente. Para executar os testes, crie um novo dispositivo simulado com as seguintes características:

*Propriedades*

| Nome                     | Valores                      |
| ------------------------ | --------------------------- |
| Cor                    | White, vermelho, azul            |
| Brilho               | 0 a 100                    |
| Estimado restante da vida | Contagem decrescente de 10.000 horas |

*Telemetria*

A tabela seguinte mostra os dados que na lâmpada relatórios para a cloud como um fluxo de dados:

| Nome   | Valores      |
| ------ | ----------- |
| Estado | "on", "desativado" |
| Temperatura | Graus F |
| Online | VERDADEIRO, FALSO |

> [!NOTE]
> O **online** valor de telemetria é obrigatório para tipos de todos os simulado.

*Métodos*

A tabela seguinte mostra as ações que suporta o novo dispositivo:

| Nome        |
| ----------- |
| Mudar de   |
| Optar por desativar  |

*Estado inicial*

A tabela seguinte mostra o estado inicial do dispositivo:

| Nome                     | Valores |
| ------------------------ | -------|
| Cor inicial            | Branco  |
| Brilho inicial       | 75     |
| Vida restante inicial   | 10,000 |
| Estado inicial de telemetria | "on"   |
| Temperatura de telemetria inicial | 200   |

Para concluir os passos neste guia de procedimentos, precisa de uma subscrição do Azure Active Directory.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de procedimentos, terá de:

* Visual Studio Code. Pode [transferir o Visual Studio Code para Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET core. Pode baixar [.NET Core para Mac, Linux e Windows](https://www.microsoft.com/net/download).
* [C# para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman. Pode baixar [Postman para Mac, Windows ou Linux](https://www.getpostman.com/apps).
* Uma [hub IoT implementado na sua subscrição do Azure](../articles/iot-hub/iot-hub-create-through-portal.md). Terá de cadeia de ligação do hub IoT para concluir os passos neste guia. Pode obter a cadeia de ligação do portal do Azure.
* Uma base de dados do Cosmos DB que utiliza a API de SQL e que está configurada para [consistência forte](../articles/cosmos-db/manage-account.md). Terá de cadeia de ligação da base de dados do Cosmos DB para concluir os passos neste guia. Pode obter a cadeia de ligação do portal do Azure.

## <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Conclua as seguintes tarefas para preparar o ambiente de desenvolvimento:

* Transfira a origem para os microsserviços de simulação do dispositivo.
* Transfira a origem para os microsserviços de placa de armazenamento.
* Execute localmente o microsserviços de placa de armazenamento.

As instruções neste artigo partem do princípio de que está a utilizar o Windows. Se estiver a utilizar outro sistema operacional, poderá ter de ajustar alguns dos caminhos de ficheiro e comandos de acordo com seu ambiente.

### <a name="download-the-microservices"></a>Transferir os microsserviços

Transfira e deszipe o [microsserviços de monitorização remota](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) do GitHub para um local adequado no seu computador local. O artigo pressupõe que o nome desta pasta está **remote-monitoring-serviços-dotnet-mestre**.

Transfira e deszipe o [microsserviços de simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) do GitHub para um local adequado no seu computador local. O artigo pressupõe que o nome desta pasta está **dispositivo-simulação-dotnet-mestre**.

### <a name="run-the-storage-adapter-microservice"></a>Execute os microsserviços de placa de armazenamento

Abra o **remote-monitoring-services-dotnet-master\storage-adapter** pasta no Visual Studio Code. Clique em qualquer **restaurar** botões para corrigir quaisquer não resolvido dependências.

Abra o **.vscode/launch.json** do ficheiro e atribuir a cadeia de ligação do Cosmos DB para o **PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING** variável de ambiente.

<!-- Open the **WebService/appsettings.ini** file and assign your Cosmos DB connection string to the **documentdb_connstring** configuration setting.-->

Para executar os microsserviços localmente, clique em **depurar > Iniciar depuração**.

O **Terminal** janela no Visual Studio Code mostra a saída dos microsserviços em execução, incluindo um URL para a verificação de estado de funcionamento do serviço web: [ http://127.0.0.1:9022/v1/status ](http://127.0.0.1:9022/v1/status). Ao navegar para este endereço, o estado deve ser "OK: ativo e bem".

Deixe os microsserviços de placa de armazenamento em execução nesta instância do Visual Studio Code enquanto efetua os passos seguintes.

## <a name="modify-the-chiller"></a>Modificar o chiller

Nesta secção, adiciona um novo **temperatura interna** tipo de telemetria para o existente **Chiller** tipo de dispositivo:

1. Crie uma nova pasta **C:\temp\devicemodels** no seu computador local.

1. Copie os seguintes ficheiros para a nova pasta a partir da cópia transferida de microsserviços de simulação do dispositivo:

    | Origem | Destino |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.JSON | C:\temp\devicemodels\chiller-01.JSON |
    | Services\data\devicemodels\scripts\chiller-01-State.js | C:\temp\devicemodels\scripts\chiller-01-State.js |
    | Services\data\devicemodels\scripts\Reboot-Method.js | C:\temp\devicemodels\scripts\Reboot-Method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-Method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-Method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-Method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-Method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-Method.js | C:\temp\devicemodels\scripts\IncreasePressure-Method.js |

1. Abra o **C:\temp\devicemodels\chiller-01.json** ficheiro.

1. Na **InitialState** secção, adicione as seguintes duas definições:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Na **telemetria** matriz, adicione a seguinte definição:

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

1. Guardar a **C:\temp\devicemodels\chiller-01.json** ficheiro.

1. Abra o **C:\temp\devicemodels\scripts\chiller-01-state.js** ficheiro.

1. Adicione os campos seguintes para o **estado** variável:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Atualização do **principal** funcionar da seguinte forma:

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

1. Guardar a **C:\temp\devicemodels\scripts\chiller-01-state.js** ficheiro.

## <a name="create-the-lightbulb"></a>Criar a lâmpada

Nesta secção, vai definir uma nova **lâmpada** tipo de dispositivo:

1. Crie um ficheiro **C:\temp\devicemodels\lightbulb-01.json** e adicione o seguinte conteúdo:

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

    Guardar as alterações ao **C:\temp\devicemodels\lightbulb-01.json**.

1. Crie um ficheiro **C:\temp\devicemodels\scripts\lightbulb-01-state.js** e adicione o seguinte conteúdo:

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

    Guardar as alterações ao **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Crie um ficheiro **C:\temp\devicemodels\scripts\SwitchOn-method.js** e adicione o seguinte conteúdo:

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

    Guardar as alterações ao **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Crie um ficheiro **C:\temp\devicemodels\scripts\SwitchOff-method.js** e adicione o seguinte conteúdo:

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

    Guardar as alterações ao **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Acabou de criar uma versão personalizada dos **Chiller** tipo de dispositivo e criado um novo **lâmpada** tipo de dispositivo.

## <a name="test-the-devices"></a>Os dispositivos de teste

Nesta secção, vai testar os tipos de dispositivo que criou nas secções anteriores localmente.

### <a name="run-the-device-simulation-microservice"></a>Execute os microsserviços de simulação do dispositivo

Abra o **dispositivo-simulação-dotnet-mestre** pasta transferiu a partir do GitHub numa nova instância do Visual Studio Code. Clique em qualquer **restaurar** botões para corrigir quaisquer não resolvido dependências.

Abra o **.vscode/launch.json** do ficheiro e atribuir a cadeia de ligação do IoT Hub para o **PCS_IOTHUB_CONNSTRING** variável de ambiente. No mesmo ficheiro, adicione a **PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING** variável de ambiente e atribuir a cadeia de ligação da base de dados do Cosmos DB.

Abra o **WebService/Properties/launchSettings.json** do ficheiro e atribuir a cadeia de ligação do IoT Hub para o **PCS_IOTHUB_CONNSTRING** variável de ambiente.

Abra o **WebService/appsettings.ini** de ficheiros e modifique as definições da seguinte forma:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Para executar os microsserviços localmente, clique em **depurar > Iniciar depuração**.

O **Terminal** janela no Visual Studio Code mostra a saída dos microsserviços em execução.

Deixe os microsserviços de simulação de dispositivo em execução nesta instância do Visual Studio Code enquanto efetua os passos seguintes.

### <a name="set-up-a-monitor-for-device-events"></a>Configurar um monitor de eventos do dispositivo

Nesta secção, vai utilizar a CLI do Azure para configurar um monitor de eventos para ver a telemetria enviada a partir dos dispositivos ligados ao seu hub IoT.

O seguinte script parte do princípio de que é o nome do IoT hub **testes de simulação de dispositivo**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Deixe o monitor de eventos em execução enquanto testa os dispositivos simulados.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Criar uma simulação com o tipo de dispositivo chiller atualizado

Nesta secção, vai utilizar a ferramenta de Postman para pedir os microsserviços de simulação de dispositivo para executar uma simulação usando o tipo de dispositivo chiller atualizado. Postman é uma ferramenta que permite que envie pedidos REST para um serviço web. Os ficheiros de configuração do Postman que precisa estão em sua cópia local do **simulação de dispositivo-dotnet** repositório.

Para configurar o Postman:

1. Abra o Postman no seu computador local.

1. Clique em **ficheiro > importar**. Em seguida, clique em **escolher ficheiros**.

1. Navegue para o **dispositivo-simulação-dotnet-master/docs/postman** pasta. Selecione **accelerator.postman_collection de solução de simulação de dispositivo do Azure IoT** e **accelerator.postman_environment de solução de simulação de dispositivo do IoT do Azure** e clique em **abra**.

1. Expanda a **acelerador de solução de simulação de dispositivo do IoT do Azure** aos pedidos de pode enviar.

1. Clique em **ambiente sem** e selecione **acelerador de solução de simulação de dispositivo do IoT do Azure**.

Tem agora uma coleção e carregado na sua área de trabalho do Postman que pode utilizar para interagir com os microsserviços de simulação de dispositivo de ambiente.

Para configurar e executar a simulação:

1. Na coleção do Postman, selecione **criar modificado simulação chiller** e clique em **enviar**. Este pedido cria quatro instâncias do tipo de dispositivo simulado chiller.

1. O resultado de monitor de eventos na janela da CLI do Azure mostra a telemetria dos dispositivos simulados, incluindo o novo **internal_temperature** valores.

Para parar a simulação, selecione o **parar simulação** pedido no Postman e clique em **enviar**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Criar uma simulação com o tipo de dispositivo de lâmpada elétrica

Nesta secção, vai utilizar a ferramenta de Postman para pedir os microsserviços de simulação de dispositivo para executar uma simulação usando o tipo de dispositivo de lâmpada elétrica. Postman é uma ferramenta que permite que envie pedidos REST para um serviço web.

Para configurar e executar a simulação:

1. Na coleção do Postman, selecione **criar a simulação de lâmpada elétrica** e clique em **enviar**. Este pedido cria duas instâncias do tipo de dispositivo simulado lâmpada.

1. O resultado de monitor de eventos na janela da CLI do Azure mostra a telemetria das lâmpadas simuladas.

Para parar a simulação, selecione o **parar simulação** pedido no Postman e clique em **enviar**.

## <a name="clean-up-resources"></a>Limpar recursos

Pode parar os dois microsserviços localmente em execução nas suas instâncias do Visual Studio Code (**depurar > parar depuração**).

Se já não necessitar de instâncias do IoT Hub e do Cosmos DB, eliminá-los a partir da sua subscrição do Azure para evitar eventuais encargos desnecessários.