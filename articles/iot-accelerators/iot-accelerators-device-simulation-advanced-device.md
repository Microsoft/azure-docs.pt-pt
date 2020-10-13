---
title: Criar um modelo avançado de dispositivo simulado - Azure/ Microsoft Docs
description: Neste guia de como fazer, aprende-se a criar um modelo avançado de dispositivo para utilização com o acelerador de solução de simulação de dispositivo.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 101bd9ce351ea830688fc18affdf21ce527ba43a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261435"
---
# <a name="create-an-advanced-device-model"></a>Create an advanced device model (Criar um modelo de dispositivo avançado)

Este guia de como fazer descreve os ficheiros JSON e JavaScript que definem um modelo de dispositivo personalizado. O artigo inclui alguns ficheiros de definição de modelo de dispositivo de amostra e mostra-lhe como carregá-los para a sua instância de Simulação de Dispositivo. Pode criar modelos avançados de dispositivos para simular comportamentos de dispositivo mais realistas para os seus testes.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de como fazer, precisa de uma instância implementada de Simulação de Dispositivo na sua subscrição Azure.

Se ainda não tiver implementado a Simulação de Dispositivos, deve concluir o início rápido [Implementar e executar uma simulação de dispositivo no Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Abrir a Simulação de Dispositivo

Para executar a Simulação de Dispositivos no browser, comece por navegar para [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com) (Aceleradores de Soluções do Microsoft Azure IoT).

Poderá ser-lhe pedido para iniciar sessão com as credenciais da subscrição do Azure.

Em seguida, clique em **Lançar** no azulejo para simulação de dispositivo que implementou na [Implementação e executar uma simulação de dispositivo IoT no](quickstart-device-simulation-deploy.md) arranque rápido do Azure.

## <a name="device-models"></a>Modelos de dispositivos

Cada dispositivo simulado pertence a um modelo de dispositivo específico que define o comportamento de simulação. Este comportamento inclui a frequência com que enviar telemetria, que tipo de mensagens enviar e os métodos suportados.

Define um modelo de dispositivo utilizando um ficheiro de definição de dispositivo JSON e um conjunto de ficheiros JavaScript. Estes ficheiros JavaScript definem o comportamento de simulação, como a telemetria aleatória e a lógica do método.

Um modelo de dispositivo típico tem:

* Um ficheiro JSON para cada modelo de dispositivo (por exemplo, elevator.jsligado).
* Um ficheiro de script de comportamento JavaScript para cada modelo de dispositivo (por exemplo, elevator-state.js)
* Um ficheiro de script de método JavaScript para cada método do dispositivo (por exemplo, elevator-go-down.js)

> [!NOTE]
> Nem todos os modelos de dispositivos definem métodos. Portanto, um modelo de dispositivo pode ou não ter scripts de método. No entanto, todos os modelos de dispositivos devem ter um script de comportamento.

## <a name="device-definition-file"></a>Ficheiro de definição de dispositivo

Cada ficheiro de definição de dispositivo contém detalhes de um modelo de dispositivo simulado, incluindo as seguintes informações:

* Nome do modelo do dispositivo: string.
* Protocolo: AMQP / MQTT ! HTTP.
* O estado do dispositivo inicial.
* Quantas vezes é para refrescar o estado do dispositivo.
* Que ficheiro JavaScript utilizar para refrescar o estado do dispositivo.
* Uma lista de mensagens de telemetria para enviar, cada uma com uma frequência específica.
* O esquema das mensagens de telemetria, utilizado por aplicação de back-end para analisar a telemetria recebida.
* Uma lista de métodos suportados e o ficheiro JavaScript para usar para simular cada método.

### <a name="file-schema"></a>Esquema de arquivo

A versão do esquema é sempre "1.0.0" e é específica para o formato deste ficheiro:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Descrição do modelo do dispositivo

As seguintes propriedades descrevem o modelo do dispositivo. Cada tipo tem um identificador único, uma versão semântica, um nome e uma descrição:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>Protocolo IoT

Os dispositivos IoT podem ligar-se usando diferentes protocolos. A simulação permite-lhe utilizar **amqp,** **MQTT,** ou **HTTP:**

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Estado do dispositivo simulado

Cada dispositivo simulado tem um estado interno, que deve ser definido. O Estado também define as propriedades que podem ser reportadas em telemetria. Por exemplo, um refrigerador pode ter um estado inicial como:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Um dispositivo móvel com vários sensores pode ter mais propriedades, por exemplo:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

O estado do dispositivo é mantido na memória pelo serviço de simulação e fornecido como entrada para a função JavaScript. A função JavaScript poderia decidir:

* Ignorar o estado e gerar alguns dados aleatórios.
* Atualizar o estado do dispositivo de uma forma realista para um determinado cenário.

A função que gera o estado também recebe como entrada:

* A identificação do dispositivo.
* O modelo do dispositivo.
* A hora atual. Este valor permite gerar diferentes dados por dispositivo e pelo tempo.

### <a name="generating-telemetry-messages"></a>Gerando mensagens de telemetria

O serviço de simulação pode enviar vários tipos de telemetria para cada dispositivo. Normalmente, a telemetria inclui dados do estado do dispositivo. Por exemplo, uma sala simulada pode enviar informações sobre temperatura e humidade a cada 10 segundos. Note os espaços reservados no seguinte corte, que são automaticamente substituídos por valores do estado do dispositivo:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Os espaços reservados utilizam uma sintaxe especial **${NAME}** onde **NAME** é uma chave do objeto de estado do dispositivo devolvido pela função **principal** JavaScript. As cordas devem ser citadas, enquanto os números não.

#### <a name="message-schema"></a>Esquema de mensagem

Cada tipo de mensagem deve ter um esquema bem definido. O esquema de mensagem também é publicado no IoT Hub, para que as aplicações de back-end possam reutilizar a informação para interpretar a telemetria de entrada.

O esquema suporta o formato JSON, que permite fácil análise, transformação e análise, através de vários sistemas e serviços.

Os campos listados no esquema podem ser dos seguintes tipos:

* Objeto - serializado usando JSON
* Binário - serializado usando base64
* Texto
* Booleano
* Número inteiro
* Double (Duplo)
* DateTime

### <a name="supported-methods"></a>Métodos apoiados

Os dispositivos simulados também podem reagir a chamadas de método, caso em que executam alguma lógica e fornecem alguma resposta. Da mesma forma que a simulação, a lógica do método é armazenada num ficheiro JavaScript e pode interagir com o estado do dispositivo. Por exemplo:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Criar um ficheiro de definição de dispositivo

Neste como guiar vê-se como criar um modelo de dispositivo para um drone. O drone voará aleatoriamente em torno de um conjunto inicial de coordenadas mudando de local e altitude.

Copie o JSON seguinte para um editor de texto e guarde-o como **drone.js.**

### <a name="device-definition-json-example"></a>Definição de dispositivo JSON exemplo

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Ficheiros de script de comportamento

O código no ficheiro do script de comportamento move o drone. O script altera a elevação e localização do drone manipulando o estado de memória do dispositivo.

Os ficheiros JavaScript devem ter uma função **principal,** que aceita dois parâmetros:

* Um objeto **de contexto** que contém três propriedades:
    * **currentTime** como uma cadeia com formato **yyyy-MM-dd'T'HH:mm:sszzz**.
    * **deviceId**. Por exemplo, **Simulado.Elevador.123**.
    * **modelo de dispositivo**. Por exemplo, **Elevador.**
* Um objeto **de estado** que é o valor devolvido pela função na chamada anterior. Este estado do dispositivo é mantido pelo serviço de simulação e utilizado para gerar mensagens de telemetria.

A função **principal** devolve o estado do novo dispositivo. Por exemplo:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Crie um ficheiro de script de comportamento

Copie o JavaScript seguinte para um editor de texto e guarde-o como **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Modelo de dispositivo JavaScript exemplo de simulação

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
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
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
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
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Criar um ficheiro de script de método

Os scripts de métodos são semelhantes aos scripts de comportamento. Definem o comportamento do dispositivo quando uma nuvem específica para o método do dispositivo é chamada.

O guião de recordação do drone define as coordenadas do drone num ponto fixo para simular o regresso do drone a casa.

Copie o JavaScript seguinte para um editor de texto e guarde-o como **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Modelo de dispositivo JavaScript exemplo de simulação

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
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
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Depurando ficheiros de scripts

Embora não possa anexar um depurar a um ficheiro de comportamento em execução, é possível escrever informações no registo de serviço utilizando a função **de registo.** Para erros de sintaxe, o intérprete falha e escreve informações sobre a exceção ao registo.

Exemplo de registo:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Implementar um modelo avançado de dispositivo

Para implementar o modelo avançado do dispositivo, faça o upload dos ficheiros da sua instância de Simulação do Dispositivo:

Selecione **Device models** (Modelos de dispositivos), na barra de menus. A página **dos modelos do Dispositivo** lista os modelos do dispositivo disponíveis neste caso de Simulação do Dispositivo:

![Modelos de dispositivos](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Clique em **+ Add Device Models** (+ Adicionar Modelos de Dispositivos), no canto superior direito da página:

![Adicionar modelo de dispositivo](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Clique **em Avançado** para abrir o separador modelo avançado do dispositivo:

![Separador Avançado](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Clique em Procurar e selecione os **ficheiros** JSON e JavaScript que criou. Certifique-se de selecionar os três ficheiros. Se faltar algum ficheiro, a validação falha:

![Procurar ficheiros](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Se os seus ficheiros passarem na validação, clique em **Guardar** e o modelo do dispositivo está pronto para ser utilizado numa simulação. Caso contrário, corrija quaisquer erros e recarregue os ficheiros:

![Guardar](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia de como fazer, aprendeu sobre os ficheiros de modelos do dispositivo utilizados na Simulação do Dispositivo e como criar um modelo avançado de dispositivo. Em seguida, poderá querer explorar como [utilizar insights de séries de tempo para visualizar a telemetria enviada do acelerador de solução de simulação do dispositivo](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
