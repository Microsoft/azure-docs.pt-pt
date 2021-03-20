---
title: Compreenda os modelos do dispositivo IoT Plug e Play | Microsoft Docs
description: Compreenda a linguagem de modelação digital Twins Definition Language (DTDL) para dispositivos IoT Plug and Play. O artigo descreve tipos de dados primitivos e complexos, padrões de reutilização que utilizam componentes e heranças, e tipos semânticos. O artigo fornece orientações sobre a escolha do identificador de modelos duplos do dispositivo e suporte de ferramentas para a autoria do modelo.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a3389408b0942875aa7d760f1c514b995e381f9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609476"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Guia de modelagem IoT Plug and Play

No núcleo do IoT Plug and Play, está um _modelo_ de dispositivo que descreve as capacidades de um dispositivo para uma aplicação IoT Plug e Play. Este modelo é estruturado como um conjunto de interfaces que definem:

- _Propriedades_ que representam o estado apenas de leitura ou de um dispositivo ou de outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade apenas de leitura e uma temperatura-alvo num termóstato pode ser uma propriedade writável.
- _Campos de telemetria_ que definem os dados emitidos por um dispositivo, quer os dados sejam um fluxo regular de leituras de sensores, um erro ocasional ou uma mensagem de informação.
- _Comandos_ que descrevem uma função ou operação que pode ser feita num dispositivo. Por exemplo, um comando poderia reiniciar um portal ou tirar uma fotografia usando uma câmara remota.

Para saber mais sobre como o IoT Plug and Play utiliza modelos de dispositivos, consulte o [guia de desenvolvimento de dispositivos IoT Plug e Play](concepts-developer-guide-device.md) e o guia de desenvolvimento de [serviços IoT Plug and Play](concepts-developer-guide-service.md).

Para definir um modelo, utilize a Linguagem de Definição de Gémeos Digitais (DTDL). A DTDL utiliza uma variante JSON chamada [JSON-LD](https://json-ld.org/). O seguinte corte mostra o modelo para um dispositivo termóstato que:

- Tem um ID de modelo único: `dtmi:com:example:Thermostat;1` .
- Envia telemetria de temperatura.
- Tem uma propriedade para definir a temperatura alvo.
- Tem uma propriedade só de leitura para reportar a temperatura máxima desde o último reboot.
- Responde a um comando que solicita temperaturas máximas, mínimas e médias ao longo de um período de tempo.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

O modelo do termóstato tem uma única interface. Exemplos posteriores neste artigo mostram modelos mais complexos que usam componentes e heranças.

Este artigo descreve como conceber e autorar os seus próprios modelos e abrange temas como tipos de dados, estrutura de modelos e ferramentas.

Para saber mais, consulte a [especificação de Definição de Gémeos Digitais v2.](https://github.com/Azure/opendigitaltwins-dtdl)

## <a name="model-structure"></a>Estrutura do modelo

Propriedades, telemetria e comandos são agrupados em interfaces. Esta secção descreve como pode utilizar interfaces para descrever modelos simples e complexos utilizando componentes e heranças.

### <a name="model-ids"></a>IDs de modelo

Cada interface tem um identificador de modelos duplos digitais único (DTMI). Os modelos complexos utilizam DTMIs para identificar componentes. As aplicações podem utilizar os DTMIs que os dispositivos enviam para localizar definições de modelo num repositório.

As DTMIs devem seguir a convenção de nomeação exigida pelo [repositório do modelo IoT Plug and Play:](https://github.com/Azure/iot-plugandplay-models)

- O prefixo DTMI é `dtmi:` .
- O sufixo DTMI é o número de versão do modelo como `;2` .
- O corpo do DTMI mapeia para a pasta e arquiva no repositório do modelo onde o modelo está armazenado. O número da versão faz parte do nome do ficheiro.

Por exemplo, o modelo identificado pelo DTMI `dtmi:com:Example:Thermostat;2` é armazenado no *dtmi/com/exemplo/thermostat-2.jsem* ficheiro.

O seguinte corte mostra o esboço de uma definição de interface com o seu DTMI único:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Sem componentes

Um modelo simples, como o termóstato mostrado anteriormente, não utiliza componentes embutidos ou em cascata. A telemetria, propriedades e comandos são definidos no `contents` nó da interface.

O exemplo a seguir mostra parte de um modelo simples que não utiliza componentes:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Ferramentas como o Azure IoT Explorer e o designer de modelos de dispositivos IoT Central rotulam uma interface autónoma como o termóstato como um _componente predefinido_.

A imagem que se segue mostra como o modelo aparece na ferramenta exploradora Azure IoT:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Componente padrão no explorador Azure IoT":::

A imagem que se segue mostra como o modelo apresenta como o componente predefinido no modelo do dispositivo IoT Central. Selecione **Ver identidade** para ver o DTMI do modelo:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Screenshot mostrando o modelo do termóstato no designer IoT Central":::

O ID do modelo é armazenado numa propriedade gémea do dispositivo, como mostra a seguinte imagem:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="ID modelo em propriedade digital twin":::

Um modelo DTDL sem componentes é uma simplificação útil para um dispositivo ou um módulo IoT Edge com um único conjunto de telemetria, propriedades e comandos. Um modelo que não utilize componentes facilita a migração de um dispositivo ou módulo existente para ser um dispositivo ou módulo IoT Plug and Play - cria um modelo DTDL que descreve o seu dispositivo ou módulo real sem a necessidade de definir quaisquer componentes.

> [!TIP]
> Um módulo pode ser um [módulo](../iot-hub/iot-hub-devguide-module-twins.md) de dispositivo ou um [módulo IoT Edge](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Reutilizar

Há duas formas de reutilizar as definições de interface. Utilize vários componentes num modelo para fazer referência a outras definições de interface. Utilize a herança para alargar as definições de interface existentes.

### <a name="multiple-components"></a>Múltiplos componentes

Os componentes permitem construir uma interface de modelo como conjunto de outras interfaces.

Por exemplo, a interface [do termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) é definida como um modelo. Pode incorporar esta interface como um ou mais componentes quando definir o [modelo do Controlador de Temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). No exemplo seguinte, estes componentes são chamados `thermostat1` e `thermostat2` .

Para um modelo DTDL com vários componentes, existem duas ou mais secções componentes. Cada secção `@type` definiu `Component` e explicitamente refere-se a um esquema como mostrado no seguinte corte:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Este modelo tem três componentes definidos na secção de conteúdos - dois `Thermostat` componentes e um `DeviceInformation` componente. A secção de conteúdos também inclui definições de propriedade, telemetria e comando.

As imagens que se seguem mostram como este modelo aparece na IoT Central. As definições de propriedade, telemetria e comando no controlador de temperatura aparecem no **componente predefinido** de nível superior . As definições de propriedade, telemetria e comando para cada termóstato aparecem nas definições dos componentes:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Screenshot mostrando o modelo do dispositivo do controlador de temperatura no IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Screenshot mostrando os componentes do termóstato no modelo do dispositivo do controlador de temperatura no IoT Central.":::

Para aprender a escrever código de dispositivo que interage com componentes, consulte [o guia de desenvolvimento do dispositivo IoT Plug and Play](concepts-developer-guide-device.md).

Para aprender a escrever código de serviço que interliga com componentes num dispositivo, consulte o [guia de desenvolvimento de serviços IoT Plug and Play](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Herança

A herança permite-lhe reutilizar as capacidades numa interface base para alargar as capacidades de uma interface. Por exemplo, vários modelos de dispositivos podem partilhar capacidades comuns, como um número de série:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Exemplo de herança num modelo de dispositivo que mostra um termóstato e um controlador de fluxo que partilham capacidades a partir de uma interface base." border="false":::

O seguinte corte mostra um modelo DTML que usa a `extends` palavra-chave para definir a relação de herança mostrada no diagrama anterior:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

A imagem a seguir mostra este modelo no ambiente do modelo do dispositivo IoT Central:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Screenshot mostrando a herança da interface no IoT Central":::

Quando escreve um dispositivo ou código do lado do serviço, o seu código não precisa de fazer nada de especial para lidar com interfaces herdadas. No exemplo mostrado nesta secção, o código do dispositivo relata o número de série como se fosse parte da interface do termóstato.

### <a name="tips"></a>Sugestões

Pode combinar componentes e heranças quando criar um modelo. O diagrama seguinte mostra um `thermostat` modelo herdado de uma `baseDevice` interface. A `baseDevice` interface tem um componente, que por si só herda de outra interface:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagrama mostrando um modelo que usa componentes e herança." border="false":::

O seguinte corte mostra um modelo DTML que utiliza as `extends` `component` palavras-chave e as palavras-chave para definir a relação de herança e a utilização do componente mostradas no diagrama anterior:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Tipos de dados

Utilize tipos de dados para definir parâmetros de telemetria, propriedades e comando. Os tipos de dados podem ser primitivos ou complexos. Os tipos de dados complexos utilizam primitivos ou outros tipos complexos. A profundidade máxima para tipos complexos é de cinco níveis.

### <a name="primitive-types"></a>Tipos primitivos

A tabela a seguir mostra o conjunto de tipos primitivos que pode utilizar:

| Tipo primitivo | Description |
| --- | --- |
| `boolean` | Um valor booleano |
| `date` | Data completa, tal como definida na [secção 5.6 do RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Data-hora tal como definida no [RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Um ponto flutuante IEEE 8 byte |
| `duration` | Duração no formato ISO 8601 |
| `float` | Um ponto flutuante IEEE 4 byte |
| `integer` | Um inteiro de 4 bytes assinado |
| `long` | Um inteiro de 8 bytes assinado |
| `string` | Uma corda UTF8 |
| `time` | Um tempo integral, tal como definido na [secção 5.6 do RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

O seguinte corte mostra uma definição de telemetria exemplo que utiliza o `double` tipo no `schema` campo:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Tipos de dados complexos

Os tipos de dados complexos são de *matriz,* *enumeração,* *mapa,* *objeto,* ou um dos tipos geoespaciais.

#### <a name="arrays"></a>Matrizes

Um conjunto é um tipo de dado indexável onde todos os elementos são do mesmo tipo. O tipo de elemento pode ser um tipo primitivo ou complexo.

O seguinte corte mostra uma definição de telemetria exemplo que utiliza o `Array` tipo no `schema` campo. Os elementos da matriz são booleanos:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Enumerações

Uma enumeração descreve um tipo com um conjunto de rótulos nomeados que mapeiam valores. Os valores podem ser inteiros ou cordas, mas as etiquetas são sempre cordas.

O seguinte corte mostra uma definição de telemetria exemplo que utiliza o `Enum` tipo no `schema` campo. Os valores da enumeração são inteiros:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Mapas

Um mapa é um tipo com pares de valores-chave onde todos os valores têm o mesmo tipo. A chave de um mapa deve ser uma corda. Os valores num mapa podem ser qualquer tipo, incluindo outro tipo complexo.

O seguinte corte mostra uma definição de propriedade exemplo que usa o `Map` tipo no `schema` campo. Os valores no mapa são as cordas:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objetos

Um tipo de objeto é composto por campos nomeados. Os tipos de campos num mapa de objetos podem ser primitivos ou complexos.

O seguinte corte mostra uma definição de telemetria exemplo que utiliza o `Object` tipo no `schema` campo. Os campos do objeto `dateTime` `duration` são, e `string` tipos:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Tipos geoespaciais

A DTDL fornece um conjunto de tipos geoespaciais, baseados na [GeoJSON,](https://geojson.org/)para modelar estruturas geográficas de dados: `point` , , , , , , e `multiPoint` `lineString` `multiLineString` `polygon` `multiPolygon` . Estes tipos são estruturas aninhadas predefinidas de matrizes, objetos e enumerações.

O seguinte corte mostra uma definição de telemetria exemplo que utiliza o `point` tipo no `schema` campo:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Como os tipos geoespaciais são baseados em matrizes, não podem ser usados em definições de propriedade.

## <a name="semantic-types"></a>Tipos semânticos

O tipo de dados de uma definição de propriedade ou telemetria especifica o formato dos dados que um dispositivo troca com um serviço. O tipo semântico fornece informações sobre telemetria e propriedades que uma aplicação pode usar para determinar como processar ou exibir um valor. Cada tipo semântico tem uma ou mais unidades associadas. Por exemplo, celsius e fahrenheit são unidades para o tipo semântico de temperatura. Os dashboards e análises da IoT Central podem usar a informação semântica do tipo para determinar como traçar valores de telemetria ou propriedade e unidades de exibição. Para saber como pode usar o model parser para ler os tipos semânticos, consulte [o parser de modelos de gémeos digitais.](concepts-model-parser.md)

O seguinte corte mostra uma definição de telemetria exemplo que inclui informações de tipo semântico. O tipo semântico `Temperature` é adicionado à `@type` matriz, e o `unit` valor, é uma das `degreeCelsius` unidades válidas para o tipo semântico:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Localização

As aplicações, como a IoT Central, utilizam informações no modelo para construir dinamicamente uma UI em torno dos dados que são trocados com um dispositivo IoT Plug e Play. Por exemplo, os azulejos num tablier podem apresentar nomes e descrições para telemetria, propriedades e comandos.

O opcional `description` e os campos no modelo `displayName` seguram as cordas destinadas a ser utilizadas numa UI. Estes campos podem conter cordas localizadas que uma aplicação pode usar para tornar uma UI localizada.

O seguinte corte mostra uma definição de telemetria de temperatura exemplo que inclui cordas localizadas:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Adicionar cordas localizadas é opcional. O exemplo a seguir tem apenas uma única língua padrão:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Ciclo de vida e ferramentas

As quatro fases do ciclo de vida de um modelo de dispositivo são a autoria, publicação, utilização e versão:

### <a name="author"></a>Autor

Os modelos de dispositivos DTML são documentos JSON que pode criar num editor de texto. No entanto, no IoT Central pode utilizar o ambiente GUI do modelo de dispositivo para criar um modelo DTML. No IoT Central pode:

- Criar interfaces que definam propriedades, telemetria e comandos.
- Utilize componentes para montar várias interfaces em conjunto.
- Defina relações de herança entre interfaces.
- Importar e exportar ficheiros de modelos DTML.

Para saber mais, consulte [Definir um novo tipo de dispositivo IoT na sua aplicação Azure IoT Central](../iot-central/core/howto-set-up-template.md).

O [editor DTDL para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) dá-lhe um ambiente de edição baseado em texto com validação de sintaxe e preconização automática para um controlo mais fino sobre a experiência de autoria do modelo.

### <a name="publish"></a>Publicar

Para tornar os seus modelos DTML partilháveis e detetáveis, publica-os num repositório de modelos de dispositivos.

Antes de publicar um modelo no repositório público, pode utilizar as `dmr-client` ferramentas para validar o seu modelo.

Para saber mais, consulte [o repositório de modelos de dispositivos.](concepts-model-repository.md)

### <a name="use"></a>Utilização

Aplicações, como a IoT Central, utilizam modelos de dispositivos. No IoT Central, um modelo faz parte do modelo do dispositivo que descreve as capacidades do dispositivo. O IoT Central utiliza o modelo do dispositivo para construir dinamicamente um UI para o dispositivo, incluindo dashboards e análises.

Uma solução personalizada pode usar o [model parser de gémeos digitais](concepts-model-parser.md) para entender as capacidades de um dispositivo que implementa o modelo. Para saber mais, consulte [use os modelos IoT Plug e Play numa solução IoT](concepts-model-discovery.md).

### <a name="version"></a>Versão

Para garantir que os dispositivos e soluções do lado do servidor que utilizam os modelos continuam a funcionar, os modelos publicados são imutáveis.

O DTMI inclui um número de versão que pode utilizar para criar várias versões de um modelo. Os dispositivos e as soluções do lado do servidor podem utilizar a versão específica que foram concebidas para serem utilizadas.

A IoT Central implementa mais regras de versão para os modelos de dispositivos. Se for a versão de um modelo de dispositivo e o seu modelo no IoT Central, pode migrar dispositivos de versões anteriores para versões posteriores. No entanto, os dispositivos migrados não podem utilizar novas capacidades sem uma atualização de firmware. Para saber mais, consulte [Criar uma nova versão do modelo do dispositivo](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Limites e constrangimentos

A lista que se segue resume alguns constrangimentos e limites fundamentais nos modelos:

- Atualmente, a profundidade máxima para matrizes, mapas e objetos é de cinco níveis de profundidade.
- Não pode usar matrizes em definições de propriedade.
- Pode estender as interfaces a uma profundidade de 10 níveis.
- Uma interface pode estender-se no máximo a duas outras interfaces.
- Um componente não pode conter outro componente.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre modelação de dispositivos, aqui estão alguns recursos adicionais:

- [Instale e utilize as ferramentas de autoria DTDL](howto-use-dtdl-authoring-tools.md)
- [Linguagem de definição de gémeos digitais v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repositórios de modelos](./concepts-model-repository.md)
