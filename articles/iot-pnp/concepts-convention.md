---
title: IoT Plug and Play conventions / Microsoft Docs
description: A descrição das convenções IoT Plug and Play espera que os dispositivos utilizem quando enviam telemetria e propriedades, e manuseie comandos e atualizações de propriedade.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 86c6ea9dded423e7bd513faf73adfd293f2bd38f
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302601"
---
# <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Os dispositivos IoT Plug e Play devem seguir um conjunto de convenções quando trocam mensagens com um hub IoT. Os dispositivos IoT Plug e Play utilizam o protocolo MQTT para comunicar com o IoT Hub.

Os dispositivos podem incluir módulos , ou ser [implementados](../iot-hub/iot-hub-devguide-module-twins.md)num [módulo IoT Edge](../iot-edge/about-iot-edge.md) hospedado pelo tempo de funcionaamento IoT Edge.

Você descreve a telemetria, propriedades e comandos que um dispositivo IoT Plug and Play implementa com um modelo [digital Twins Definition Language v2 (DTDL).](https://github.com/Azure/opendigitaltwins-dtdl) _model_ Existem dois tipos de modelo referidos neste artigo:

- **Sem componente** - Um modelo sem componentes. O modelo declara telemetria, propriedades e comandos como propriedades de alto nível na secção de conteúdo da interface principal. Na ferramenta exploradora Azure IoT, este modelo aparece como um único _componente predefinido_.
- **Múltiplos componentes** - Um modelo composto por duas ou mais interfaces. Uma interface principal, que aparece como o _componente padrão,_ com telemetria, propriedades e comandos. Uma ou mais interfaces declaradas como componentes com telemetria adicional, propriedades e comandos.

Para obter mais informações, consulte [componentes IoT Plug e Play nos modelos.](concepts-components.md)

## <a name="identify-the-model"></a>Identificar o modelo

Para anunciar o modelo que implementa, um dispositivo ou módulo IoT Plug and Play inclui o ID do modelo no pacote de conexão MQTT adicionando `model-id` ao `USERNAME` campo.

Para identificar o modelo que um dispositivo ou módulo implementa, um serviço pode obter o ID do modelo a partir de:

- O `modelId` dispositivo twin field.
- O campo digital `$metadata.$model` gémeo.
- Uma notificação digital de mudança de gémeos.

## <a name="telemetry"></a>Telemetria

A telemetria enviada de um dispositivo sem componentes não requer metadados extra. O sistema adiciona a `dt-dataschema` propriedade.

A telemetria enviada a partir de um dispositivo de vários componentes deve adicionar `$.sub` como propriedade de mensagem. O sistema adiciona as `dt-subject` `dt-dataschema` propriedades e propriedades.

## <a name="read-only-properties"></a>Propriedades só de leitura

### <a name="sample-no-component-read-only-property"></a>Amostra nenhuma propriedade só de leitura de componente

Um dispositivo ou módulo pode enviar qualquer JSON válido que siga as regras DTDL v2.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Carga de propriedade reportada por amostra:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Amostra de vários componentes apenas para ler propriedade

O dispositivo ou módulo deve adicionar o `{"__t": "c"}` marcador para indicar que o elemento se refere a um componente.

DTDL que faz referência a um componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL que define o componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Carga de propriedade reportada por amostra:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Propriedades writable

O dispositivo ou módulo deve confirmar que recebeu a propriedade enviando uma propriedade reportada. A propriedade reportada deve incluir:

- `value` - o valor real do imóvel (normalmente o valor recebido, mas o dispositivo pode decidir reportar um valor diferente).
- `ac` - um código de reconhecimento que utilize um código de estado HTTP.
- `av` - uma versão de reconhecimento que se refira à `$version` propriedade desejada. Você pode encontrar este valor na carga útil json da propriedade desejada.
- `ad` - uma descrição facultativa de reconhecimento.

Quando um dispositivo é iniciado, deve solicitar o twin do dispositivo e verificar se há atualizações de propriedades. Se a versão de uma propriedade pordificável tiver aumentado enquanto o dispositivo estava offline, o dispositivo deverá enviar uma resposta de propriedade reportada para confirmar que recebeu a atualização.

Quando um dispositivo começa pela primeira vez, pode enviar um valor inicial para uma propriedade reportada se não receber uma propriedade inicial desejada a partir do centro. Neste caso, o aparelho deve ser programado `av` para `1` . Por exemplo:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Um dispositivo pode usar a propriedade reportada para fornecer outras informações ao centro. Por exemplo, o dispositivo poderia responder com uma série de mensagens em curso, tais como:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Quando o dispositivo atinge a temperatura-alvo, envia a seguinte mensagem:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Um dispositivo pode reportar um erro como:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Amostra sem propriedade de propriedade writable componente

Quando um dispositivo recebe várias propriedades relatadas numa única carga útil, pode enviar as respostas de propriedade reportadas através de várias cargas.

Um dispositivo ou módulo pode enviar qualquer JSON válido que siga as regras DTDL v2:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Carga útil da propriedade desejada:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Primeira carga útil reportada pela amostra:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Amostra reportada segunda carga útil:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Amostra de vários componentes propriedade writable

O dispositivo ou módulo deve adicionar o `{"__t": "c"}` marcador para indicar que o elemento se refere a um componente.

O marcador é enviado apenas para atualizações de propriedades definidas num componente. As atualizações para propriedades definidas no componente predefinido não incluem o marcador, ver [Amostra nenhuma propriedade de componente writable](#sample-no-component-writable-property)

Quando um dispositivo recebe várias propriedades relatadas numa única carga útil, pode enviar as respostas de propriedade reportadas através de várias cargas.

O dispositivo ou módulo deve confirmar que recebeu as propriedades enviando propriedades reportadas:

DTDL que faz referência a um componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL que define o componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Carga útil da propriedade desejada:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Primeira carga útil reportada pela amostra:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Amostra reportada segunda carga útil:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Comandos

Nenhuma interface de componente utiliza o nome de comando sem um prefixo.

Num dispositivo ou módulo, várias interfaces de componentes utilizam nomes de comando com o seguinte formato: `componentName*commandName` .

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu sobre as convenções IoT Plug and Play, aqui estão alguns recursos adicionais:

- [Linguagem de definição de gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK de Dispositivo C](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)