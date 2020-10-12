---
title: Compreender os duplos digitais Plug and Play do IoT
description: Entenda como ioT Plug e Play usa gémeos digitais
author: prashmo
ms.author: prashmo
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5d5ffe4e7d92530f18e278382ab3637c3326e57c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578058"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Compreender os duplos digitais Plug and Play do IoT

Um dispositivo IoT Plug and Play implementa um modelo descrito pelo esquema [digital Twins Definition Language (DTDL).](https://github.com/Azure/opendigitaltwins-dtdl) Um modelo descreve o conjunto de componentes, propriedades, comandos e mensagens de telemetria que um determinado dispositivo pode ter. Um twin de dispositivo e um gémeo digital são inicializados da primeira vez que um dispositivo IoT Plug e Play se conecta a um hub IoT.

IoT Plug and Play utiliza a versão DTDL 2. Para obter mais informações sobre esta versão, consulte a [Linguagem de Definição de Gémeos Digitais (DTDL) -](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) especificação da versão 2 no GitHub.

A DTDL não é exclusiva da IoT Plug and Play. Outros serviços IoT, como [a Azure Digital Twins,](../digital-twins/overview.md)usam-no para representar ambientes inteiros, como edifícios e redes de energia. Para saber mais, consulte [Os modelos gémeos em Azure Digital Twins.](../digital-twins/concepts-models.md)

Este artigo descreve como os componentes e propriedades estão representados nas secções *desejadas* e *reportadas* de um dispositivo gémeo. Também descreve como estes conceitos são mapeados para o duplo digital correspondente.

O dispositivo de ficha e reprodução IoT neste artigo que implementa [o modelo do Controlador de Temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) com componente do [termóstato.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)

## <a name="device-twins-and-digital-twins"></a>Gémeos do dispositivo e gémeos digitais

Os gémeos do dispositivo são documentos JSON que armazenam informações do estado do dispositivo, incluindo metadados, configurações e condições. Para saber mais, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub.](../iot-hub/iot-hub-devguide-device-twins.md) Tanto os fabricantes de dispositivos como de soluções podem continuar a utilizar o mesmo conjunto de APIs e SDKs de dispositivos para implementar dispositivos e soluções utilizando convenções IoT Plug e Play.

As APIs Digitais Twin operam em construções de alto nível na Linguagem de Definição de Gémeos Digitais (DTDL), tais como componentes, propriedades e comandos. As APIs Digitais Twin facilitam aos construtores de soluções a criação de soluções IoT Plug e Play.

Em um dispositivo gémeo, o estado de uma propriedade writable é dividido através das secções desejadas e relatadas. Todas as propriedades apenas de leitura estão disponíveis dentro da secção relatada.

Em um gémeo digital, há uma visão unificada do estado atual e desejado da propriedade. O estado de sincronização de uma determinada propriedade é armazenado na secção de componente predefinido `$metadata` correspondente.

### <a name="digital-twin-json-format"></a>Formato JSON twin digital

Quando representado como um objeto JSON, um gémeo digital inclui os seguintes campos:

| Nome do campo | Descrição |
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

#### <a name="device-twin-sample"></a>Amostra de twin dispositivo

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

#### <a name="digital-twin-sample"></a>Amostra digital twin

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

### <a name="properties"></a>Propriedades

As propriedades são campos de dados que representam o estado de uma entidade (como as propriedades em muitas linguagens de programação orientadas a objetos).

#### <a name="read-only-property"></a>Propriedade só de leitura

Schema:

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

Digamos que o dispositivo também tinha a seguinte propriedade writable no componente padrão:

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
Considere a interface [do termóstato,](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) que é definida como um modelo.
Esta interface pode agora ser incorporada como um termóstato de componentes1 (e outro termóstato de componentes2) ao definir o [modelo do Controlador de Temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

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

A Azure Digital Twins vem equipado com **Get Digital Twin,** **Update Digital Twin,** **Invoke Component Command** e **Invoke Command** para gerir o dispositivo digital twin. Pode utilizar as [APIs REST](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) diretamente ou através de um [SDK de serviço](../iot-pnp/libraries-sdks.md).

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
- [Interagir com um dispositivo a partir da sua solução](quickstart-service-node.md)
- [IoT Digital Twin REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Explorador de Azure IoT](howto-use-iot-explorer.md)
