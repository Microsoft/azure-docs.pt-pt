---
title: Esquema do dispositivo em solução de monitorização remota - Azure / Microsoft Docs
description: Este artigo descreve o esquema JSON que define um dispositivo simulado na solução de monitorização remota.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683751"
---
# <a name="understand-the-device-model-schema"></a>Compreender o esquema do modelo de dispositivo

Pode utilizar dispositivos simulados na solução de Monitorização Remota para testar o seu comportamento. A solução de monitorização remota inclui um serviço de simulação de dispositivos para executar dispositivos simulados. Quando implementa a solução de monitorização remota, uma recolha de dispositivos simulados é aprovisionada automaticamente. Pode personalizar os dispositivos simulados existentes ou criar os seus próprios.

Este artigo descreve o esquema do modelo do dispositivo que especifica as capacidades e o comportamento de um dispositivo simulado. O modelo do dispositivo é armazenado num ficheiro JSON.

> [!NOTE]
> Este esquema de modelo de dispositivo é apenas para dispositivos simulados hospedados no serviço de simulação do dispositivo. Se pretender criar um dispositivo real, consulte [A Ligação do seu dispositivo ao acelerador de solução](iot-accelerators-connecting-devices.md)de monitorização remota .

Os seguintes artigos estão relacionados com o artigo atual:

* [Implementar o comportamento do modelo do dispositivo](iot-accelerators-remote-monitoring-device-behavior.md) descreve os ficheiros JavaScript que utiliza para implementar o comportamento de um dispositivo simulado.
* [Criar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) junta tudo e mostra-lhe como implementar um novo tipo de dispositivo simulado para a sua solução.

Neste artigo, vai aprender a:

>[!div class="checklist"]
> * Utilize um ficheiro JSON para definir um modelo de dispositivo simulado
> * Especifique as propriedades do dispositivo simulado
> * Especifique a telemetria que o dispositivo simulado envia
> * Especificar os métodos cloud-to-device a que o dispositivo responde

## <a name="the-parts-of-the-device-model-schema"></a>As partes do esquema do modelo do dispositivo

Cada modelo de dispositivo, como um refrigerador ou um caminhão, define um tipo de dispositivo que o serviço de simulação pode simular. Cada modelo de dispositivo é armazenado num ficheiro JSON com o seguinte esquema de alto nível:

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

Pode visualizar os ficheiros de esquemas para os dispositivos simulados predefinidos na pasta de [modelos de dispositivos](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) no GitHub.

A tabela seguinte descreve as entradas de esquemade alto nível:

| Entrada de Schema | Descrição |
| -- | --- |
| `SchemaVersion` | A versão schema `1.0.0` é sempre e é específica para o formato deste ficheiro. |
| `Id` | Um ID único para este modelo de dispositivo. |
| `Version` | Identifica a versão do modelo do dispositivo. |
| `Name` | Um nome amigável para o modelo do dispositivo. |
| `Description` | Uma descrição do modelo do dispositivo. |
| `Protocol` | O protocolo de ligação que o dispositivo utiliza. Pode ser `AMQP`um `MQTT`dos. `HTTP` |

As seguintes secções descrevem as outras secções do esquema JSON:

## <a name="simulation"></a>Simulação

Na `Simulation` secção, define-se o estado interno do dispositivo simulado. Quaisquer valores de telemetria enviados pelo dispositivo devem fazer parte deste estado do dispositivo.

A definição do estado do dispositivo tem dois elementos:

* `InitialState`define valores iniciais para todas as propriedades do objeto do estado do dispositivo.
* `Script`identifica um ficheiro JavaScript que funciona numa programação para atualizar o estado do dispositivo. Pode utilizar este ficheiro de script para aleatoriamente os valores de telemetria enviados pelo dispositivo.

Para saber mais sobre o ficheiro JavaScript que atualiza o objeto do estado do dispositivo, consulte [Compreender o comportamento do modelo do dispositivo](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

O serviço de simulação executa o ficheiro **chiller-01-state.js** a cada cinco segundos para atualizar o estado do dispositivo. Pode ver os ficheiros JavaScript para os dispositivos simulados predefinidos na pasta de [scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) no GitHub. Por convenção, estes ficheiros JavaScript têm o sufixo **-estado** para distingui-los dos ficheiros que implementam comportamentos de método.

## <a name="properties"></a>Propriedades

A `Properties` secção do esquema define os valores de propriedade que o dispositivo reporta à solução. Por exemplo:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Quando a solução começa, questiona todos os dispositivos `Type` simulados para construir uma lista de valores a utilizar na UI. A solução `Latitude` `Longitude` utiliza as propriedades e propriedades para adicionar a localização do dispositivo ao mapa no painel de instrumentos.

## <a name="telemetry"></a>Telemetria

A `Telemetry` matriz lista todos os tipos de telemetria que o dispositivo simulado envia para a solução.

O exemplo seguinte envia uma mensagem de telemetria `floor` `vibration`JSON a cada 10 segundos com, e `temperature` dados dos sensores do elevador:

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

`MessageTemplate`define a estrutura da mensagem JSON enviada pelo dispositivo simulado. Os espaços reservados `MessageTemplate` usam a `${NAME}` `NAME` sintaxe onde é uma chave do [objeto de estado](#simulation)do dispositivo . As cordas devem ser citadas, os números não devem.

`MessageSchema`define o esquema da mensagem enviada pelo dispositivo simulado. O esquema da mensagem também é publicado no IoT Hub para permitir que as aplicações de backend reutilizem a informação para interpretar a telemetria que está a chegar.

Atualmente, só pode usar schemas de mensagens JSON. Os campos enumerados no esquema podem ser dos seguintes tipos:

* Objeto - serializado usando JSON
* Binário - serializado usando base64
* Texto
* Booleano
* Número inteiro
* Double
* DateTime

Para enviar mensagens de telemetria em intervalos diferentes, adicione vários tipos de telemetria à `Telemetry` matriz. O exemplo seguinte envia dados de temperatura e humidade a cada 10 segundos e o estado da luz a cada minuto:

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

## <a name="cloudtodevicemethods"></a>Métodos cloudtodevice

Um dispositivo simulado pode responder a métodos cloud-to-device chamados de um hub IoT. A `CloudToDeviceMethods` secção do ficheiro esquema do modelo do dispositivo:

* Define os métodos a que o dispositivo simulado pode responder.
* Identifica o ficheiro JavaScript que contém a lógica a executar.

O dispositivo simulado envia a lista de métodos que suporta para o centro ioT a que está ligado.

Para saber mais sobre o ficheiro JavaScript que implementa o comportamento do dispositivo, consulte [Compreender o comportamento do modelo do dispositivo](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

O exemplo que se segue especifica três métodos suportados e os ficheiros JavaScript que implementam esses métodos:

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

Pode ver os ficheiros JavaScript para os dispositivos simulados predefinidos na pasta de [scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) no GitHub. Por convenção, estes ficheiros JavaScript têm o **método de** sufixo para distingui-los dos ficheiros que implementam o comportamento do Estado.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu como criar o seu próprio modelo de dispositivo simulado personalizado. Este artigo mostrou-lhe como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Utilize um ficheiro JSON para definir um modelo de dispositivo simulado
> * Especifique as propriedades do dispositivo simulado
> * Especifique a telemetria que o dispositivo simulado envia
> * Especificar os métodos cloud-to-device a que o dispositivo responde

Agora que aprendeu sobre o esquema jSON, o próximo passo sugerido é aprender a [implementar o comportamento do seu dispositivo simulado.](iot-accelerators-remote-monitoring-device-behavior.md)

Para obter mais informações sobre a solução de Monitorização Remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
