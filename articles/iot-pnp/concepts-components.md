---
title: Compreender componentes nos modelos IoT Plug and Play Microsoft Docs
description: Compreenda a diferença entre os modelos IoT Plug e Play DTDL que utilizam componentes e modelos que não utilizam componentes.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c4a32a5c929e74332e85ceb6f4cff787e237e385
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069650"
---
# <a name="iot-plug-and-play-components-in-models"></a>Componentes IoT Plug e Play nos modelos

Nas convenções IoT Plug and Play, um dispositivo é um dispositivo IoT Plug and Play se apresentar o seu ID modelo de definição de gémeos digitais (DTDL) quando se conecta a um hub IoT.

O seguinte corte mostra alguns exemplos de IDs de modelo:

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

Embora o modelo não defina explicitamente um componente, comporta-se como se houvesse um único componente com todas as definições de telemetria, propriedade e comando.

A imagem que se segue mostra como o modelo aparece na ferramenta exploradora Azure IoT:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Componente padrão no explorador Azure IoT":::

O ID do modelo é armazenado numa propriedade gémea do dispositivo, como mostra a seguinte imagem:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="ID modelo em propriedade digital twin":::

Um modelo DTDL sem componentes é uma simplificação útil para um dispositivo com um único conjunto de telemetria, propriedades e comandos. Um modelo que não utiliza componentes facilita a migração de um dispositivo existente para ser um dispositivo IoT Plug and Play - cria um modelo DTDL que descreve o seu dispositivo real sem a necessidade de definir quaisquer componentes.

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

Este modelo tem três componentes definidos na secção de conteúdos - dois `Thermostat` componentes e um `DeviceInformation` componente. Há também um componente de raiz padrão.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre componentes de modelos, aqui estão alguns recursos adicionais:

- [Linguagem de definição de gémeos digitais v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repositórios de modelos](./concepts-model-repository.md)