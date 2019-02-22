---
title: Criar um modelo de dispositivo simulado avançadas - Azure | Documentos da Microsoft
description: Este guia de procedimentos, irá aprender a criar um modelo do dispositivo avançadas para utilização com acelerador de solução de simulação do dispositivo.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: a97afe64992db6b48558561ba958b513c8416494
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592830"
---
# <a name="create-an-advanced-device-model"></a>Criar um modelo de dispositivo avançados

Este guia de procedimentos descreve os ficheiros JSON e JavaScript que definem um modelo de dispositivo personalizado. O artigo inclui alguns ficheiros de definição de modelo de dispositivo de exemplo e mostra-lhe como carregá-los à sua instância de simulação do dispositivo. Pode criar modelos de dispositivos avançado para simular os comportamentos de dispositivo mais realistas para seus testes.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de procedimentos, terá uma instância implantada de simulação do dispositivo na sua subscrição do Azure.

Se ainda não tiver implementado a Simulação de Dispositivos, deve concluir o início rápido [Implementar e executar uma simulação de dispositivo no Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Abrir a Simulação de Dispositivo

Para executar a Simulação de Dispositivos no browser, comece por navegar para [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com) (Aceleradores de Soluções do Microsoft Azure IoT).

Poderá ser-lhe pedido para iniciar sessão com as credenciais da subscrição do Azure.

Em seguida, clique em **inicie** no mosaico da simulação de dispositivo que tenha implementado na [implementar e executar uma simulação de dispositivo do IoT no Azure](quickstart-device-simulation-deploy.md) início rápido.

## <a name="device-models"></a>Modelos de dispositivos

Cada dispositivo simulado pertence a um modelo de dispositivo específico que define o comportamento de simulação. Este comportamento inclui como frequentemente enviar telemetria, que tipo de mensagens a enviar e os métodos com suporte.

É possível definir um modelo de dispositivo usando um arquivo de definição de dispositivo JSON e um conjunto de arquivos JavaScript. Estes ficheiros JavaScript definem o comportamento de simulação, tais como a telemetria aleatória e a lógica do método.

Tem um modelo de dispositivo típico:

* Um ficheiro de JSON para cada modelo do dispositivo (por exemplo, elevator.json).
* Um ficheiro de script de comportamento de JavaScript para cada modelo do dispositivo (por exemplo, elevador-state.js)
* Um ficheiro de script de método de JavaScript para cada método de dispositivo (por exemplo, elevador-go-down.js)

> [!NOTE]
> Nem todos os modelos de dispositivos definem métodos. Portanto um modelo do dispositivo pode ou não ter scripts de método. No entanto, todos os modelos de dispositivos tem de ter um script de comportamento.

## <a name="device-definition-file"></a>Ficheiro de definição de dispositivo

Cada ficheiro de definição do dispositivo contém detalhes de um modelo de dispositivo simulado, incluindo as seguintes informações:

* Nome de modelo do dispositivo: cadeia de caracteres.
* Protocolo: AMQP | MQTT | HTTP.
* O estado inicial do dispositivo.
* A frequência de atualizar o estado do dispositivo.
* Qual arquivo JavaScript para utilizar para atualizar o estado do dispositivo.
* Uma lista de mensagens de telemetria para enviar, cada um com uma frequência específica.
* O esquema das mensagens de telemetria, utilizada pela aplicação de back-end para analisar a telemetria recebida.
* Uma lista de métodos suportados e o ficheiro de JavaScript para usar para simular a cada método.

### <a name="file-schema"></a>Esquema de ficheiro

A versão do esquema é sempre "1.0.0" e é específica para o formato deste ficheiro:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Descrição do modelo de dispositivo

As seguintes propriedades descrevem o modelo do dispositivo. Cada tipo tem um identificador exclusivo, uma versão semântica, um nome e uma descrição:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>Protocolo do IoT

Dispositivos IoT podem ligar através de protocolos diferentes. A simulação permite-lhe utilizar qualquer um **AMQP**, **MQTT**, ou **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Estado do dispositivo simulado

Cada dispositivo simulado tem um Estado interno, que tem de ser definido. O estado também define as propriedades que podem ser comunicadas na telemetria. Por exemplo, um chiller pode ter um Estado inicial, tais como:

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

Estado do dispositivo é mantido na memória pelo serviço de simulação e fornecido como entrada para a função de JavaScript. A função de JavaScript poderia decidir:

* Para ignorar o estado e gerar alguns dados aleatórios.
* Para atualizar o estado do dispositivo de alguma forma realista para um determinado cenário.

A função que gera o estado também recebe como entrada:

* O ID de dispositivo.
* O modelo do dispositivo.
* A hora atual. Este valor torna possível gerar dados de diferentes por dispositivo e por hora.

### <a name="generating-telemetry-messages"></a>A gerar mensagens de telemetria

O serviço de simulação pode enviar vários tipos de telemetria de cada dispositivo. Normalmente, a telemetria inclui dados de estado do dispositivo. Por exemplo, uma sala simulada pode enviar informações sobre a temperatura e humidade cada 10 segundos. Tenha em atenção os marcadores de posição no seguinte fragmento, que são automaticamente substituídas por valores do Estado do dispositivo:

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

Os marcadores de posição usam uma sintaxe especial **${NAME}** onde **nome** é uma chave do objeto de estado de dispositivo devolvido pelo JavaScript **principal** função. Cadeias de caracteres devem estar escritos entre aspas, enquanto os números não devem.

#### <a name="message-schema"></a>Esquema de mensagens

Cada tipo de mensagem tem de ter um esquema bem definido. O esquema de mensagem também é publicado no IoT Hub, para que as aplicações de back-end podem reutilizar as informações para interpretar a telemetria de entrada.

O esquema suporta o formato JSON, que permite fácil de análise, transformação e análise, em vários sistemas e serviços.

Os campos apresentados no esquema podem ser dos seguintes tipos:

* Objeto - serializado utilizando o JSON
* Binário - serializado utilizando o em base64
* Texto
* Booleano
* Número inteiro
* Double
* DateTime

### <a name="supported-methods"></a>Métodos suportados

Dispositivos simulados também podem reagir a chamadas de método, caso em que executar alguma lógica e fornecer alguma resposta. Da mesma forma para a simulação, a lógica do método é armazenada num arquivo JavaScript e pode interagir com o estado do dispositivo. Por exemplo:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Crie um ficheiro de definição de dispositivo

Neste procedimentos-to-guia verá como criar um modelo do dispositivo para um drone. Drone aleatoriamente será saíssem voando um conjunto inicial de alterar a localização e altitude de coordenadas.

Copie o JSON seguinte para um editor de texto e guarde-o como **drone.json**.

### <a name="device-definition-json-example"></a>Exemplo de JSON de definição de dispositivo

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

## <a name="behavior-script-files"></a>Arquivos de script de comportamento

O código no ficheiro de script de comportamento move drone. O script altera a elevação e a localização do drone manipulando no estado de memória do dispositivo.

Os arquivos JavaScript tem de ter uma **principal** função, que aceita dois parâmetros:

* R **contexto** objeto que contém três propriedades:
    * **currentTime** como uma cadeia com formato **aaaa-MM-DD ' HH:mm:sszzz**.
    * **deviceId**. Por exemplo, **Simulated.Elevator.123**.
    * **deviceModel**. Por exemplo, **elevador**.
* R **estado** objeto que é o valor devolvido pela função na chamada anterior. Este estado do dispositivo é mantido pelo serviço de simulação e utilizado para gerar mensagens de telemetria.

O **principal** função devolve o novo estado do dispositivo. Por exemplo:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Crie um ficheiro de script de comportamento

Copie o seguinte JavaScript num editor de texto e guarde-o como **drone state.js**.

### <a name="device-model-javascript-simulation-example"></a>Exemplo de simulação de JavaScript de modelo de dispositivo

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

## <a name="create-a-method-script-file"></a>Crie um ficheiro de script do método

Scripts de método são semelhantes aos scripts de comportamento. Elas definem o comportamento do dispositivo quando é chamada de uma nuvem específica para o método de dispositivo.

O script de recolhimento de drones define coordenadas do drone a um ponto fixo para simular drone retornando home page.

Copie o seguinte JavaScript num editor de texto e guarde-o como **droneRecall method.js**.

### <a name="device-model-javascript-simulation-example"></a>Exemplo de simulação de JavaScript de modelo de dispositivo

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

## <a name="debugging-script-files"></a>Arquivos de script de depuração

Embora não é possível anexar um depurador para um ficheiro de comportamento em execução, é possível escrever informações para o registo de serviço com o **log** função. Para erros de sintaxe, o interpretador de falha e escreve informações sobre a exceção no registo.

Exemplo de registo:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return state;
}
```

## <a name="deploy-an-advanced-device-model"></a>Implementar um modelo de dispositivo avançados

Para implementar o seu modelo de dispositivo avançados, que carregue os ficheiros de sua instância de simulação do dispositivo:

Selecione **Device models** (Modelos de dispositivos), na barra de menus. O **modelos de dispositivos** página lista os modelos de dispositivo disponíveis nesta instância de simulação do dispositivo:

![Modelos de dispositivos](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Clique em **+ Add Device Models** (+ Adicionar Modelos de Dispositivos), no canto superior direito da página:

![Adicionar modelo de dispositivo](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Clique em **avançadas** para abrir o separador de modelo avançado de dispositivos:

![Separador Avançadas](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Clique em **procurar** e selecione os ficheiros JSON e JavaScript que criou. Certifique-se de que selecione todos os três arquivos. Se qualquer um arquivo está em falta, a validação falhar:

![Procurar ficheiros](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Se os ficheiros passam na validação, clique em **guardar** e seu modelo de dispositivo está pronto para ser usado numa simulação. Caso contrário, corrija os erros e recarregue o os ficheiros:

![Guardar](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Passos Seguintes

Este guia de procedimentos, ficou a conhecer os arquivos de modelo de dispositivo usados na simulação de dispositivo e como criar um modelo de dispositivo avançado. Em seguida, pode querer explorar como [utilize o Time Series Insights para visualizar a telemetria enviada a partir do solution accelerator de simulação do dispositivo](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
