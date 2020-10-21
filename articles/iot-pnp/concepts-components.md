---
title: Compreender componentes nos modelos IoT Plug and Play Microsoft Docs
description: Compreenda a diferença entre os modelos IoT Plug e Play DTDL que utilizam componentes e modelos que não utilizam componentes.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7d33811dc056baa3d205d7d86c1bdd7bff74ae54
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330007"
---
# <a name="iot-plug-and-play-components-in-models"></a>Componentes do IoT Plug and Play em modelos

Nas convenções IoT Plug and Play, um dispositivo será considerado um dispositivo IoT Plug and Play se apresentar o ID de modelo DTDL (Digital Twins Definition Language) quando se ligar a um hub IoT.

O fragmento a seguir mostra alguns IDs de modelos de exemplo:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Sem componentes

Um modelo simples não utiliza componentes embutidos ou em cascata. Inclui informações de cabeçalho e uma secção de conteúdo para definir telemetria, propriedades e comandos.

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

Embora o modelo não defina explicitamente um componente, comporta-se como se existisse um _único componente padrão_, com todas as definições de telemetria, propriedade e comando.

A imagem que se segue mostra como o modelo aparece na ferramenta exploradora Azure IoT:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Componente padrão no explorador Azure IoT":::

O ID do modelo é armazenado numa propriedade gémea do dispositivo, como mostra a seguinte imagem:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Componente padrão no explorador Azure IoT":::

Um modelo DTDL sem componentes é uma simplificação útil para um dispositivo ou módulo IoT Edge com um único conjunto de telemetria, propriedades e comandos. Um modelo que não utilize componentes facilita a migração de um dispositivo ou módulo existente para ser um dispositivo ou módulo IoT Plug and Play - cria um modelo DTDL que descreve o seu dispositivo ou módulo real sem a necessidade de definir quaisquer componentes.

> [!TIP]
> Um módulo pode ser um [módulo](../iot-hub/iot-hub-devguide-module-twins.md) de dispositivo ou um [módulo IoT Edge](../iot-edge/about-iot-edge.md).

## <a name="multiple-components"></a>Múltiplos componentes

Os componentes permitem construir uma interface de modelo como conjunto de outras interfaces.

Por exemplo, a interface [do termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) é definida como um modelo. Pode incorporar esta interface como um ou mais componentes quando define o [modelo do Controlador de Temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). No exemplo seguinte, estes componentes são chamados `thermostat1` e `thermostat2` .

Para um modelo DTDL com vários componentes, existem duas ou mais secções componentes. Cada secção `@type` definiu `Component` e explicitamente refere-se a um esquema como mostrado no seguinte corte:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
...
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
...
```

Este modelo tem três componentes definidos na secção de conteúdos - dois `Thermostat` componentes e um `DeviceInformation` componente. Há também um componente padrão.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre componentes de modelos, aqui estão alguns recursos adicionais:

- [Instale e utilize as ferramentas de autoria DTDL](howto-use-dtdl-authoring-tools.md)
- [Linguagem de definição de gémeos digitais v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repositórios de modelos](./concepts-model-repository.md)
