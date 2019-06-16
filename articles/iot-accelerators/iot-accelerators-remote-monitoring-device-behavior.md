---
title: Simulated o comportamento do dispositivo na solução de monitorização remota - Azure | Documentos da Microsoft
description: Este artigo descreve como utilizar JavaScript para definir o comportamento de um dispositivo simulado na solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 04d2ad2f0e86ee977600af86a2ffd1e9d7680375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65823408"
---
# <a name="implement-the-device-model-behavior"></a>Implementar o comportamento de modelo do dispositivo

O artigo [compreender o esquema do modelo de dispositivo](iot-accelerators-remote-monitoring-device-schema.md) descreve o esquema que define um modelo de dispositivo simulado. Esse artigo chamado de dois tipos de arquivo JavaScript que implementam o comportamento de um dispositivo simulado:

- **Estado** JavaScript ficheiros que são executados em intervalos fixos para atualizar o estado interno do dispositivo.
- **Método** JavaScript ficheiros que são executados quando a solução invoca um método no dispositivo.

> [!NOTE]
> Comportamentos de modelo do dispositivo são apenas para dispositivos simulados alojados no serviço de simulação do dispositivo. Se quiser criar um dispositivo real, veja [ligar o seu dispositivo para o acelerador de solução de monitorização remota](iot-accelerators-connecting-devices.md).

Neste artigo, vai aprender a:

>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método da solução de monitorização remota
> * Os scripts de depuração

## <a name="state-behavior"></a>Comportamento de estado

O [simulação](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) seção do esquema de modelo do dispositivo define o estado interno de um dispositivo simulado:

- `InitialState` Define os valores iniciais para todas as propriedades do objeto de estado do dispositivo.
- `Script` identifica um arquivo JavaScript que é executada numa agenda, para atualizar o estado do dispositivo.

O exemplo seguinte mostra a definição do objeto de estado do dispositivo para um dispositivo simulado chiller:

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

O estado do dispositivo simulado, conforme definido no `InitialState` secção, é mantida na memória pelo serviço de simulação. As informações de estado são passadas como entrada para o `main` função definida **chiller-01-state.js**. Neste exemplo, o serviço de simulação é executado o **chiller-01-state.js** ficheiro a cada cinco segundos. O script pode modificar o estado do dispositivo simulado.

O seguinte mostra o contorno da típica `main` função:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

O `context` parâmetro tem as seguintes propriedades:

- `currentTime` como uma cadeia com formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, por exemplo `Simulated.Chiller.123`
- `deviceModel`, por exemplo `Chiller`

O `state` parâmetro contém o estado do dispositivo, como mantidos pelo serviço de simulação do dispositivo. Este valor é o `state` objeto retornado pela chamada anterior para `main`.

O exemplo seguinte mostra uma implementação típica do `main` método para lidar com o estado do dispositivo mantido pelo serviço de simulação:

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

A exemplo a seguir mostra como o `main` método pode simular a valores de telemetria que variam ao longo do tempo:

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

Pode ver o concluída [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) no GitHub.

## <a name="method-behavior"></a>Comportamento do método

O [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) seção do esquema de modelo do dispositivo define os métodos de um dispositivo simulado responde a.

O exemplo seguinte mostra a lista de métodos suportados por um dispositivo simulado chiller:

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

Cada método possui um arquivo JavaScript associado que implementa o comportamento do método.

O estado do dispositivo simulado, conforme definido no `InitialState` seção do esquema, é mantida na memória pelo serviço de simulação. As informações de estado são passadas como entrada para o `main` função definida no arquivo JavaScript, quando o método é chamado. O script pode modificar o estado do dispositivo simulado.

O seguinte mostra o contorno da típica `main` função:

```javascript
function main(context, previousState, previousProperties) {

}
```

O `context` parâmetro tem as seguintes propriedades:

- `currentTime` como uma cadeia com formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, por exemplo `Simulated.Chiller.123`
- `deviceModel`, por exemplo `Chiller`

O `state` parâmetro contém o estado do dispositivo, como mantidos pelo serviço de simulação do dispositivo.

O `properties` parâmetro contém as propriedades do dispositivo que são escritas como propriedades comunicadas para o dispositivo duplo do IoT Hub.

Existem três funções globais, que pode usar para ajudar a implementar o comportamento do método:

- `updateState` Para atualizar o estado mantido pelo serviço de simulação.
- `updateProperty` Para atualizar uma propriedade de único dispositivo.
- `sleep` para colocar em pausa a execução para simular uma tarefa de execução longa.

O exemplo seguinte mostra uma versão abreviada do **IncreasePressure method.js** script utilizado pelos dispositivos simulados chiller:

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

## <a name="debugging-script-files"></a>Arquivos de script de depuração

Não é possível anexar um depurador para o interpretador de Javascript utilizado pelo serviço de simulação do dispositivo para executar scripts de estado e o método. No entanto, pode registar informações no registo do serviço. O incorporado `log()` função permite-lhe guardar as informações para controlar e depurar a execução de função.

Se houver um erro de sintaxe o interpretador falha, e grava uma `Jint.Runtime.JavaScriptException` entrada no registo de serviço.

O [que executa o serviço localmente](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) artigo no GitHub mostra-lhe como executar o serviço de simulação de dispositivo localmente. Executar o serviço localmente torna mais fácil depurar seus dispositivos simulados antes de implementá-las para a cloud.

## <a name="next-steps"></a>Passos Seguintes

Este artigo descreveu como definir o comportamento do seu próprio modelo personalizado de dispositivo simulado. Este artigo mostrou como para:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método da solução de monitorização remota
> * Os scripts de depuração

Agora que aprendeu como especificar o comportamento de um dispositivo simulado, o passo seguinte sugerido é saber como [criar um dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

Para obter mais informações para desenvolvedores sobre a solução de monitorização remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas de Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
