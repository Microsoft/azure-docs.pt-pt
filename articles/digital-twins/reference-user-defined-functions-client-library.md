---
title: User-defined functions client library reference - Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins user-defined functions client library reference documentation.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 41baacd4485e6702ec29057f5d539724b74e353b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383251"
---
# <a name="user-defined-functions-client-library-reference"></a>User-defined functions client library reference

This document provides reference information for the Azure Digital Twins user-defined functions client library.

## <a name="helper-methods"></a>Helper methods

The client library defines helper methods for commonly used operations.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Given a space identifier, this function retrieves the space from the graph.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Space identifier |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Given a sensor identifier, this function retrieves the sensor from the graph.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Sensor identifier |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Given a device identifier, this function retrieves the device from the graph.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Device identifier |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Given a sensor identifier and its data type, this function retrieves the current value for that sensor.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Sensor identifier |
| *dataType*  | `string` | Sensor data type |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Given a space identifier and the value name, this function retrieves the current value for that space property.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Space identifier |
| *valueName* | `string` | Space property name |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Given a sensor identifier and its data type, this function retrieves the historical values for that sensor.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor identifier |
| *dataType* | `string` | Sensor data type |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Given a space identifier and the value name, this function retrieves the historical values for that property on the space.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Space identifier |
| *valueName* | `string` | Space property name |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Given a space identifier, this function retrieves the child spaces for that parent space.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Space identifier |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Given a space identifier, this function retrieves the child sensors for that parent space.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Space identifier |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Given a space identifier, this function retrieves the child devices for that parent space.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Space identifier |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Given a device identifier, this function retrieves the child sensors for that parent device.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Device identifier |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Given a space identifier, this function retrieves its parent space.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Space identifier |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Given a sensor identifier, this function retrieves its parent space.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensor identifier |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Given a device identifier, this function retrieves its parent space.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Device identifier |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Given a sensor identifier, this function retrieves its parent device.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensor identifier |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Given a space identifier, this function retrieves the property and its value from the space.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Space identifier |
| *propertyName* | `string` | Space property name |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Given a sensor identifier, this function retrieves the property and its value from the sensor.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor identifier |
| *propertyName* | `string` | Sensor property name |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Given a device identifier, this function retrieves the property and its value from the device.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Device identifier |
| *propertyName* | `string` | Device property name |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

This function sets a value on the sensor object with the given data type.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor identifier |
| *dataType*  | `string` | Sensor data type |
| *valor*  | `string` | Valor |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

This function sets a value on the space object with the given data type.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Space identifier |
| *dataType* | `string` | Data type |
| *valor* | `string` | Valor |

### <a name="logmessage"></a>log(message)

This function logs the following message within the user-defined function.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Message to be logged |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

This function sends a custom notification out to be dispatched.

**Kind**: global function

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Graph object identifier. Examples are space, sensor, and device ID.|
| *topologyObjectType*  | `string` | Examples are sensor and device.|
| *payload*  | `string` | The JSON payload to be sent with the notification. |

## <a name="return-types"></a>Return types

The response models returned from client reference helper methods are described below.

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

### <a name="space-methods"></a>Space methods

#### <a name="parent--space"></a>Parent() ⇒ `space`

This function returns the parent space of the current space.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

This function returns the child sensors of the current space.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

This function returns the child devices of the current space.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

This function returns the extended property and its value for the current space.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Name of the extended property |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

This function returns the value of the current space.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Name of the value |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

This function returns the historical values of the current space.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Name of the value |

#### <a name="notifypayload"></a>Notify(payload)

This function sends a notification with the specified payload.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON payload to include in the notification |

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

#### <a name="parent--space"></a>Parent() ⇒ `space`

This function returns the parent space of the current device.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

This function returns the child sensors of the current device.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

This function returns the extended property and its value for the current device.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Name of the extended property |

#### <a name="notifypayload"></a>Notify(payload)

This function sends a notification with the specified payload.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON payload to include in the notification |

### <a name="sensor"></a>Sensor

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

### <a name="sensor-methods"></a>Sensor methods

#### <a name="space--space"></a>Space() ⇒ `space`

This function returns the parent space of the current sensor.

#### <a name="device--device"></a>Device() ⇒ `device`

This function returns the parent device of the current sensor.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

This function returns the extended property and its value for the current sensor.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Name of the extended property |

#### <a name="value--value"></a>Value() ⇒ `value`

This function returns the value of the current sensor.

#### <a name="history--value"></a>History() ⇒ `value[]`

This function returns the historical values of the current sensor.

#### <a name="notifypayload"></a>Notify(payload)

This function sends a notification with the specified payload.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON payload to include in the notification |

### <a name="value"></a>Valor

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Extended property

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Passos seguintes

- Learn about [Azure Digital Twins user-defined functions](./concepts-user-defined-functions.md).

- Learn [how to create user-defined functions](./how-to-user-defined-functions.md).

- Learn [how to debug user-defined functions](./how-to-diagnose-user-defined-functions.md).
