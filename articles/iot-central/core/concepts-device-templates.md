---
title: O que são modelos de dispositivo no Azure IoT Central Microsoft Docs
description: Os modelos do dispositivo Azure IoT Central permitem especificar o comportamento dos dispositivos ligados à sua aplicação. Um modelo de dispositivo especifica a telemetria, propriedades e comandos que o dispositivo deve implementar. Um modelo de dispositivo também define o UI para o dispositivo em IoT Central, como os formulários e dashboards que um operador utiliza.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 75317b5c6af2d0ce89d2db32f4343d9cc73a1a81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91813173"
---
# <a name="what-are-device-templates"></a>O que são modelos de dispositivo?

_Este artigo aplica-se aos desenvolvedores de dispositivos e aos construtores de soluções._

Um modelo de dispositivo no Azure IoT Central é uma planta que define as características e comportamentos de um tipo de dispositivo que se conecta à sua aplicação. Por exemplo, o modelo do dispositivo define a telemetria que um dispositivo envia para que a IoT Central possa criar visualizações que utilizem as unidades e tipos de dados corretos.

Um construtor de soluções adiciona modelos de dispositivo a uma aplicação IoT Central. Um desenvolvedor de dispositivos escreve o código do dispositivo que implementa os comportamentos definidos no modelo do dispositivo.

Um modelo de dispositivo inclui as seguintes secções:

- _Um modelo de capacidade do dispositivo (DCM)_. Esta parte do modelo do dispositivo define como o dispositivo interage com a sua aplicação. Um desenvolvedor de dispositivos implementa os comportamentos definidos no DCM.
    - _Interfaces_. Um DCM contém uma ou mais interfaces que definem a telemetria, propriedades e comandos que o dispositivo deve implementar.
- _Propriedades na nuvem_. Esta parte do modelo do dispositivo permite ao desenvolvedor da solução especificar quaisquer metadados do dispositivo para armazenar. As propriedades da nuvem nunca são sincronizadas com dispositivos e só existem na aplicação. As propriedades da nuvem não afetam o código que um desenvolvedor de dispositivos escreve para implementar o DCM.
- _Personalizações._ Esta parte do modelo do dispositivo permite que o desenvolvedor de solução substitua algumas das definições no DCM. As personalizações são úteis se o desenvolvedor de solução quiser refinar a forma como a aplicação lida com um valor, como alterar o nome de ecrã para uma propriedade ou a cor usada para exibir um valor de telemetria. As personalizações não afetam o código que um desenvolvedor de dispositivos escreve para implementar o DCM.
- _Vistas._ Esta parte do modelo do dispositivo permite ao desenvolvedor de solução definir visualizações para visualizar dados do dispositivo, e formas para gerir e controlar um dispositivo. As vistas utilizam o DCM, propriedades em nuvem e personalizações. As vistas não afetam o código que um desenvolvedor de dispositivos escreve para implementar o DCM.

## <a name="device-capability-models"></a>Modelos de função de dispositivo

Um DCM define como um dispositivo interage com a sua aplicação IoT Central. O desenvolvedor do dispositivo deve certificar-se de que o dispositivo implementa os comportamentos definidos no DCM para que o IoT Central possa monitorizar e gerir o dispositivo. Um DCM é composto por uma ou mais _interfaces,_ e cada interface pode definir uma coleção de tipos de _telemetria,_ propriedades do _dispositivo,_ e _comandos_. Um desenvolvedor de solução pode importar um ficheiro JSON que define o DCM num modelo de dispositivo, ou usar a UI web no IoT Central para criar ou editar um DCM. As alterações a um DCM efetuadas através do UI web exigem que o modelo do [dispositivo seja versão](./howto-version-device-template.md).

Um desenvolvedor de soluções também pode exportar um ficheiro JSON que contenha o DCM. Um desenvolvedor de dispositivos pode usar este documento JSON para entender como o dispositivo deve comunicar com a aplicação IoT Central.

O ficheiro JSON que define o DCM utiliza a [Linguagem de Definição Digital Twin (DTDL) V1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). A IoT Central espera que o ficheiro JSON contenha o DCM com as interfaces definidas em linha, em vez de em ficheiros separados.

Um dispositivo IoT típico é composto por:

- Peças personalizadas, que são as coisas que tornam o seu dispositivo único.
- Peças padrão, que são coisas que são comuns a todos os dispositivos.

Estas peças são chamadas _interfaces_ num DCM. As interfaces definem os detalhes de cada parte que o seu dispositivo implementa. As interfaces são reutilizáveis através dos DCMs.

O exemplo a seguir mostra o esboço do modelo de capacidade do dispositivo para um dispositivo sensor ambiental com duas interfaces:

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Um modelo de capacidade tem alguns campos necessários:

- `@id`: um ID único sob a forma de um simples Nome de Recurso Uniforme.
- `@type`: declara que este objeto é um modelo de capacidade.
- `@context`: especifica a versão DTDL utilizada para o modelo de capacidade.
- `implements`: lista as interfaces que o seu dispositivo implementa.

Cada entrada na lista de interfaces na secção de implementações tem um:

- `name`: o nome de programação da interface.
- `schema`: a interface que o modelo de capacidade implementa.

Uma interface tem alguns campos necessários:

- `@id`: um ID único sob a forma de um simples Nome de Recurso Uniforme.
- `@type`: declara que este objeto é uma interface.
- `@context`: especifica a versão DTDL utilizada para a interface.
- `contents`: lista as propriedades, a telemetria e os comandos que compõem o seu dispositivo.

Existem alguns campos opcionais que pode usar para adicionar mais detalhes ao modelo de capacidade, como o nome do ecrã e a descrição.

## <a name="interfaces"></a>Interfaces

O DTDL permite-lhe descrever as capacidades do seu dispositivo. As capacidades relacionadas são agrupadas em interfaces. As interfaces descrevem as propriedades, a telemetria e comanda uma parte dos instrumentos do seu dispositivo:

- `Properties`. As propriedades são campos de dados que representam o estado do seu dispositivo. Utilize propriedades para representar o estado duradouro do dispositivo, como o estado de saída de uma bomba de refrigeração. As propriedades também podem representar propriedades básicas do dispositivo, como a versão firmware do dispositivo. Pode declarar propriedades apenas para leitura ou legitável. Apenas os dispositivos podem atualizar o valor de uma propriedade apenas de leitura. Um operador pode definir o valor de uma propriedade writable para enviar para um dispositivo.
- `Telemetry`. Os campos de telemetria representam medições dos sensores. Sempre que o seu dispositivo efetuar uma medição do sensor, deve enviar um evento de telemetria contendo os dados do sensor.
- `Commands`. Os comandos representam métodos que os utilizadores do seu dispositivo podem executar no dispositivo. Por exemplo, um comando de reset ou um comando para ligar ou desligar uma ventoinha.

O exemplo a seguir mostra a definição de interface de sensores ambientais:

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

Este exemplo mostra duas propriedades (uma só leitura e uma writable), um tipo de telemetria e dois comandos. Uma descrição mínima de campo tem um:

- `@type` para especificar o tipo de capacidade: `Telemetry` `Property` , , ou `Command` .  Em alguns casos, o tipo inclui um tipo semântico para permitir que a IoT Central faça algumas suposições sobre como lidar com o valor.
- `name` para o valor da telemetria.
- `schema` para especificar o tipo de dados para a telemetria ou a propriedade. Este valor pode ser um tipo primitivo, como duplo, inteiro, booleano ou corda. Os tipos complexos de objetos, matrizes e mapas também são suportados.
- `commandType` para especificar como o comando deve ser tratado.

Os campos opcionais, como o nome do ecrã e a descrição, permitem adicionar mais detalhes à interface e às capacidades.

## <a name="properties"></a>Propriedades

Por padrão, as propriedades são apenas de leitura. Propriedades apenas de leitura significam que o dispositivo reporta atualizações do valor da propriedade para a sua aplicação IoT Central. Sua aplicação IoT Central não pode definir o valor de uma propriedade apenas de leitura.

Também pode marcar uma propriedade como escrita numa interface. Um dispositivo pode receber uma atualização para uma propriedade escrita a partir da sua aplicação IoT Central, bem como reportar atualizações de valor da propriedade para a sua aplicação.

Os dispositivos não precisam de ser ligados para definir valores de propriedade. Os valores atualizados são transferidos quando o dispositivo se liga à aplicação. Este comportamento aplica-se a propriedades apenas de leitura e de escrita.

Não utilize propriedades para enviar telemetria do seu dispositivo. Por exemplo, uma propriedade de leitura apenas, como `temperatureSetting=80` deve significar que a temperatura do dispositivo foi definida para 80, e o dispositivo está tentando chegar ou ficar a esta temperatura.

Para propriedades writable, a aplicação do dispositivo devolve um código de estado, versão e descrição desejadas para indicar se recebeu e aplicou o valor da propriedade.

## <a name="telemetry"></a>Telemetria

O IoT Central permite-lhe ver a telemetria nos dashboards e gráficos e utilizar regras para desencadear ações quando os limiares são atingidos. A IoT Central utiliza as informações no DCM, tais como tipos de dados, unidades e nomes de exibição, para determinar como exibir valores de telemetria.

Pode utilizar a funcionalidade de exportação de dados IoT Central para transmitir telemetria para outros destinos, como armazenamento ou Centros de Eventos.

## <a name="commands"></a>Comandos

Os comandos são sincronizados ou assíncronos. Um comando sincronizado deve ser executado dentro de 30 segundos por defeito, e o dispositivo deve ser ligado quando o comando chegar. Se o dispositivo responder a tempo ou o dispositivo não estiver ligado, o comando falha.

Utilize comandos assíncronos para operações de longa duração. O dispositivo envia informações de progresso utilizando mensagens de telemetria. Estas mensagens de progresso têm as seguintes propriedades do cabeçalho:

- `iothub-command-name`: o nome de comando, por exemplo `UpdateFirmware` .
- `iothub-command-request-id`: o ID de pedido gerado no lado do servidor e enviado para o dispositivo na chamada inicial.
- `iothub-interface-id`: O ID da interface neste comando é definido, por `urn:example:AssetTracker:1` exemplo.
 `iothub-interface-name`: o nome da instância desta interface, por exemplo `myAssetTracker` .
- `iothub-command-statuscode`: o código de estado devolvido do dispositivo, por exemplo `202` .

## <a name="cloud-properties"></a>Propriedades da cloud

As propriedades da nuvem fazem parte do modelo do dispositivo, mas não fazem parte do DCM. As propriedades da nuvem permitem ao desenvolvedor da solução especificar quaisquer metadados do dispositivo para armazenar na aplicação IoT Central. As propriedades da nuvem não afetam o código que um desenvolvedor de dispositivos escreve para implementar o DCM.

Um desenvolvedor de soluções pode adicionar propriedades em nuvem a dashboards e vistas ao lado das propriedades do dispositivo para permitir que um operador gere os dispositivos ligados à aplicação. Um desenvolvedor de soluções também pode usar propriedades de nuvem como parte de uma definição de regra para tornar um valor limiar editável por um operador.

## <a name="customizations"></a>Personalizações

As personalizações fazem parte do modelo do dispositivo, mas não fazem parte do DCM. As personalizações permitem ao desenvolvedor de soluções melhorar ou sobrepor algumas das definições no DCM. Por exemplo, um desenvolvedor de solução pode alterar o nome de visualização para um tipo de telemetria ou propriedade. Um desenvolvedor de soluções também pode usar personalizações para adicionar validação, como um comprimento mínimo ou máximo para uma propriedade de dispositivo de corda.

As personalizações podem afetar o código que um desenvolvedor de dispositivos escreve para implementar o DCM. Por exemplo, uma personalização poderia definir comprimentos mínimos e máximos de cordas ou valores numéricos mínimos e máximos para a telemetria.

## <a name="views"></a>Vistas

Um desenvolvedor de soluções cria pontos de vista que permitem aos operadores monitorizar e gerir dispositivos conectados. As vistas fazem parte do modelo do dispositivo, por isso uma vista está associada a um tipo de dispositivo específico. Uma vista pode incluir:

- Gráficos para tramar telemetria.
- Azulejos para exibir propriedades do dispositivo apenas de leitura.
- Azulejos para permitir que o operador edite propriedades do dispositivo writable.
- Azulejos para permitir que o operador edite propriedades na nuvem.
- Azulejos para permitir que o operador ligue comandos, incluindo comandos que esperam uma carga útil.
- Azulejos para exibir etiquetas, imagens ou texto de marcação.

A telemetria, propriedades e comandos que pode adicionar a uma vista são determinados pelo DCM, propriedades em nuvem e personalizações no modelo do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Como desenvolvedor de dispositivos, agora que aprendeu sobre os modelos do dispositivo, um próximo passo sugerido é ler [telemetria, propriedade e cargas de comando](./concepts-telemetry-properties-commands.md) para saber mais sobre os dados que um dispositivo troca com a IoT Central.

Como desenvolvedor de soluções, um próximo passo sugerido é ler [Definir um novo tipo de dispositivo IoT na sua aplicação Azure IoT Central](./howto-set-up-template.md) para saber mais sobre como criar um modelo de dispositivo.
