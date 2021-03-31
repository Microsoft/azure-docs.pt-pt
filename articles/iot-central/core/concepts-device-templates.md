---
title: O que são modelos de dispositivo em Azure IoT Central | Microsoft Docs
description: Os modelos do dispositivo Azure IoT Central permitem especificar o comportamento dos dispositivos ligados à sua aplicação. Um modelo de dispositivo especifica a telemetria, propriedades e comandos que o dispositivo deve implementar. Um modelo de dispositivo também define o UI para o dispositivo em IoT Central, como os formulários e dashboards que um operador utiliza.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 04c2330ffee396f5fc30b85640e992df77c08263
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97795433"
---
# <a name="what-are-device-templates"></a>O que são modelos de dispositivo?

_Este artigo aplica-se aos desenvolvedores de dispositivos e aos construtores de soluções._

Um modelo de dispositivo no Azure IoT Central é uma planta que define as características e comportamentos de um tipo de dispositivo que se conecta à sua aplicação. Por exemplo, o modelo do dispositivo define a telemetria que um dispositivo envia para que a IoT Central possa criar visualizações que utilizem as unidades e tipos de dados corretos.

Um construtor de soluções adiciona modelos de dispositivo a uma aplicação IoT Central. Um desenvolvedor de dispositivos escreve o código do dispositivo que implementa os comportamentos definidos no modelo do dispositivo.

Um modelo de dispositivo inclui as seguintes secções:

- _Um modelo de dispositivo._ Esta parte do modelo do dispositivo define como o dispositivo interage com a sua aplicação. Um desenvolvedor de dispositivos implementa os comportamentos definidos no modelo.
    - _Componente predefinido_. Cada modelo de dispositivo tem um componente predefinido. A interface do componente predefinido descreve capacidades específicas do modelo do dispositivo.
    - _Componentes_. Um modelo de dispositivo pode incluir componentes para além do componente padrão para descrever as capacidades do dispositivo. Cada componente tem uma interface que descreve as capacidades do componente. As interfaces dos componentes podem ser reutilizadas noutros modelos do dispositivo. Por exemplo, vários modelos de dispositivos telefónicos poderiam utilizar a mesma interface de câmara.
    - _Interfaces herdadas._ Um modelo de dispositivo contém uma ou mais interfaces que ampliam as capacidades do componente padrão.
- _Propriedades na nuvem_. Esta parte do modelo do dispositivo permite ao desenvolvedor da solução especificar quaisquer metadados do dispositivo para armazenar. As propriedades da nuvem nunca são sincronizadas com dispositivos e só existem na aplicação. As propriedades da nuvem não afetam o código que um desenvolvedor de dispositivos escreve para implementar o modelo do dispositivo.
- _Personalizações._ Esta parte do modelo do dispositivo permite que o desenvolvedor de solução substitua algumas das definições no modelo do dispositivo. As personalizações são úteis se o desenvolvedor de solução quiser refinar a forma como a aplicação lida com um valor, como alterar o nome de ecrã para uma propriedade ou a cor usada para exibir um valor de telemetria. As personalizações não afetam o código que um desenvolvedor de dispositivos escreve para implementar o modelo do dispositivo.
- _Vistas._ Esta parte do modelo do dispositivo permite ao desenvolvedor de solução definir visualizações para visualizar dados do dispositivo, e formas para gerir e controlar um dispositivo. As vistas utilizam o modelo do dispositivo, propriedades em nuvem e personalizações. As visualizações não afetam o código que um desenvolvedor de dispositivos escreve para implementar o modelo do dispositivo.

## <a name="device-models"></a>Modelos de dispositivos

Um modelo de dispositivo define como um dispositivo interage com a sua aplicação IoT Central. O desenvolvedor do dispositivo deve certificar-se de que o dispositivo implementa os comportamentos definidos no modelo do dispositivo para que o IoT Central possa monitorizar e gerir o dispositivo. Um modelo de dispositivo é composto por uma ou mais _interfaces,_ e cada interface pode definir uma coleção de tipos de _telemetria,_ propriedades do _dispositivo,_ e _comandos_. Um desenvolvedor de soluções pode importar um ficheiro JSON que define o modelo do dispositivo num modelo de dispositivo, ou usar a UI web no IoT Central para criar ou editar um modelo de dispositivo. As alterações a um modelo de dispositivo feito através da UI Web exigem que o modelo do [dispositivo seja versão](./howto-version-device-template.md).

Um desenvolvedor de soluções também pode exportar um ficheiro JSON que contenha o modelo do dispositivo. Um desenvolvedor de dispositivos pode usar este documento JSON para entender como o dispositivo deve comunicar com a aplicação IoT Central.

O ficheiro JSON que define o modelo do dispositivo utiliza o [Digital Twin Definition Language (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). A IoT Central espera que o ficheiro JSON contenha o modelo do dispositivo com as interfaces definidas em linha, em vez de em ficheiros separados.

Um dispositivo IoT típico é composto por:

- Peças personalizadas, que são as coisas que tornam o seu dispositivo único.
- Peças padrão, que são coisas que são comuns a todos os dispositivos.

Estas peças são chamadas _interfaces_ num modelo de dispositivo. As interfaces definem os detalhes de cada parte que o seu dispositivo implementa. As interfaces são reutilizáveis em todos os modelos do dispositivo. No DTDL, um componente refere-se a uma interface definida num ficheiro DTDL separado.

O exemplo a seguir mostra o esboço do modelo do dispositivo para um dispositivo controlador de temperatura. O componente predefinido inclui definições para `workingSet` `serialNumber` , e `reboot` . O modelo do dispositivo também inclui as `thermostat` `deviceInformation` interfaces:

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
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
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
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
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

Uma interface tem alguns campos necessários:

- `@id`: um ID único sob a forma de um simples Nome de Recurso Uniforme.
- `@type`: declara que este objeto é uma interface.
- `@context`: especifica a versão DTDL utilizada para a interface.
- `contents`: lista as propriedades, a telemetria e os comandos que compõem o seu dispositivo. As capacidades podem ser definidas em várias interfaces.

Existem alguns campos opcionais que pode usar para adicionar mais detalhes ao modelo de capacidade, como o nome do ecrã e a descrição.

Cada entrada na lista de interfaces na secção de implementações tem um:

- `name`: o nome de programação da interface.
- `schema`: a interface que o modelo de capacidade implementa.

## <a name="interfaces"></a>Interfaces

O DTDL permite-lhe descrever as capacidades do seu dispositivo. As capacidades relacionadas são agrupadas em interfaces. As interfaces descrevem as propriedades, a telemetria e comanda uma parte dos instrumentos do seu dispositivo:

- `Properties`. As propriedades são campos de dados que representam o estado do seu dispositivo. Utilize propriedades para representar o estado duradouro do dispositivo, como o estado de saída de uma bomba de refrigeração. As propriedades também podem representar propriedades básicas do dispositivo, como a versão firmware do dispositivo. Pode declarar propriedades apenas para leitura ou legitável. Apenas os dispositivos podem atualizar o valor de uma propriedade apenas de leitura. Um operador pode definir o valor de uma propriedade writable para enviar para um dispositivo.
- `Telemetry`. Os campos de telemetria representam medições dos sensores. Sempre que o seu dispositivo efetuar uma medição do sensor, deve enviar um evento de telemetria contendo os dados do sensor.
- `Commands`. Os comandos representam métodos que os utilizadores do seu dispositivo podem executar no dispositivo. Por exemplo, um comando de reset ou um comando para ligar ou desligar uma ventoinha.

O exemplo a seguir mostra a definição de interface do termóstato:

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
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
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
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
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
        "name" : "tempReport",
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
              "name" : "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name" : "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name" : "endTime",
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

Este exemplo mostra duas propriedades (uma só leitura e uma writable), um tipo de telemetria e um comando. Uma descrição mínima de campo tem um:

- `@type` para especificar o tipo de capacidade: `Telemetry` `Property` , , ou `Command` .  Em alguns casos, o tipo inclui um tipo semântico para permitir que a IoT Central faça algumas suposições sobre como lidar com o valor.
- `name` para o valor da telemetria.
- `schema` para especificar o tipo de dados para a telemetria ou a propriedade. Este valor pode ser um tipo primitivo, como duplo, inteiro, booleano ou corda. Os tipos e mapas complexos de objetos também são suportados.

Os campos opcionais, como o nome do ecrã e a descrição, permitem adicionar mais detalhes à interface e às capacidades.

## <a name="properties"></a>Propriedades

Por padrão, as propriedades são apenas de leitura. Propriedades apenas de leitura significam que o dispositivo reporta atualizações do valor da propriedade para a sua aplicação IoT Central. Sua aplicação IoT Central não pode definir o valor de uma propriedade apenas de leitura.

Também pode marcar uma propriedade como escrita numa interface. Um dispositivo pode receber uma atualização para uma propriedade escrita a partir da sua aplicação IoT Central, bem como reportar atualizações de valor da propriedade para a sua aplicação.

Os dispositivos não precisam de ser ligados para definir valores de propriedade. Os valores atualizados são transferidos quando o dispositivo se liga à aplicação. Este comportamento aplica-se a propriedades apenas de leitura e de escrita.

Não utilize propriedades para enviar telemetria do seu dispositivo. Por exemplo, uma propriedade de leitura apenas, como `temperatureSetting=80` deve significar que a temperatura do dispositivo foi definida para 80, e o dispositivo está tentando chegar ou ficar a esta temperatura.

Para propriedades writable, a aplicação do dispositivo devolve um código de estado, versão e descrição desejadas para indicar se recebeu e aplicou o valor da propriedade.

## <a name="telemetry"></a>Telemetria

O IoT Central permite-lhe ver a telemetria nos dashboards e gráficos e utilizar regras para desencadear ações quando os limiares são atingidos. A IoT Central utiliza as informações no modelo do dispositivo, tais como tipos de dados, unidades e nomes de exibição, para determinar como exibir valores de telemetria.

Pode utilizar a funcionalidade de exportação de dados IoT Central para transmitir telemetria para outros destinos, como armazenamento ou Centros de Eventos.

## <a name="commands"></a>Comandos

Um comando deve ser executado dentro de 30 segundos por defeito, e o dispositivo deve ser ligado quando o comando chegar. Se o dispositivo responder a tempo ou o dispositivo não estiver ligado, o comando falha.

Os comandos podem ter parâmetros de pedido e devolver uma resposta.

### <a name="offline-commands"></a>Comandos offline

Pode escolher comandos de fila se um dispositivo estiver atualmente offline, ativando a opção **"Fila" se** for por opção offline para um comando no modelo do dispositivo.

Os comandos offline são notificações unidirecionais para o dispositivo a partir da sua solução. Os comandos offline podem ter parâmetros de pedido, mas não devolvem uma resposta.

> [!NOTE]
> Esta opção só está disponível na UI web IoT Central. Esta definição não está incluída se exportar um modelo ou interface a partir do modelo do dispositivo.

## <a name="cloud-properties"></a>Propriedades da cloud

As propriedades da nuvem fazem parte do modelo do dispositivo, mas não fazem parte do modelo do dispositivo. As propriedades da nuvem permitem ao desenvolvedor da solução especificar quaisquer metadados do dispositivo para armazenar na aplicação IoT Central. As propriedades da nuvem não afetam o código que um desenvolvedor de dispositivos escreve para implementar o modelo do dispositivo.

Um desenvolvedor de soluções pode adicionar propriedades em nuvem a dashboards e vistas ao lado das propriedades do dispositivo para permitir que um operador gere os dispositivos ligados à aplicação. Um desenvolvedor de soluções também pode usar propriedades de nuvem como parte de uma definição de regra para tornar um valor limiar editável por um operador.

## <a name="customizations"></a>Personalizações

As personalizações fazem parte do modelo do dispositivo, mas não fazem parte do modelo do dispositivo. As personalizações permitem ao desenvolvedor de soluções melhorar ou sobrepor algumas das definições no modelo do dispositivo. Por exemplo, um desenvolvedor de solução pode alterar o nome de visualização para um tipo de telemetria ou propriedade. Um desenvolvedor de soluções também pode usar personalizações para adicionar validação, como um comprimento mínimo ou máximo para uma propriedade de dispositivo de corda.

As personalizações podem afetar o código que um desenvolvedor de dispositivos escreve para implementar o modelo do dispositivo. Por exemplo, uma personalização poderia definir comprimentos mínimos e máximos de cordas ou valores numéricos mínimos e máximos para a telemetria.

## <a name="views"></a>Vistas

Um desenvolvedor de soluções cria pontos de vista que permitem aos operadores monitorizar e gerir dispositivos conectados. As vistas fazem parte do modelo do dispositivo, por isso uma vista está associada a um tipo de dispositivo específico. Uma vista pode incluir:

- Gráficos para tramar telemetria.
- Azulejos para exibir propriedades do dispositivo apenas de leitura.
- Azulejos para permitir que o operador edite propriedades do dispositivo writable.
- Azulejos para permitir que o operador edite propriedades na nuvem.
- Azulejos para permitir que o operador ligue comandos, incluindo comandos que esperam uma carga útil.
- Azulejos para exibir etiquetas, imagens ou texto de marcação.

A telemetria, propriedades e comandos que pode adicionar a uma vista são determinados pelo modelo do dispositivo, propriedades na nuvem e personalizações no modelo do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Como desenvolvedor de dispositivos, agora que aprendeu sobre os modelos do dispositivo, um próximo passo sugerido é ler [telemetria, propriedade e cargas de comando](./concepts-telemetry-properties-commands.md) para saber mais sobre os dados que um dispositivo troca com a IoT Central.

Como desenvolvedor de soluções, um próximo passo sugerido é ler [Definir um novo tipo de dispositivo IoT na sua aplicação Azure IoT Central](./howto-set-up-template.md) para saber mais sobre como criar um modelo de dispositivo.
