---
title: Referência da biblioteca de cliente de funções do Azure duplos Digital definidas pelo utilizador | Documentos da Microsoft
description: Referência da biblioteca de cliente de funções do Azure duplos Digital definidas pelo utilizador.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 06/06/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: be05cec8e3d755f1b04e5ecc5ec7c740053a74d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073042"
---
# <a name="user-defined-functions-client-library-reference"></a>Referência da biblioteca de cliente funções definidas pelo utilizador

Este documento fornece informações de referência para a biblioteca de cliente de funções definidas pelo utilizador duplos Digital do Azure.

## <a name="helper-methods"></a>Métodos auxiliares

A biblioteca de cliente define métodos auxiliares para operações usadas.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Tendo em conta um identificador de espaço, esta função recupera o espaço do gráfico.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identificador de espaço |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Tendo em conta um identificador de sensor, essa função obtém o sensor do gráfico.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identificador de sensor |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Tendo em conta um identificador de dispositivo, esta função recupera o dispositivo do gráfico.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identificador de dispositivo |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Tendo em conta um identificador de sensor e o respetivo tipo de dados, essa função obtém o valor atual para esse sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identificador de sensor |
| *dataType*  | `string` | tipo de dados do sensor |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Com um identificador de espaço e o nome do valor, esta função recupera o valor atual para essa propriedade de espaço.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identificador de espaço |
| *valueName* | `string` | nome da propriedade de espaço |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Tendo em conta um identificador de sensor e o respetivo tipo de dados, esta função recupera os valores históricos para esse sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensor |
| *dataType* | `string` | tipo de dados do sensor |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Com um identificador de espaço e o nome do valor, esta função recupera os valores históricos para essa propriedade no espaço de.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *valueName* | `string` | nome da propriedade de espaço |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Tendo em conta um identificador de espaço, essa função obtém os espaços de subordinados para esse espaço principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Tendo em conta um identificador de espaço, essa função obtém os sensores de subordinados para esse espaço principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Tendo em conta um identificador de espaço, essa função obtém os dispositivos de subordinados para esse espaço principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Tendo em conta um identificador de dispositivo, esta função recupera os sensores de subordinados para esse dispositivo principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificador de dispositivo |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Tendo em conta um identificador de espaço, essa função obtém seu espaço de principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identificador de espaço |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Tendo em conta um identificador de sensor, essa função obtém seu espaço de principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificador de sensor |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Tendo em conta um identificador de dispositivo, essa função obtém seu espaço de principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identificador de dispositivo |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Tendo em conta um identificador de sensor, essa função obtém o respetivo dispositivo principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificador de sensor |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>⇒ getSpaceExtendedProperty (spaceId, propertyName) `extendedProperty`

Tendo em conta um identificador de espaço, esta função recupera a propriedade e o respetivo valor do espaço.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *propertyName* | `string` | nome da propriedade de espaço |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>⇒ getSensorExtendedProperty (sensorId, propertyName) `extendedProperty`

Tendo em conta um identificador de sensor, esta função recupera a propriedade e o respetivo valor do sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensor |
| *propertyName* | `string` | nome de propriedade do sensor |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Tendo em conta um identificador de dispositivo, esta função recupera a propriedade e o respetivo valor do dispositivo.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificador de dispositivo |
| *propertyName* | `string` | nome de propriedade do dispositivo |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Esta função define um valor no objeto de sensor com o tipo de dados fornecido.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensor |
| *dataType*  | `string` | tipo de dados do sensor |
| *valor*  | `string` | Value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Esta função define um valor no objeto espaço com o tipo de dados fornecido.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *dataType* | `string` | Tipo de dados |
| *valor* | `string` | Value |

### <a name="logmessage"></a>log(Message)

Esta função regista a mensagem seguinte dentro da função definida pelo utilizador.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *message* | `string` | mensagem de ter sessão iniciada |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Esta função envia uma notificação para ser expedida.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identificador de objeto de gráfico. Os exemplos são espaço, o sensor e o ID de dispositivo.|
| *topologyObjectType*  | `string` | Os exemplos são sensor e do dispositivo.|
| *payload*  | `string` | O payload JSON para serem enviados com a notificação. |

## <a name="return-types"></a>Tipos de retorno

Os modelos de resposta retornados dos métodos de programa auxiliar de referência do cliente são descritos abaixo.

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

#### <a name="parent--space"></a>Parent() ⇒ `space`

Esta função devolve o espaço de principal do espaço atual.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Essa função retorna o filho sensores do espaço atual.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Essa função retorna o filho de dispositivos do espaço atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e o respetivo valor para o atual espaço.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | nome da propriedade expandida |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Esta função devolve o valor do espaço atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | nome do valor |

#### <a name="historyvaluename--value"></a>History(VALUENAME) ⇒ `value[]`

Esta função devolve os valores de histórico do espaço atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | nome do valor |

#### <a name="notifypayload"></a>NOTIFY(Payload)

Esta função envia uma notificação com o payload especificado.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON para incluir na notificação |

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

Esta função devolve o espaço de principal do dispositivo atual.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Essa função retorna o filho sensores do dispositivo atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e o respetivo valor para o dispositivo atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | nome da propriedade expandida |

#### <a name="notifypayload"></a>NOTIFY(Payload)

Esta função envia uma notificação com o payload especificado.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON para incluir na notificação |

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

### <a name="sensor-methods"></a>Métodos de sensor

#### <a name="space--space"></a>Space() ⇒ `space`

Esta função devolve o espaço de principal do sensor de corrente.

#### <a name="device--device"></a>Device() ⇒ `device`

Esta função devolve o dispositivo principal do sensor de corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e o respetivo valor para o sensor de corrente.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | nome da propriedade expandida |

#### <a name="value--value"></a>Value () ⇒ `value`

Esta função devolve o valor do sensor de corrente.

#### <a name="history--value"></a>History() ⇒ `value[]`

Esta função devolve os valores de histórico do sensor de corrente.

#### <a name="notifypayload"></a>NOTIFY(Payload)

Esta função envia uma notificação com o payload especificado.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON para incluir na notificação |

### <a name="value"></a>Value

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Propriedade expandida

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [as funções definidas pelo utilizador de duplos Digital do Azure](./concepts-user-defined-functions.md).

- Saiba mais [como criar funções definidas pelo utilizador](./how-to-user-defined-functions.md).

- Saiba mais [como depurar funções definidas pelo utilizador](./how-to-diagnose-user-defined-functions.md).
