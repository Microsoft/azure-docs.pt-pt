---
title: Compreender os duplos digitais Plug and Play do IoT
description: Entenda como ioT Plug e Play usa gémeos digitais
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 99c957e5bf6ffe69c94e109796590f5ab975c3cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656891"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Compreender os duplos digitais Plug and Play do IoT

Um dispositivo IoT Plug and Play implementa um modelo descrito pelo esquema [digital Twins Definition Language (DTDL).](https://github.com/Azure/opendigitaltwins-dtdl) Um modelo descreve o conjunto de componentes, propriedades, comandos e mensagens de telemetria que um determinado dispositivo pode ter.

IoT Plug and Play utiliza a versão DTDL 2. Para obter mais informações sobre esta versão, consulte a [Linguagem de Definição de Gémeos Digitais (DTDL) -](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) especificação da versão 2 no GitHub.

> [!NOTE]
> A DTDL não é exclusiva da IoT Plug and Play. Outros serviços IoT, como [a Azure Digital Twins,](../digital-twins/overview.md)usam-no para representar ambientes inteiros, como edifícios e redes de energia.

Os SDKs de serviço Azure IoT incluem APIs que permitem que um serviço interaja o gémeo digital de um dispositivo. Por exemplo, um serviço pode ler as propriedades do dispositivo a partir do gémeo digital ou usar o gémeo digital para chamar um comando num dispositivo. Para saber mais, consulte [os exemplos digitais do IoT Hub twin.](concepts-developer-guide-service.md#iot-hub-digital-twin-examples)

O dispositivo IoT Plug and Play exemplo neste artigo implementa um [modelo de Controlador de Temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) que tem componentes do [termóstato.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)

## <a name="device-twins-and-digital-twins"></a>Gémeos do dispositivo e gémeos digitais

Além de um twin digital, o Azure IoT Hub também mantém um *dispositivo twin* para cada dispositivo conectado. Um duplo dispositivo é semelhante a um gémeo digital na medida em que é uma representação das propriedades de um dispositivo. Os SDKs de serviço Azure IoT incluem APIs para interagir com gémeos do dispositivo.

Um hub IoT inicializa um twin digital e um dispositivo gémeo na primeira vez que um dispositivo IoT Plug e Play se conecta.

Os gémeos do dispositivo são documentos JSON que armazenam informações do estado do dispositivo, incluindo metadados, configurações e condições. Para saber mais, consulte [exemplos de clientes de serviço ioT Hub](concepts-developer-guide-service.md#iot-hub-service-client-examples). Tanto os fabricantes de dispositivos como de soluções podem continuar a utilizar o mesmo conjunto de APIs e SDKs de dispositivos para implementar dispositivos e soluções utilizando convenções IoT Plug e Play.

As APIs digitais gémeas operam em construções DTDL de alto nível, tais como componentes, propriedades e comandos. As APIs digitais gémeas facilitam aos construtores de soluções a criação de soluções IoT Plug e Play.

Em um dispositivo gémeo, o estado de uma propriedade writable é dividido entre as *propriedades desejadas* e secções *de propriedades relatadas.* Todas as propriedades apenas de leitura estão disponíveis dentro da secção de propriedades reportadas.

Em um gémeo digital, há uma visão unificada do estado atual e desejado da propriedade. O estado de sincronização de uma determinada propriedade é armazenado na secção de componente predefinido `$metadata` correspondente.

### <a name="device-twin-json-example"></a>Exemplo JSON gémeo do dispositivo

O seguinte corte mostra um dispositivo IoT Plug e Play duplo formatado como um objeto JSON:

```json
{
  "deviceId": "sample-device",
  "modelId": "dtmi:com:example:TemperatureController;1",
  "version": 15,
  "properties": {
    "desired": {
      "thermostat1": {
        "__t": "c",
        "targetTemperature": 21.8
      },
      "$metadata": {...},
      "$version": 4
    },
    "reported": {
      "serialNumber": "alwinexlepaho8329",
      "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "__t": "c",
        "targetTemperature": {
          "value": 21.8,
          "ac": 200,
          "ad": "Successfully executed patch",
        }
      },
      "$metadata": {...},
      "$version": 11
    }
  }
}
```

### <a name="digital-twin-example"></a>Exemplo digital gémeo

O seguinte corte mostra o gémeo digital formatado como um objeto JSON:

```json
{
  "$dtId": "sample-device",
  "serialNumber": "alwinexlepaho8329",
  "thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
      "targetTemperature": {
        "desiredValue": 21.8,
        "desiredVersion": 4,
        "ackVersion": 4,
        "ackCode": 200,
        "ackDescription": "Successfully executed patch",
        "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
      },
      "maxTempSinceLastReboot": {
         "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:example:TemperatureController;1",
    "serialNumber": {
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

A tabela a seguir descreve os campos no objeto JSON gémeo digital:

| Nome do campo | Description |
| --- | --- |
| `$dtId` | Uma cadeia fornecida pelo utilizador que representa o ID do dispositivo digital twin |
| `{propertyName}` | O valor de um imóvel em JSON |
| `$metadata.$model` | [Opcional] O ID da interface de modelo que caracteriza este gémeo digital |
| `$metadata.{propertyName}.desiredValue` | [Apenas para propriedades writable] O valor desejado do imóvel especificado |
| `$metadata.{propertyName}.desiredVersion` | [Apenas para propriedades writable] A versão do valor desejado mantida pelo IoT Hub|
| `$metadata.{propertyName}.ackVersion` | [Obrigatório, apenas para propriedades writable] A versão reconhecida pelo dispositivo que implementa o gémeo digital, deve ser maior ou igual à versão desejada |
| `$metadata.{propertyName}.ackCode` | [Obrigatório, apenas para propriedades writable] O `ack` código devolvido pela aplicação do dispositivo implementando o twin digital |
| `$metadata.{propertyName}.ackDescription` | [Opcional, apenas para propriedades writable] A `ack` descrição devolvida pela aplicação do dispositivo implementando o twin digital |
| `$metadata.{propertyName}.lastUpdateTime` | O IoT Hub mantém o tempo da última atualização da propriedade pelo dispositivo. Os cartões de tempo estão em UTC e codificados no formato ISO8601 YYYYY-MM-DDTHH:MM:SS.mmmZ |
| `{componentName}` | Um objeto JSON contendo os valores de propriedade e metadados do componente. |
| `{componentName}.{propertyName}` | O valor do imóvel do componente em JSON |
| `{componentName}.$metadata` | A informação dos metadados para o componente. |

### <a name="properties"></a>Propriedades

As propriedades são campos de dados que representam o estado de uma entidade (como as propriedades em muitas linguagens de programação orientadas a objetos).

#### <a name="read-only-property"></a>Propriedade só de leitura

Esquema DTDL:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

Neste exemplo, `alwinexlepaho8329` encontra-se o valor atual da `serialNumber` propriedade apenas de leitura reportada pelo dispositivo.
Os seguintes snippets mostram a representação JSON lado a lado da `serialNumber` propriedade:

:::row:::
   :::column span="":::
      **Dispositivo duplo**

```json
"properties": {
  "reported": {
    "serialNumber": "alwinexlepaho8329"
  }
}
```

   :::column-end:::
   :::column span="":::
      **Gémeo digital**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Propriedade writable

Os exemplos a seguir mostram uma propriedade writable no componente predefinido.

DTDL:

```json
{
  "@type": "Property",
  "name": "fanSpeed",
  "displayName": "Fan Speed",
  "writable": true,
  "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Dispositivo duplo**

```json
{
  "properties": {
    "desired": {
      "fanSpeed": 2.0,
    },
    "reported": {
      "fanSpeed": {
        "value": 3.0,
        "ac": 200,
        "av": 1,
        "ad": "Successfully executed patch version 1"
      }
    }
  },
}
```

   :::column-end:::
   :::column span="":::
      **Gémeo digital**

```json
{
  "fanSpeed": 3.0,
  "$metadata": {
    "fanSpeed": {
      "desiredValue": 2.0,
      "desiredVersion": 2,
      "ackVersion": 1,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch version 1",
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

Neste exemplo, `3.0` encontra-se o valor atual do `fanSpeed` imóvel reportado pelo dispositivo. `2.0` é o valor desejado definido pela solução. O estado de valor e sincronização desejado de uma propriedade de nível de raiz é definido dentro do nível de raiz `$metadata` para um gémeo digital. Quando o dispositivo estiver online, pode aplicar esta atualização e reportar o valor atualizado.

### <a name="components"></a>Componentes

Os componentes permitem a construção da interface do modelo como um conjunto de outras interfaces.
Por exemplo, a interface [do termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) pode ser incorporada como componentes `thermostat1` e no modelo do controlador de `thermostat2` [temperatura.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)

Num duplo dispositivo, um componente é identificado pelo `{ "__t": "c"}` marcador. Num gémeo digital, a presença de `$metadata` marcas é um componente.

Neste exemplo, `thermostat1` encontra-se um componente com duas propriedades:

- `maxTempSinceLastReboot` é uma propriedade só de leitura.
- `targetTemperature` é uma propriedade que foi sincronizada com sucesso pelo dispositivo. O estado de valor e sincronização pretendido destas propriedades está no `$metadata` componente.

Os seguintes snippets mostram a representação JSON lado a lado do `thermostat1` componente:

:::row:::
   :::column span="":::
      **Dispositivo duplo**

```json
"properties": {
  "desired": {
    "thermostat1": {
      "__t": "c",
      "targetTemperature": 21.8
    },
    "$metadata": {
    },
    "$version": 4
  },
  "reported": {
    "thermostat1": {
      "maxTempSinceLastReboot": 25.3,
      "__t": "c",
      "targetTemperature": {
        "value": 21.8,
        "ac": 200,
        "ad": "Successfully executed patch",
        "av": 4
      }
    },
    "$metadata": {
    },
    "$version": 11
  }
}
```

   :::column-end:::
   :::column span="":::
      **Gémeo digital**

```json
"thermostat1": {
  "maxTempSinceLastReboot": 25.3,
  "targetTemperature": 21.8,
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 21.8,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch",
      "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
    },
    "maxTempSinceLastReboot": {
       "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>APIs gémeos digitais

As APIs digitais twin incluem **Get Digital Twin,** **Update Digital Twin,** **Invoke Component Command** e **Invoke Command** operações mais gerindo um twin digital. Pode utilizar as [APIs REST](/rest/api/iothub/service/digitaltwin) diretamente ou através de um [SDK de serviço](../iot-pnp/libraries-sdks.md).

## <a name="digital-twin-change-events"></a>Eventos de alteração de duplos digitais

Quando os eventos de alteração de duplos digitais estão ativados, é acionado um evento sempre que o valor atual ou desejado do componente ou da propriedade é alterado. Os eventos digitais de mudança de gémeos são gerados no formato [JSON Patch.](http://jsonpatch.com/) Os eventos correspondentes são gerados no formato twin do dispositivo se os eventos de mudança dupla estiverem ativados.

Para aprender a permitir o encaminhamento para eventos de dispositivos e gémeos digitais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Para compreender o formato da mensagem, consulte [Criar e ler mensagens IoT Hub](../iot-hub/iot-hub-devguide-messages-construct.md).

Por exemplo, o seguinte evento digital twin change é desencadeado quando `targetTemperature` é definido pela solução:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

O seguinte evento de mudança de gémeos digital é desencadeado quando o dispositivo informa que a alteração acima desejada foi aplicada:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> As mensagens de notificação de alterações gémeas são duplicadas quando ligadas tanto no dispositivo como na notificação digital de alteração de twin change.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre gémeos digitais, aqui estão alguns recursos adicionais:

- [Como usar IoT Plug e Play APIs digitais twin](howto-manage-digital-twin.md)
- [Interagir com um dispositivo a partir da sua solução](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Explorador de Azure IoT](howto-use-iot-explorer.md)