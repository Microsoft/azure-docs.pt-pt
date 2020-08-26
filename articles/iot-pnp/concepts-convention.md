---
title: IoT Plug and Play conventions / Microsoft Docs
description: A descrição das convenções IoT Plug and Play espera que os dispositivos utilizem quando enviam telemetria e propriedades, e manuseie comandos e atualizações de propriedade.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef29be53e776c4c185ac8430b3340c53ca85d855
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856045"
---
# <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Os dispositivos IoT Plug e Play Preview devem seguir um conjunto de convenções quando trocam mensagens com um hub IoT. Os dispositivos IoT Plug e Play Preview utilizam o protocolo MQTT para comunicar com o IoT Hub.

Você descreve a telemetria, propriedades e comandos que um dispositivo IoT Plug and Play implementa com um modelo [digital Twins Definition Language v2 (DTDL).](https://github.com/Azure/opendigitaltwins-dtdl) _model_ Existem dois tipos de modelo referidos neste artigo:

- **Sem componente** - Um modelo sem componentes. O modelo declara telemetria, propriedades e comandos como propriedades de alto nível na secção de conteúdo da interface principal.
- **Múltiplos componentes** - Um modelo composto por duas ou mais interfaces. Uma interface principal com telemetria, propriedades e comandos. Uma ou mais interfaces declaradas como componentes com telemetria adicional, propriedades e comandos.

Para obter mais informações, consulte [componentes IoT Plug e Play nos modelos.](concepts-components.md)

## <a name="identify-the-model"></a>Identificar o modelo

Para anunciar o modelo que implementa, um dispositivo IoT Plug and Play inclui o ID do modelo no pacote de conexão MQTT adicionando `model-id` ao `USERNAME` campo.

Para identificar o modelo que um dispositivo implementa, um serviço pode obter o ID do modelo a partir de:

- O `modelId` dispositivo twin field.
- O campo digital `$metadata.$model` gémeo.
- Uma notificação digital de mudança de gémeos.

## <a name="telemetry"></a>Telemetria

A telemetria enviada de um dispositivo sem componentes não requer metadados extra. O sistema adiciona a `dt-dataschema` propriedade.

A telemetria enviada a partir de um dispositivo de vários componentes deve adicionar `$.sub` como propriedade de mensagem. O sistema adiciona as `dt-subject` `dt-dataschema` propriedades e propriedades.

## <a name="read-only-properties"></a>Propriedades apenas de leitura

### <a name="sample-no-component-read-only-property"></a>Amostra nenhuma propriedade só de leitura de componente

Um dispositivo pode enviar qualquer JSON válido que siga as regras DTDL v2.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Carga útil da amostra**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Amostra de vários componentes apenas para ler propriedade

O dispositivo deve adicionar o `{"__t": "c"}` marcador para indicar que o elemento se refere a um componente.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade reportada**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Propriedades writable

O dispositivo deve confirmar que recebeu a propriedade enviando uma propriedade reportada. A propriedade reportada deve incluir:

- `value` - o valor real do imóvel (normalmente o valor recebido, mas o dispositivo pode decidir reportar um valor diferente).
- `ac` - um código de reconhecimento que utilize um código de estado HTTP.
- `av` - uma versão de reconhecimento que se refira à `$version` propriedade desejada.
- `ad` - uma descrição facultativa de reconhecimento.

### <a name="sample-no-component-writable-property"></a>Amostra sem propriedade de propriedade writable componente

Um dispositivo pode enviar qualquer JSON válido que siga as regras DTDL v2:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade desejada**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade reportada**

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
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Amostra de vários componentes propriedade writable

O dispositivo deve adicionar o `{"__t": "c"}` marcador para indicar que o elemento se refere a um componente.

O marcador é enviado apenas para atualizações do nível de componente, por isso os dispositivos não devem verificar se esta bandeira.

O dispositivo deve confirmar que recebeu a propriedade enviando uma propriedade reportada:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade desejada**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade reportada**

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
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Comandos

Nenhuma interface de componente utiliza o nome de comando sem um prefixo.

Num dispositivo, várias interfaces de componentes utilizam nomes de comando com o seguinte formato: `componentName*commandName` .

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre as convenções IoT Plug and Play, aqui estão alguns recursos adicionais:

- [Linguagem de definição de gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK de Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)
