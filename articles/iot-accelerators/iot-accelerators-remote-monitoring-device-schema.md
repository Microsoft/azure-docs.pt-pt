---
title: Esquema de dispositivo na solução de monitorização remota - Azure | Documentos da Microsoft
description: Este artigo descreve o esquema JSON que define um dispositivo simulado na solução de monitorização remota.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 0f9669d491648ecc621aab27d0908dcc3dc84438
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823334"
---
# <a name="understand-the-device-model-schema"></a>Compreender o esquema do modelo de dispositivo

Pode utilizar dispositivos simulados na solução de monitorização remota para testar o seu comportamento. A solução de monitorização remota inclui um serviço de simulação de dispositivo para executar os dispositivos simulados. Ao implementar a solução de monitorização remota, uma coleção de dispositivos simulados é aprovisionada automaticamente. Pode personalizar os dispositivos simulados existentes ou criar os seus próprios.

Este artigo descreve o esquema do modelo de dispositivo Especifica as capacidades e comportamento de um dispositivo simulado. O modelo do dispositivo é armazenado num ficheiro JSON.

> [!NOTE]
> Este esquema de modelo do dispositivo é apenas para dispositivos simulados alojados no serviço de simulação do dispositivo. Se quiser criar um dispositivo real, veja [ligar o seu dispositivo para o acelerador de solução de monitorização remota](iot-accelerators-connecting-devices.md).

Os artigos seguintes estão relacionados ao artigo atual:

* [Implementar o comportamento de modelo do dispositivo](iot-accelerators-remote-monitoring-device-behavior.md) descreve os ficheiros de JavaScript que utilizar para implementar o comportamento de um dispositivo simulado.
* [Criar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) coloca tudo isso e mostra-lhe como implementar um novo tipo de dispositivo simulado à sua solução.

Neste artigo, vai aprender a:

>[!div class="checklist"]
> * Utilizar um ficheiro JSON para definir um modelo de dispositivo simulado
> * Especifique as propriedades do dispositivo simulado
> * Especifique a telemetria que do dispositivo simulado envia
> * Especifique os métodos de cloud para o dispositivo, que o dispositivo responde a

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

Para saber mais sobre o ficheiro de JavaScript que atualiza o objeto de estado do dispositivo, veja [compreender o comportamento de modelo do dispositivo](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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
* Integer
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

Para saber mais sobre o ficheiro de JavaScript que implementa o comportamento do dispositivo, veja [compreender o comportamento de modelo do dispositivo](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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

## <a name="next-steps"></a>Passos Seguintes

Este artigo descreveu como criar seu próprio modelo personalizado de dispositivo simulado. Este artigo mostrou como para:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Utilizar um ficheiro JSON para definir um modelo de dispositivo simulado
> * Especifique as propriedades do dispositivo simulado
> * Especifique a telemetria que do dispositivo simulado envia
> * Especifique os métodos de cloud para o dispositivo, que o dispositivo responde a

Agora que aprendeu sobre o esquema JSON, o passo seguinte sugerido é saber como [implementar o comportamento do seu dispositivo simulado](iot-accelerators-remote-monitoring-device-behavior.md).

Para obter mais informações para desenvolvedores sobre a solução de monitorização remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas de Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
