---
title: Telemetria, propriedade e cargas de comando em Azure IoT Central | Microsoft Docs
description: Os modelos do dispositivo Azure IoT Central permitem especificar a telemetria, propriedades e comandos de um dispositivo que deve implementar. Compreenda o formato dos dados que um dispositivo pode trocar com a IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: f027b2d41f63b5aa7ea3df87e06224abd629799b
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535319"
---
# <a name="telemetry-property-and-command-payloads"></a>Payloads de comandos, propriedades e telemetria

_Este artigo aplica-se aos desenvolvedores de dispositivos._

Um modelo de dispositivo em Azure IoT Central é uma planta que define o:

* Telemetria um dispositivo envia para a IoT Central.
* Propriedades um dispositivo sincronizado com ioT Central.
* O comando que a IoT Central chama um dispositivo.

Este artigo descreve, para os desenvolvedores de dispositivos, as cargas JSON que os dispositivos enviam e recebem para telemetria, propriedades e comandos definidos num modelo de dispositivo.

O artigo não descreve todos os tipos possíveis de telemetria, propriedade e carga de comando, mas os exemplos ilustram todos os tipos chave.

Cada exemplo mostra um corte do modelo do dispositivo que define as cargas de tipo e exemplo JSON para ilustrar como o dispositivo deve interagir com a aplicação IoT Central.

> [!NOTE]
> A IoT Central aceita qualquer JSON válido, mas só pode ser utilizado para visualizações se corresponder a uma definição no modelo do dispositivo. Pode exportar dados que não correspondam a uma definição, consulte [os dados do Export IoT para destinos em Azure.](howto-export-data.md)

O ficheiro JSON que define o modelo do dispositivo utiliza o [Digital Twin Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Para o código do dispositivo de amostra que mostra algumas destas cargas em uso, consulte a Create e ligue uma aplicação do cliente ao seu tutorial de [aplicação Azure IoT Central.](tutorial-connect-device.md)

## <a name="view-raw-data"></a>Ver dados brutos

A IoT Central permite-lhe visualizar os dados brutos que um dispositivo envia para uma aplicação. Esta visão é útil para problemas de resolução de problemas com a carga útil enviada a partir de um dispositivo. Para visualizar os dados brutos, um dispositivo está a enviar:

1. Navegue para o dispositivo a partir da página **Dispositivos.**

1. Selecione o **separador de dados Raw:**

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Vista de dados brutos":::

    Nesta vista, pode selecionar as colunas para visualizar e definir um intervalo de tempo para visualizar. A coluna **de dados não modelo** mostra dados do dispositivo que não correspondem a nenhuma definição de propriedade ou telemetria no modelo do dispositivo.

## <a name="telemetry"></a>Telemetria

### <a name="primitive-types"></a>Tipos primitivos

Esta secção mostra exemplos de tipos primitivos de telemetria que um dispositivo transmite para uma aplicação IoT Central.

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `boolean` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo:

```json
{ "BooleanTelemetry": true }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `string` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `integer` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo:

```json
{ "IntegerTelemetry": 23 }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `double` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo:

```json
{ "DoubleTelemetry": 56.78 }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `dateTime` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo - `DateTime` os tipos devem estar no formato ISO 8061:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `duration` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo - as durações devem estar no formato ISO 8601:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Tipos complexos

Esta secção mostra exemplos de tipos complexos de telemetria que um dispositivo transmite para uma aplicação IoT Central.

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `geopoint` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

> [!NOTE]
> O tipo de **esquema de geoponto** não faz parte da [especificação digital da linguagem de definição de gémeos.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) A IoT Central suporta atualmente o tipo **de esquema de geoponto** e o tipo semântico **de localização** para retrocompatibilidade.

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo. A IoT Central exibe o valor como um pino num mapa:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `Enum` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo. Os valores possíveis `0` `1` são, e `2` o display no IoT Central como `Item1` , e `Item2` `Item3` :

```json
{ "EnumTelemetry": 1 }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `Object` telemetria. Este objeto tem três campos com `dateTime` `integer` tipos, `Enum` e:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo. `DateTime` os tipos devem ser compatíveis com a NORMA ISO 8061. Os valores possíveis `Property3` `0` `1` são, e que exibem na IoT Central como `Item1` , e `Item2` `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `vector` telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

Um cliente do dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Tipos de eventos e estados

Esta secção mostra exemplos de eventos de telemetria e afirma que um dispositivo envia para uma aplicação IoT Central.

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `integer` evento:

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

Um cliente do dispositivo deve enviar os dados do evento como JSON que se parece com o seguinte exemplo:

```json
{ "IntegerEvent": 74 }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `integer` estado:

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

Um cliente de dispositivo deve enviar o estado como JSON que parece o seguinte exemplo. Os valores do estado inteiro possível `1` `2` são, ou `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Propriedades

> [!NOTE]
> Os formatos de carga útil para propriedades aplicam-se às aplicações criadas em ou após 07/14/2020.

### <a name="primitive-types"></a>Tipos primitivos

Esta secção mostra exemplos de tipos de propriedade primitiva que um dispositivo envia para uma aplicação IoT Central.

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `boolean` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin:

```json
{ "BooleanProperty": false }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `boolean` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin:

```json
{ "LongProperty": 439 }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `date` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se parece com o seguinte exemplo como uma propriedade reportada no dispositivo twin. `Date` os tipos devem ser compatíveis com a NORMA ISO 8061:

```json
{ "DateProperty": "2020-05-17" }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `duration` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin - as durações devem ser compatível com a duração ISO 8601:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `float` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin:

```json
{ "FloatProperty": 1.9 }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `string` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Tipos complexos

Esta secção mostra exemplos de tipos de propriedade complexos que um dispositivo envia para uma aplicação IoT Central.

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `geopoint` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

> [!NOTE]
> O tipo de **esquema de geoponto** não faz parte da [especificação digital da linguagem de definição de gémeos.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) A IoT Central suporta atualmente o tipo **de esquema de geoponto** e o tipo semântico **de localização** para retrocompatibilidade.

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `Enum` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se parece com o seguinte exemplo como uma propriedade reportada no dispositivo twin. Os valores possíveis `0` `1` são, e o display no IoT Central como `Item1` , e `Item2` `Item3` :

```json
{ "EnumProperty": 1 }
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `Object` propriedade. Este objeto tem dois campos com tipos `string` `integer` e:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `vector` propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Tipos de propriedades escritas

Esta secção mostra exemplos de tipos de propriedades escritas que um dispositivo recebe de uma aplicação IoT Central.

A IoT Central espera uma resposta do dispositivo para atualizações de propriedades escritas. A mensagem de resposta deve incluir os `ac` campos e `av` campos. O campo `ad` é opcional. Veja os seguintes excertos, por exemplo.

`ac` é um campo numérico que utiliza os valores na tabela seguinte:

| Valor | Etiqueta | Descrição |
| ----- | ----- | ----------- |
| `'ac': 200` | Concluído | A operação de mudança de propriedade foi concluída com sucesso. |
| `'ac': 202`  ou `'ac': 201` | Pendente | A operação de mudança de propriedade está pendente ou em curso |
| `'ac': 4xx` | Erro | A alteração de propriedade solicitada não era válida ou tinha um erro |
| `'ac': 5xx` | Erro | O dispositivo sofreu um erro inesperado ao processar a alteração solicitada. |

`av` é o número de versão enviado para o dispositivo.

`ad` é uma descrição do string de opção.

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `string` propriedade escrever:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

O dispositivo recebe a seguinte carga útil da IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

O dispositivo deve enviar a seguinte carga JSON para a IoT Central depois de processar a atualização. Esta mensagem inclui o número de versão da atualização original recebida da IoT Central. Quando a IoT Central recebe esta mensagem, marca a propriedade como **sincronizada** na UI:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de `Enum` propriedade escrever:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

O dispositivo recebe a seguinte carga útil da IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

O dispositivo deve enviar a seguinte carga JSON para a IoT Central depois de processar a atualização. Esta mensagem inclui o número de versão da atualização original recebida da IoT Central. Quando a IoT Central recebe esta mensagem, marca a propriedade como **sincronizada** na UI:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Comandos

O seguinte corte de um modelo de dispositivo mostra a definição de um comando que não tem parâmetros e que não espera que o dispositivo devolva nada:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

O dispositivo recebe uma carga útil vazia no pedido e deve devolver uma carga útil vazia na resposta com um `200` código de resposta HTTP para indicar o sucesso.

O seguinte corte de um modelo de dispositivo mostra a definição de um comando que tem um parâmetro inteiro e que espera que o dispositivo devolva um valor inteiro:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

O dispositivo recebe um valor inteiro como a carga útil do pedido. O dispositivo deve devolver um valor inteiro como a carga útil de resposta com um `200` código de resposta HTTP para indicar o sucesso.

O seguinte corte de um modelo de dispositivo mostra a definição de um comando que tem um parâmetro de objeto e que espera que o dispositivo devolva um objeto. Neste exemplo, ambos os objetos têm campos inteiros e de cordas:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

O seguinte corte mostra um exemplo de pedido de carga útil enviado para o dispositivo:

```json
{ "Field1": 56, "Field2": "A string value" }
```

O seguinte corte mostra um exemplo de carga útil de resposta enviada do dispositivo. Utilize um `200` código de resposta HTTP para indicar o sucesso:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>Comandos de longa duração

O seguinte corte de um modelo de dispositivo mostra a definição de um comando. O comando tem um parâmetro inteiro e espera que o dispositivo devolva um valor inteiro:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

O dispositivo recebe um valor inteiro como a carga útil do pedido. Se o dispositivo necessitar de tempo para processar este comando, deverá devolver uma carga de resposta vazia com um `202` código de resposta HTTP para indicar que o dispositivo aceitou o pedido de processamento.

Quando o dispositivo tiver terminado de processar o pedido, deverá enviar um imóvel para a IoT Central que se pareça com o exemplo seguinte. O nome da propriedade deve ser o mesmo que o nome de comando:

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>Comandos offline

Na UI web IoT Central, pode selecionar a opção **Queue se offline** para um comando. Os comandos offline são notificações unidirecionais para o dispositivo a partir da sua solução que são entregues assim que um dispositivo se conecta. Os comandos offline podem ter parâmetros de pedido, mas não devolvem uma resposta.

A **definição offline** não está incluída se exportar um modelo ou interface a partir do modelo do dispositivo. Não se pode dizer, olhando para um modelo ou interface JSON exportado, que um comando é um comando offline.

Os comandos offline utilizam [mensagens nuvem-dispositivo IoT Hub](../../iot-hub/iot-hub-devguide-messages-c2d.md) para enviar o comando e a carga útil para o dispositivo.

O seguinte corte de um modelo de dispositivo mostra a definição de um comando. O comando tem um parâmetro de objeto com um campo de data e uma enumeração:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 3,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

Se ativar a **fila se** a opção offline no modelo de dispositivo UI para o comando no corte anterior, então a mensagem que o dispositivo recebe inclui as seguintes propriedades:

| Nome da propriedade | Valor de exemplo |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>Passos seguintes

Como desenvolvedor de dispositivos, agora que aprendeu sobre os modelos do dispositivo, um próximo passo sugerido é ler [Se conectar ao Azure IoT Central](./concepts-get-connected.md) para saber mais sobre como registar dispositivos com a IoT Central e como a IoT Central protege as ligações do dispositivo.
