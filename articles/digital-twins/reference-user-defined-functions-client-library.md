---
title: Referência de funções definidas pelo usuário do Azure digital gêmeos | Microsoft Docs
description: Referência da biblioteca de cliente das funções definidas pelo usuário do Azure digital gêmeos.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: daab63067087267c12cef75178b5f974043e0fd9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931233"
---
# <a name="user-defined-functions-client-library-reference"></a>Referência da biblioteca de cliente de funções definidas pelo usuário

Este documento fornece informações de referência para a biblioteca de cliente de funções definidas pelo usuário do Azure digital gêmeos.

## <a name="helper-methods"></a>Métodos auxiliares

A biblioteca de cliente define métodos auxiliares para operações comumente usadas.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata (ID) ⇒ `space`

Dado um identificador de espaço, essa função recupera o espaço do grafo.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ---------- | ------------------- | ------------ |
| *sessão*  | `guid` | Identificador de espaço |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata (ID) ⇒ `sensor`

Dado um identificador de sensor, essa função recupera o sensor do grafo.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ---------- | ------------------- | ------------ |
| *sessão*  | `guid` | Identificador do sensor |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata (ID) ⇒ `device`

Dado um identificador de dispositivo, essa função recupera o dispositivo do grafo.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sessão* | `guid` | Identificador do dispositivo |

### <a name="getsensorvaluesensorid-datatype--value"></a>getsensorvalue (sensorid, dataType) ⇒ `value`

Dado um identificador de sensor e seu tipo de dados, essa função recupera o valor atual para esse sensor.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorid*  | `guid` | Identificador do sensor |
| *Tipo de dados*  | `string` | Tipo de dados do sensor |

### <a name="getspacevaluespaceid-valuename--value"></a>getspacevalue (spaceid, valorname) ⇒ `value`

Dado um identificador de espaço e o nome do valor, essa função recupera o valor atual dessa propriedade de espaço.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceid*  | `guid` | Identificador de espaço |
| *valueName* | `string` | Nome da propriedade de espaço |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorid, dataType) ⇒ `value[]`

Dado um identificador de sensor e seu tipo de dados, essa função recupera os valores históricos para esse sensor.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorid* | `guid` | Identificador do sensor |
| *Tipo de dados* | `string` | Tipo de dados do sensor |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceid, dataType) ⇒ `value[]`

Dado um identificador de espaço e o nome do valor, essa função recupera os valores históricos para essa propriedade no espaço.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceid* | `guid` | Identificador de espaço |
| *valueName* | `string` | Nome da propriedade de espaço |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces (spaceid) ⇒ `space[]`

Dado um identificador de espaço, essa função recupera os espaços filho para esse espaço pai.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceid* | `guid` | Identificador de espaço |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors (spaceid) ⇒ `sensor[]`

Dado um identificador de espaço, essa função recupera os sensores filho para esse espaço pai.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceid* | `guid` | Identificador de espaço |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices (spaceid) ⇒ `device[]`

Dado um identificador de espaço, essa função recupera os dispositivos filho para esse espaço pai.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceid* | `guid` | Identificador de espaço |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors (DeviceID) ⇒ `sensor[]`

Dado um identificador de dispositivo, essa função recupera os sensores filho para esse dispositivo pai.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificador do dispositivo |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace (childSpaceId) ⇒ `space`

Dado um identificador de espaço, essa função recupera seu espaço pai.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identificador de espaço |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace (childSensorId) ⇒ `space`

Dado um identificador de sensor, essa função recupera seu espaço pai.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificador do sensor |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace (childDeviceId) ⇒ `space`

Dado um identificador de dispositivo, essa função recupera seu espaço pai.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identificador do dispositivo |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice (childSensorId) ⇒ `space`

Dado um identificador de sensor, essa função recupera seu dispositivo pai.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificador do sensor |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceid, propertyName) ⇒ `extendedProperty`

Dado um identificador de espaço, essa função recupera a propriedade e seu valor do espaço.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceid* | `guid` | Identificador de espaço |
| *propertyName* | `string` | Nome da propriedade de espaço |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorid, propertyName) ⇒ `extendedProperty`

Dado um identificador de sensor, essa função recupera a propriedade e seu valor do sensor.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorid* | `guid` | Identificador do sensor |
| *propertyName* | `string` | Nome da Propriedade do sensor |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (DeviceID, propertyName) ⇒ `extendedProperty`

Dado um identificador de dispositivo, essa função recupera a propriedade e seu valor do dispositivo.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificador do dispositivo |
| *propertyName* | `string` | Nome da Propriedade do dispositivo |

### <a name="setsensorvaluesensorid-datatype-value"></a>setsensorvalue (sensorid, dataType, valor)

Essa função define um valor no objeto sensor com o tipo de dados fornecido.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorid* | `guid` | Identificador do sensor |
| *Tipo de dados*  | `string` | Tipo de dados do sensor |
| *valor*  | `string` | Valor |

### <a name="setspacevaluespaceid-datatype-value"></a>setspacevalue (spaceid, dataType, valor)

Essa função define um valor no objeto Space com o tipo de dados fornecido.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceid* | `guid` | Identificador de espaço |
| *Tipo de dados* | `string` | Data type |
| *valor* | `string` | Valor |

### <a name="logmessage"></a>log (mensagem)

Essa função registra a seguinte mensagem na função definida pelo usuário.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *Mensagem* | `string` | Mensagem a ser registrada |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, payload)

Essa função envia uma notificação personalizada a ser expedida.

**Kind**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identificador de objeto de grafo. Os exemplos são espaço, sensor e ID do dispositivo.|
| *topologyObjectType*  | `string` | Os exemplos são sensor e dispositivo.|
| *carga*  | `string` | A carga JSON a ser enviada com a notificação. |

## <a name="return-types"></a>Tipos de retorno

Os modelos de resposta retornados dos métodos auxiliares de referência do cliente são descritos abaixo.

### <a name="space"></a>Espaço

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>Métodos de espaço

#### <a name="parent--space"></a>`space` de ⇒ pai ()

Essa função retorna o espaço pai do espaço atual.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ `sensor[]`

Essa função retorna os sensores filho do espaço atual.

#### <a name="childdevices--device"></a>ChildDevices () ⇒ `device[]`

Essa função retorna os dispositivos filho do espaço atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>Extended (PropertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e seu valor para o espaço atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome da propriedade estendida |

#### <a name="valuevaluename--value"></a>Valor (valor) ⇒ `value`

Essa função retorna o valor do espaço atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome do valor |

#### <a name="historyvaluename--value"></a>History (valor) ⇒ `value[]`

Essa função retorna os valores históricos do espaço atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome do valor |

#### <a name="notifypayload"></a>Notificar (carga)

Essa função envia uma notificação com a carga especificada.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *carga* | `string` | Carga JSON a ser incluída na notificação |

### <a name="device"></a>Dispositivo

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Métodos do dispositivo

#### <a name="parent--space"></a>`space` de ⇒ pai ()

Essa função retorna o espaço pai do dispositivo atual.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ `sensor[]`

Essa função retorna os sensores filho do dispositivo atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>Extended (PropertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e seu valor para o dispositivo atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome da propriedade estendida |

#### <a name="notifypayload"></a>Notificar (carga)

Essa função envia uma notificação com a carga especificada.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *carga* | `string` | Carga JSON a ser incluída na notificação |

### <a name="sensor"></a>Sensores

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>Métodos de sensor

#### <a name="space--space"></a>⇒ De espaço () `space`

Essa função retorna o espaço pai do sensor atual.

#### <a name="device--device"></a>Dispositivo () ⇒ `device`

Essa função retorna o dispositivo pai do sensor atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>Extended (PropertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e seu valor para o sensor atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome da propriedade estendida |

#### <a name="value--value"></a>Valor () ⇒ `value`

Essa função retorna o valor do sensor atual.

#### <a name="history--value"></a>History () ⇒ `value[]`

Essa função retorna os valores históricos do sensor atual.

#### <a name="notifypayload"></a>Notificar (carga)

Essa função envia uma notificação com a carga especificada.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *carga* | `string` | Carga JSON a ser incluída na notificação |

### <a name="value"></a>Valor

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Propriedade estendida

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [funções definidas pelo usuário do Azure digital gêmeos](./concepts-user-defined-functions.md).

- Saiba [como criar funções definidas pelo usuário](./how-to-user-defined-functions.md).

- Saiba [como depurar funções definidas pelo usuário](./how-to-diagnose-user-defined-functions.md).
