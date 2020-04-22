---
title: Criar um modelo avançado de dispositivo simulado - Azure. Microsoft Docs
description: Neste guia de como orientar, aprende-se a criar um modelo avançado de dispositivo para utilização com o acelerador de soluções de simulação de dispositivo.
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
ms.openlocfilehash: c568dddcbbf57ebd6ed5906bb83af01a84dafa41
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683836"
---
# <a name="create-an-advanced-device-model"></a>Create an advanced device model (Criar um modelo de dispositivo avançado)

Este guia descreve os ficheiros JSON e JavaScript que definem um modelo de dispositivo personalizado. O artigo inclui alguns ficheiros de definição de modelo de dispositivo de amostra e mostra-lhe como carregá-los para a sua instância de Simulação de Dispositivo. Pode criar modelos avançados de dispositivos para simular comportamentos mais realistas do dispositivo para os seus testes.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de como orientar, precisa de uma instância implementada de Simulação de Dispositivo na sua subscrição Azure.

Se ainda não tiver implementado a Simulação de Dispositivos, deve concluir o início rápido [Implementar e executar uma simulação de dispositivo no Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Abrir a Simulação de Dispositivo

Para executar a Simulação de Dispositivos no browser, comece por navegar para [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com) (Aceleradores de Soluções do Microsoft Azure IoT).

Poderá ser-lhe pedido para iniciar sessão com as credenciais da subscrição do Azure.

Em seguida, clique em **Lançamento** no azulejo para simulação de dispositivo que implementou no [Deploy e execute uma simulação de dispositivo IoT em Quickstart Azure.](quickstart-device-simulation-deploy.md)

## <a name="device-models"></a>Modelos de dispositivos

Cada dispositivo simulado pertence a um modelo específico de dispositivo que define o comportamento de simulação. Este comportamento inclui a frequência de enviar telemetria, que tipo de mensagens enviar, e os métodos suportados.

Define um modelo de dispositivo utilizando um ficheiro de definição de dispositivo JSON e um conjunto de ficheiros JavaScript. Estes ficheiros JavaScript definem o comportamento de simulação, como a telemetria aleatória e a lógica do método.

Um modelo típico de dispositivo tem:

* Um ficheiro JSON para cada modelo de dispositivo (por exemplo, elevator.json).
* Um ficheiro de script de comportamento JavaScript para cada modelo de dispositivo (por exemplo, elevator-state.js)
* Um ficheiro de script do método JavaScript para cada método do dispositivo (por exemplo, elevador-go-down.js)

> [!NOTE]
> Nem todos os modelos de dispositivos definem métodos. Portanto, um modelo de dispositivo pode ou não ter scripts de método. No entanto, todos os modelos do dispositivo devem ter um script de comportamento.

## <a name="device-definition-file"></a>Ficheiro de definição de dispositivo

Cada ficheiro de definição do dispositivo contém detalhes de um modelo de dispositivo simulado, incluindo as seguintes informações:

* Nome do modelo do dispositivo: corda.
* Protocolo: AMQP [ AMQP ] MQTT HTTP.
* O estado inicial do dispositivo.
* Quantas vezes para refrescar o estado do dispositivo.
* Que ficheiro JavaScript usar para refrescar o estado do dispositivo.
* Uma lista de mensagens de telemetria para enviar, cada uma com uma frequência específica.
* O esquema das mensagens de telemetria, usados por aplicação de back-end para analisar a telemetria recebida.
* Uma lista de métodos suportados e o ficheiro JavaScript para utilizar para simular cada método.

### <a name="file-schema"></a>Esquema de arquivo

A versão schema é sempre "1.0.0" e é específica do formato deste ficheiro:

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

Os dispositivos IoT podem ligar-se utilizando diferentes protocolos. A simulação permite-lhe utilizar **amqp,** **MQTT**ou **HTTP:**

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Estado do dispositivo simulado

Cada dispositivo simulado tem um estado interno, que deve ser definido. O Estado também define as propriedades que podem ser reportadas na telemetria. Por exemplo, um refrigerador pode ter um estado inicial como:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Um dispositivo em movimento com vários sensores pode ter mais propriedades, por exemplo:

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

O estado do dispositivo é mantido na memória pelo serviço de simulação e fornecido como entrada para a função JavaScript. A função JavaScript pode decidir:

* Ignorar o estado e gerar alguns dados aleatórios.
* Para atualizar o estado do dispositivo de alguma forma realista para um determinado cenário.

A função que gera o Estado também recebe como entrada:

* A identificação do dispositivo.
* O modelo do dispositivo.
* O tempo atual. Este valor permite gerar diferentes dados por dispositivo e pelo tempo.

### <a name="generating-telemetry-messages"></a>Gerando mensagens de telemetria

O serviço de simulação pode enviar vários tipos de telemetria para cada dispositivo. Tipicamente, a telemetria inclui dados do estado do dispositivo. Por exemplo, um quarto simulado pode enviar informações sobre temperatura e humidade a cada 10 segundos. Note os espaços reservados no seguinte corte, que são automaticamente substituídos por valores do estado do dispositivo:

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

Os espaços reservados usam uma sintaxe especial **${NAME}** onde o **NAME** é uma chave do objeto de estado do dispositivo devolvido pela função **principal** JavaScript. As cordas devem ser citadas, enquanto os números não devem.

#### <a name="message-schema"></a>Esquema de mensagem

Cada tipo de mensagem deve ter um esquema bem definido. O esquema da mensagem também é publicado no IoT Hub, para que as aplicações back-end possam reutilizar a informação para interpretar a telemetria que está a chegar.

O esquema suporta o formato JSON, que permite facilitar a análise, transformação e análise, em vários sistemas e serviços.

Os campos enumerados no esquema podem ser dos seguintes tipos:

* Objeto - serializado usando JSON
* Binário - serializado usando base64
* Texto
* Booleano
* Número inteiro
* Double
* DateTime

### <a name="supported-methods"></a>Métodos suportados

Os dispositivos simulados também podem reagir a chamadas de métodos, caso em que executam alguma lógica e fornecem alguma resposta. Da mesma forma que a simulação, a lógica do método é armazenada num ficheiro JavaScript, e pode interagir com o estado do dispositivo. Por exemplo:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Criar um ficheiro de definição de dispositivo

Neste como-guia você vê como criar um modelo de dispositivo para um drone. O drone voará aleatoriamente em torno de um conjunto inicial de coordenadas que mudam de local e altitude.

Copie o seguinte JSON num editor de texto e guarde-o como **drone.json**.

### <a name="device-definition-json-example"></a>Exemplo JSON definição de dispositivo

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

O código no ficheiro do guião de comportamento move o drone. O guião altera a elevação e localização do drone manipulando o dispositivo em estado de memória.

Os ficheiros JavaScript devem ter uma função **principal,** que aceita dois parâmetros:

* Um objeto de **contexto** que contém três propriedades:
    * **currentTime** como uma cadeia com formato **yyyy-MM-dd'T'HH:mm:sszzz**.
    * **dispositivoId**. Por exemplo, **Simulado.Elevador.123**.
    * **dispositivoModel**. Por exemplo, **Elevador.**
* Um objeto **de Estado** que é o valor devolvido pela função na chamada anterior. Este estado de dispositivo é mantido pelo serviço de simulação, e usado para gerar mensagens de telemetria.

A função **principal** devolve o estado do novo dispositivo. Por exemplo:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Criar um ficheiro de script de comportamento

Copie o seguinte JavaScript num editor de texto e guarde-o como **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Exemplo de simulação javaScript modelo do dispositivo

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

Os scripts de método são semelhantes aos scripts de comportamento. Definem o comportamento do dispositivo quando uma nuvem específica para o método do dispositivo é chamada.

O guião de recordação do drone define as coordenadas do drone para um ponto fixo para simular o regresso do drone a casa.

Copie o seguinte JavaScript num editor de texto e guarde-o como **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Exemplo de simulação javaScript modelo do dispositivo

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

## <a name="debugging-script-files"></a>Depurando ficheiros de script

Embora não possa anexar um debugger a um ficheiro de comportamento em execução, é possível escrever informações ao registo de serviço utilizando a função de **registo.** Para erros de sintaxe, o intérprete falha e escreve informações sobre a exceção ao registo.

Exemplo de exploração:

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

Para implementar o seu modelo avançado de dispositivo, faça o upload dos ficheiros da sua instância de Simulação de Dispositivos:

Selecione **Device models** (Modelos de dispositivos), na barra de menus. A página de **modelos do Dispositivo** lista os modelos do dispositivo disponíveis neste caso de Simulação de Dispositivos:

![Modelos de dispositivos](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Clique em **+ Add Device Models** (+ Adicionar Modelos de Dispositivos), no canto superior direito da página:

![Adicionar modelo de dispositivo](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Clique em **Advanced** para abrir o separador de modelo avançado do dispositivo:

![Separador Avançado](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Clique em **Navegar** e selecione os ficheiros JSON e JavaScript que criou. Certifique-se de selecionar os três ficheiros. Se faltar algum ficheiro, a validação falha:

![Procurar ficheiros](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Se os seus ficheiros passarem na validação, clique em **Guardar** e o seu modelo de dispositivo está pronto para ser utilizado numa simulação. Caso contrário, corrija quaisquer erros e recarregue os ficheiros:

![Guardar](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia de como orientar, aprendeu sobre os ficheiros do modelo do dispositivo utilizados na Simulação do Dispositivo e como criar um modelo avançado de dispositivo. Em seguida, poderá querer explorar como utilizar insights da Série Time para visualizar a [telemetria enviada do acelerador de solução de simulação de dispositivos](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
