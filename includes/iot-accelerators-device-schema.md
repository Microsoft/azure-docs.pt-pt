---
title: incluir ficheiro
description: incluir ficheiro
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 414bb0183e68cb46e52c379ea3f7aceda5d4170e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446140"
---
## <a name="the-parts-of-the-device-model-schema"></a>As partes do esquema do modelo de dispositivo

Cada modelo do dispositivo, como um chiller ou camião, define um tipo de dispositivo que pode simular o serviço de simulação. Cada modelo do dispositivo é armazenado num ficheiro JSON com o esquema de nível superior seguinte:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

Pode ver os arquivos de esquema para os dispositivos simulado de predefinição na [devicemodels pasta](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) no GitHub.

A tabela seguinte descreve as entradas do esquema de nível superior:

| Entrada de esquema | Descrição |
| -- | --- |
| `SchemaVersion` | A versão do esquema é sempre `1.0.0` e é específico para o formato deste ficheiro. |
| `Id` | Um ID exclusivo para este modelo do dispositivo. |
| `Version` | Identifica a versão do modelo de dispositivo. |
| `Name` | Um nome amigável para o modelo do dispositivo. |
| `Description` | Uma descrição do modelo de dispositivo. |
| `Protocol` | Utiliza o protocolo de ligação do dispositivo. Pode ser um dos `AMQP`, `MQTT`, e `HTTP`. |

As secções seguintes descrevem as outras secções no esquema JSON:

## <a name="simulation"></a>Simulação

Na `Simulation` secção, vai definir o estado interno do dispositivo simulado. Quaisquer valores de telemetria enviadas pelo dispositivo tem de ser parte esse Estado do dispositivo.

A definição do Estado do dispositivo tem dois elementos:

* `InitialState` Define os valores iniciais para todas as propriedades do objeto de estado do dispositivo.
* `Script` identifica um arquivo JavaScript que é executada numa agenda, para atualizar o estado do dispositivo. Pode utilizar este ficheiro de script para tornar os valores de telemetria enviados pelo dispositivo.

Para saber mais sobre o ficheiro de JavaScript que atualiza o objeto de estado do dispositivo, veja [compreender o comportamento de modelo do dispositivo](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

O serviço de simulação executa o **chiller-01-state.js** ficheiro a cada cinco segundos para atualizar o estado do dispositivo. Pode ver os arquivos de JavaScript para os dispositivos simulado de predefinição na [pasta de scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) no GitHub. Por convenção, esses arquivos de JavaScript têm o sufixo **-estado** para distingui-los a partir dos ficheiros que implementam os comportamentos de método.

## <a name="properties"></a>Propriedades

O `Properties` seção do esquema define os valores de propriedade, o dispositivo comunica à solução. Por exemplo:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Quando a solução é iniciado, ele consulta todos os dispositivos simulados para criar uma lista de `Type` valores a utilizar na interface do Usuário. A solução utiliza o `Latitude` e `Longitude` propriedades para adicionar a localização do dispositivo para o mapa no dashboard.

## <a name="telemetry"></a>Telemetria

O `Telemetry` matriz apresenta uma lista de todos os tipos de telemetria que o dispositivo simulado envia para a solução.

O exemplo seguinte, envia uma mensagem de telemetria JSON com a cada 10 segundos `floor`, `vibration`, e `temperature` dados a partir de sensores do elevador:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` Define a estrutura da mensagem JSON enviada pelo dispositivo simulado. Os marcadores de posição `MessageTemplate` utilize a sintaxe `${NAME}` onde `NAME` é uma chave da [objeto de estado do dispositivo](#simulation). Cadeias de caracteres devem estar escritos entre aspas, não devem números.

`MessageSchema` Define o esquema da mensagem enviada pelo dispositivo simulado. O esquema de mensagem também é publicado no IoT Hub para permitir que as aplicações de back-end reutilizar as informações para interpretar a telemetria de entrada.

Atualmente, só pode utilizar esquemas de mensagem JSON. Os campos apresentados no esquema podem ser dos seguintes tipos:

* Objeto - serializado utilizando o JSON
* Binário - serializado utilizando o em base64
* Text
* Boolean
* Número inteiro
* Double
* DateTime

Para enviar mensagens de telemetria em intervalos diferentes, adicionar vários tipos de telemetria para o `Telemetry` matriz. O exemplo seguinte envia dados de temperatura e humidade cada 10 segundos e o estado de luz de cada minuto:

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
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Um dispositivo simulado pode responder a métodos de cloud-para-dispositivo chamados a partir de um hub IoT. O `CloudToDeviceMethods` seção no arquivo de esquema do modelo do dispositivo:

* Define os métodos que o dispositivo simulado pode responder.
* Identifica o ficheiro de JavaScript que contém a lógica para executar.

O dispositivo simulado envia a lista de métodos que ele oferece suporte para o hub IoT que está ligado a.

Para saber mais sobre o ficheiro de JavaScript que implementa o comportamento do dispositivo, veja [compreender o comportamento de modelo do dispositivo](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

O exemplo seguinte especifica três métodos suportados e os ficheiros de JavaScript que implementam os métodos:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
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

Pode ver os arquivos de JavaScript para os dispositivos simulado de predefinição na [pasta de scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) no GitHub. Por convenção, esses arquivos de JavaScript têm o sufixo **-método** para distingui-los a partir dos ficheiros que implementam o comportamento do Estado.