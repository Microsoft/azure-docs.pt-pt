---
title: Dispositivo simulado em solução de monitorização remota - Azure / Microsoft Docs
description: Este artigo descreve como usar o JavaScript para definir o comportamento de um dispositivo simulado na solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890854"
---
# <a name="implement-the-device-model-behavior"></a>Implementar o comportamento do modelo do dispositivo

O artigo [Compreender o modelo do dispositivo schema](iot-accelerators-remote-monitoring-device-schema.md) descreveu o esquema que define um modelo de dispositivo simulado. Este artigo referia-se a dois tipos de ficheiro JavaScript que implementam o comportamento de um dispositivo simulado:

- **Estado** Ficheiros JavaScript que são executados em intervalos fixos para atualizar o estado interno do dispositivo.
- **Método** Os ficheiros JavaScript que funcionam quando a solução invoca um método no dispositivo.

> [!NOTE]
> Os comportamentos do modelo do dispositivo são apenas para dispositivos simulados hospedados no serviço de simulação do dispositivo. Se pretender criar um dispositivo real, consulte [A Ligação do seu dispositivo ao acelerador de solução](iot-accelerators-connecting-devices.md)de monitorização remota .

Neste artigo, vai aprender a:

>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Defina como um dispositivo simulado responde a uma chamada de método da solução de monitorização remota
> * Desinem os vossos scripts

## <a name="state-behavior"></a>Comportamento do Estado

A secção [de simulação](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) do modelo de dispositivo schema define o estado interno de um dispositivo simulado:

- `InitialState`define valores iniciais para todas as propriedades do objeto do estado do dispositivo.
- `Script`identifica um ficheiro JavaScript que funciona numa programação para atualizar o estado do dispositivo.

O exemplo seguinte mostra a definição do objeto de estado do dispositivo para um dispositivo de refrigeração simulado:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

O estado do dispositivo simulado, `InitialState` tal como definido na secção, é guardado na memória pelo serviço de simulação. A informação do Estado é `main` passada como entrada para a função definida no **chiller-01-state.js**. Neste exemplo, o serviço de simulação executa o ficheiro **chiller-01-state.js** a cada cinco segundos. O script pode modificar o estado do dispositivo simulado.

O seguinte mostra o `main` contorno de uma função típica:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

O `context` parâmetro tem as seguintes propriedades:

- `currentTime`como uma cadeia com formato`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`Por exemplo`Simulated.Chiller.123`
- `deviceModel`Por exemplo`Chiller`

O `state` parâmetro contém o estado do dispositivo tal como mantido pelo serviço de simulação do dispositivo. Este valor `state` é o objeto devolvido `main`pela chamada anterior para .

O exemplo seguinte mostra uma `main` implementação típica do método de manusear o estado do dispositivo mantido pelo serviço de simulação:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

O exemplo que `main` se segue mostra como o método pode simular valores de telemetria que variam ao longo do tempo:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Você pode ver o [refrigerador completo-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) no GitHub.

## <a name="method-behavior"></a>Comportamento do método

A secção [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) do esquema do modelo do dispositivo define os métodos a que um dispositivo simulado responde.

O exemplo seguinte mostra a lista de métodos suportados por um dispositivo de refrigeração simulado:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Cada método tem um ficheiro JavaScript associado que implementa o comportamento do método.

O estado do dispositivo simulado, `InitialState` tal como definido na secção do esquema, é guardado na memória pelo serviço de simulação. A informação do estado é `main` transmitida como entrada para a função definida no ficheiro JavaScript quando o método é chamado. O script pode modificar o estado do dispositivo simulado.

O seguinte mostra o `main` contorno de uma função típica:

```javascript
function main(context, previousState, previousProperties) {

}
```

O `context` parâmetro tem as seguintes propriedades:

- `currentTime`como uma cadeia com formato`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`Por exemplo`Simulated.Chiller.123`
- `deviceModel`Por exemplo`Chiller`

O `state` parâmetro contém o estado do dispositivo tal como mantido pelo serviço de simulação do dispositivo.

O `properties` parâmetro contém as propriedades do dispositivo que são escritas como propriedades reportadas ao dispositivo IoT Hub twin.

Existem três funções globais que pode usar para ajudar a implementar o comportamento do método:

- `updateState`para atualizar o estado detido pelo serviço de simulação.
- `updateProperty`para atualizar uma propriedade de um único dispositivo.
- `sleep`para interromper a execução para simular uma tarefa de longa duração.

O exemplo seguinte mostra uma versão abreviada do script **IncreasePressure-method.js** usado pelos dispositivos refrigeradores simulados:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Depurando ficheiros de script

Não é possível anexar um debugger ao intérprete Javascript usado pelo serviço de simulação do dispositivo para executar scripts de estado e método. No entanto, pode registar informações no registo de serviço. A `log()` função incorporada permite-lhe guardar informações para rastrear e depurar a execução da função.

Se houver um erro de sintaxe, o `Jint.Runtime.JavaScriptException` intérprete falha e escreve uma entrada no registo de serviço.

O [artigo de execução local do serviço](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) no GitHub mostra-lhe como executar o serviço de simulação do dispositivo localmente. Executar o serviço localmente torna mais fácil depurar os seus dispositivos simulados antes de os colocar na nuvem.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu como definir o comportamento do seu próprio modelo de dispositivo simulado personalizado. Este artigo mostrou-lhe como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Defina como um dispositivo simulado responde a uma chamada de método da solução de monitorização remota
> * Desinem os vossos scripts

Agora que aprendeu a especificar o comportamento de um dispositivo simulado, o próximo passo sugerido é aprender a [criar um dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

Para obter mais informações sobre a solução de Monitorização Remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
