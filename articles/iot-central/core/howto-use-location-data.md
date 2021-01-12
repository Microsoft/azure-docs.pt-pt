---
title: Utilize dados de localização numa solução Azure IoT Central
description: Saiba como utilizar os dados de localização enviados a partir de um dispositivo ligado à sua aplicação IoT Central. Defina dados de localização num mapa ou crie regras de geofencing.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128025"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Utilize dados de localização numa solução Azure IoT Central

Este artigo mostra-lhe como utilizar dados de localização numa aplicação IoT Central. Um dispositivo ligado à IoT Central pode enviar dados de localização como fluxo de telemetria ou usar uma propriedade do dispositivo para reportar dados de localização.

Um construtor de soluções pode usar os dados de localização para:

* Traçar a localização relatada num mapa.
* Traçar o histórico de localização da telemetria om um mapa.
* Crie regras de geofencagem para notificar um operador quando um dispositivo entra ou sai de uma área específica.

## <a name="add-location-capabilities-to-a-device-template"></a>Adicione capacidades de localização a um modelo de dispositivo

A imagem que se segue mostra um modelo de dispositivo com exemplos de uma propriedade do dispositivo e tipo de telemetria que utilizam dados de localização. As definições utilizam o tipo semântico **de localização** e o tipo de esquema de **geolocalização:**

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Screenshot mostrando a definição de propriedade de localização no modelo do dispositivo":::

Para referência, as definições [de Linguagem de Definição de Gémeos Digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para estas capacidades parecem o seguinte corte:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> O tipo de **esquema de geoponto** não faz parte da [especificação DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). A IoT Central suporta atualmente o tipo **de esquema de geoponto** e o tipo semântico **de localização** para retrocompatibilidade.

## <a name="send-location-data-from-a-device"></a>Enviar dados de localização a partir de um dispositivo

Quando um dispositivo envia dados para a propriedade **DeviceLocation** mostrada na secção anterior, a carga útil parece o seguinte corte JSON:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Quando um dispositivo envia dados para a telemetria **de rastreio** mostrada na secção anterior, a carga útil parece o seguinte corte JSON:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Localização do dispositivo de exibição

Pode apresentar dados de localização em vários locais da sua aplicação IoT Central. Por exemplo, sobre vistas associadas a um dispositivo individual ou em dashboards.

Quando cria uma vista para um dispositivo, pode optar por traçar a localização num mapa ou mostrar os valores individuais:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Screenshot mostrando a vista de exemplo com dados de localização":::

Pode adicionar azulejos de mapa a um dashboard para traçar a localização de um ou mais dispositivos. Quando você adiciona um azulejo de mapa para mostrar telemetria de localização, você pode traçar a localização durante um período de tempo. A imagem que se segue mostra a localização reportada por um dispositivo simulado nos últimos 30 minutos:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Screenshot mostrando exemplo painel de instrumentos com dados de localização":::

## <a name="create-a-geofencing-rule"></a>Criar uma regra de geofencing

Pode utilizar a telemetria de localização para criar uma regra de geofencing que gera um alerta quando um dispositivo se move para dentro ou para fora de uma área retangular. A imagem que se segue mostra uma regra que utiliza quatro condições para definir uma área retangular utilizando valores de latitude e longitude. A regra gera um e-mail quando o dispositivo se move para a área retangular:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Screenshot que mostra uma definição de regra de geofencing":::

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar propriedades na sua aplicação Azure IoT Central, consulte:

* [Cargas](concepts-telemetry-properties-commands.md)
* [Crie e conecte uma aplicação de cliente à sua aplicação Azure IoT Central](tutorial-connect-device.md)