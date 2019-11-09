---
title: Dispositivo simulado na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este artigo descreve como usar o JavaScript para definir o comportamento de um dispositivo simulado na solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890854"
---
# <a name="implement-the-device-model-behavior"></a>Implementar o comportamento do modelo do dispositivo

O artigo [compreende o esquema de modelo de dispositivo](iot-accelerators-remote-monitoring-device-schema.md) descrito no esquema que define um modelo de dispositivo simulado. Esse artigo fez referência a dois tipos de arquivo JavaScript que implementam o comportamento de um dispositivo simulado:

- **Estado** Arquivos JavaScript que são executados em intervalos fixos para atualizar o estado interno do dispositivo.
- **Método** Arquivos JavaScript que são executados quando a solução invoca um método no dispositivo.

> [!NOTE]
> Os comportamentos de modelo de dispositivo são apenas para dispositivos simulados hospedados no serviço de simulação de dispositivo. Se você quiser criar um dispositivo real, consulte [conectar seu dispositivo ao acelerador de solução de monitoramento remoto](iot-accelerators-connecting-devices.md).

Neste artigo, vai aprender a:

>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método da solução de monitoramento remoto
> * Depurar seus scripts

## <a name="state-behavior"></a>Comportamento do estado

A seção de [simulação](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) do esquema de modelo de dispositivo define o estado interno de um dispositivo simulado:

- `InitialState` define os valores iniciais para todas as propriedades do objeto de estado do dispositivo.
- `Script` identifica um arquivo JavaScript que é executado em uma agenda para atualizar o estado do dispositivo.

O exemplo a seguir mostra a definição do objeto de estado do dispositivo para um dispositivo resfriador simulado:

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

O estado do dispositivo simulado, conforme definido na seção `InitialState`, é mantido na memória pelo serviço de simulação. As informações de estado são passadas como entrada para a função `main` definida em **chiller-01-State. js**. Neste exemplo, o serviço de simulação executa o arquivo **chiller-01-State. js** a cada cinco segundos. O script pode modificar o estado do dispositivo simulado.

O seguinte mostra o contorno de uma função `main` típica:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

O parâmetro `context` tem as seguintes propriedades:

- `currentTime` como uma cadeia de caracteres com o formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, por exemplo `Simulated.Chiller.123`
- `deviceModel`, por exemplo `Chiller`

O parâmetro `state` contém o estado do dispositivo como mantido pelo serviço de simulação de dispositivo. Esse valor é o objeto `state` retornado pela chamada anterior para `main`.

O exemplo a seguir mostra uma implementação típica do método `main` para manipular o estado do dispositivo mantido pelo serviço de simulação:

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

O exemplo a seguir mostra como o método `main` pode simular valores de telemetria que variam ao longo do tempo:

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

Você pode exibir o [chiller-01-State. js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) completo no github.

## <a name="method-behavior"></a>Comportamento do método

A seção [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) do esquema de modelo de dispositivo define os métodos que um dispositivo simulado responde.

O exemplo a seguir mostra a lista de métodos com suporte por um dispositivo resfriador simulado:

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

Cada método tem um arquivo JavaScript associado que implementa o comportamento do método.

O estado do dispositivo simulado, conforme definido na seção `InitialState` do esquema, é mantido na memória pelo serviço de simulação. As informações de estado são passadas como entrada para a função `main` definida no arquivo JavaScript quando o método é chamado. O script pode modificar o estado do dispositivo simulado.

O seguinte mostra o contorno de uma função `main` típica:

```javascript
function main(context, previousState, previousProperties) {

}
```

O parâmetro `context` tem as seguintes propriedades:

- `currentTime` como uma cadeia de caracteres com o formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, por exemplo `Simulated.Chiller.123`
- `deviceModel`, por exemplo `Chiller`

O parâmetro `state` contém o estado do dispositivo como mantido pelo serviço de simulação de dispositivo.

O parâmetro `properties` contém as propriedades do dispositivo que são gravadas como propriedades relatadas no dispositivo do Hub IoT.

Há três funções globais que podem ser usadas para ajudar a implementar o comportamento do método:

- `updateState` atualizar o estado mantido pelo serviço de simulação.
- `updateProperty` atualizar uma única propriedade de dispositivo.
- `sleep` pausar a execução para simular uma tarefa de execução longa.

O exemplo a seguir mostra uma versão abreviada do script **IncreasePressure-Method. js** usado pelos dispositivos resfriadores simulados:

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

## <a name="debugging-script-files"></a>Depuração de arquivos de script

Não é possível anexar um depurador ao interpretador JavaScript usado pelo serviço de simulação de dispositivo para executar os scripts de estado e método. No entanto, você pode registrar informações no log de serviço. A função interna `log()` permite que você salve informações para rastrear e depurar a execução da função.

Se houver um erro de sintaxe, o intérprete falhará e gravará uma entrada de `Jint.Runtime.JavaScriptException` no log de serviço.

O artigo [executando o serviço localmente](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) no GitHub mostra como executar o serviço de simulação de dispositivo localmente. Executar o serviço localmente torna mais fácil depurar seus dispositivos simulados antes de implantá-los na nuvem.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu como definir o comportamento do seu próprio modelo de dispositivo simulado personalizado. Este artigo mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método da solução de monitoramento remoto
> * Depurar seus scripts

Agora que você aprendeu como especificar o comportamento de um dispositivo simulado, a próxima etapa sugerida é aprender a [criar um dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

Para obter mais informações de desenvolvedor sobre a solução de monitoramento remoto, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas de Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
