---
title: Funções definidas pelo utilizador referência da biblioteca do cliente - Azure Digital Twins [ Azure Digital Twins ] Microsoft Docs
description: Azure Digital Twins funções definidas para o utilizador documentação de referência da biblioteca do cliente.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276820"
---
# <a name="user-defined-functions-client-library-reference"></a>Funções definidas pelo utilizador referência da biblioteca do cliente

Este documento fornece informações de referência para a biblioteca de clientes de funções definidas pelo utilizador azure Digital Twins.

## <a name="helper-methods"></a>Métodos de ajuda

A biblioteca do cliente define métodos de ajudante para operações comumente usadas.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata (id)`space`

Dado um identificador de espaço, esta função recupera o espaço do gráfico.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ---------- | ------------------- | ------------ |
| *ID*  | `guid` | Identificador de espaço |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata (id)`sensor`

Dado um identificador de sensor, esta função recupera o sensor do gráfico.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ---------- | ------------------- | ------------ |
| *ID*  | `guid` | Identificador de sensores |

### <a name="getdevicemetadataid--device"></a>obterDeviceMetadata (id)`device`

Dado um identificador de dispositivo, esta função recupera o dispositivo a partir do gráfico.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *ID* | `guid` | Identificador de dispositivo |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, dataType)`value`

Dado um identificador de sensor e o seu tipo de dados, esta função recupera o valor atual para esse sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identificador de sensores |
| *dataType*  | `string` | Tipo de dados de sensores |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (spaceId, valueName)`value`

Dado um identificador de espaço e o nome de valor, esta função recupera o valor atual para aquela propriedade espacial.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identificador de espaço |
| *valueName* | `string` | Nome da propriedade espacial |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, dataType)`value[]`

Dado um identificador de sensores e o seu tipo de dados, esta função recupera os valores históricos para esse sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensores |
| *dataType* | `string` | Tipo de dados de sensores |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, dataType)`value[]`

Dado um identificador de espaço e o nome de valor, esta função recupera os valores históricos dessa propriedade no espaço.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *valueName* | `string` | Nome da propriedade espacial |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces (spaceId)`space[]`

Dado um identificador de espaço, esta função recupera os espaços infantis para aquele espaço dos pais.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors (spaceId)`sensor[]`

Dado um identificador de espaço, esta função recupera os sensores infantis para o espaço dos pais.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices (spaceId)`device[]`

Dado um identificador de espaço, esta função recupera os dispositivos infantis para esse espaço dos pais.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>obterDeviceChildSensors (dispositivoId) ]`sensor[]`

Dado um identificador de dispositivo, esta função recupera os sensores infantis para o dispositivo principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificador de dispositivo |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace (childSpaceId)`space`

Dado um identificador de espaço, esta função recupera o seu espaço principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *criançaSpaceId* | `guid` | Identificador de espaço |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace (childSensorId) ]`space`

Dado um identificador de sensores, esta função recupera o seu espaço principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *criançaSensorId* | `guid` | Identificador de sensores |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace (childDeviceId) ]`space`

Dado um identificador de dispositivo, esta função recupera o seu espaço principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *criançaDeviceId* | `guid` | Identificador de dispositivo |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice (childSensorId) ]`space`

Dado um identificador de sensores, esta função recupera o seu dispositivo principal.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *criançaSensorId* | `guid` | Identificador de sensores |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, propertyName)`extendedProperty`

Dado um identificador de espaço, esta função recupera a propriedade e o seu valor a partir do espaço.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *propriedadeName* | `string` | Nome da propriedade espacial |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, propertyName)`extendedProperty`

Dado um identificador de sensores, esta função recupera a propriedade e o seu valor a partir do sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensores |
| *propriedadeName* | `string` | Nome da propriedade do sensor |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (dispositivoId, propertyName)`extendedProperty`

Dado um identificador de dispositivo, esta função recupera a propriedade e o seu valor a partir do dispositivo.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificador de dispositivo |
| *propriedadeName* | `string` | Nome de propriedade do dispositivo |

### <a name="setsensorvaluesensorid-datatype-value"></a>conjuntoSensorValue (sensorId, dataType, valor)

Esta função define um valor no objeto sensor com o tipo de dados dado.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensores |
| *dataType*  | `string` | Tipo de dados de sensores |
| *valor*  | `string` | Valor |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, dataType, valor)

Esta função define um valor no objeto espacial com o tipo de dados dado.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *dataType* | `string` | Tipo de dados |
| *valor* | `string` | Valor |

### <a name="logmessage"></a>log (mensagem)

Esta função regista a seguinte mensagem dentro da função definida pelo utilizador.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *Mensagem* | `string` | Mensagem a ser registada |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>envioNotificação (topologyObjectId, topologyObjectType, carga útil)

Esta função envia uma notificação personalizada para ser despachada.

**Tipo**: função global

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *topologiaObjectId*  | `guid` | Identificador de objeto gráfico. Exemplos são o espaço, o sensor e a identificação do dispositivo.|
| *topologiaObjectType*  | `string` | Exemplos são sensor e dispositivo.|
| *payload*  | `string` | A carga útil da JSON a enviar com a notificação. |

## <a name="return-types"></a>Tipos de retorno

Os modelos de resposta devolvidos dos métodos de ajuda de referência do cliente são descritos abaixo.

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

### <a name="space-methods"></a>Métodos espaciais

#### <a name="parent--space"></a>Pais()`space`

Esta função devolve o espaço dos pais do espaço atual.

#### <a name="childsensors--sensor"></a>Sensores infantis()`sensor[]`

Esta função devolve os sensores infantis do espaço atual.

#### <a name="childdevices--device"></a>ChildDevices()`device[]`

Esta função devolve os dispositivos infantis do espaço atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName)`extendedProperty`

Esta função devolve a propriedade estendida e o seu valor para o espaço atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propriedadeName* | `string` | Nome da propriedade estendida |

#### <a name="valuevaluename--value"></a>Valor(valorNome)`value`

Esta função devolve o valor do espaço atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome do valor |

#### <a name="historyvaluename--value"></a>História(nome de valor)`value[]`

Esta função devolve os valores históricos do espaço atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome do valor |

#### <a name="notifypayload"></a>Notificação (carga útil)

Esta função envia uma notificação com a carga útil especificada.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Carga útil da JSON para incluir na notificação |

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

#### <a name="parent--space"></a>Pais()`space`

Esta função devolve o espaço dos pais do dispositivo atual.

#### <a name="childsensors--sensor"></a>Sensores infantis()`sensor[]`

Esta função devolve os sensores infantis do dispositivo atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName)`extendedProperty`

Esta função devolve a propriedade estendida e o seu valor para o dispositivo atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propriedadeName* | `string` | Nome da propriedade estendida |

#### <a name="notifypayload"></a>Notificação (carga útil)

Esta função envia uma notificação com a carga útil especificada.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Carga útil da JSON para incluir na notificação |

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

#### <a name="space--space"></a>Espaço()`space`

Esta função devolve o espaço dos pais do sensor atual.

#### <a name="device--device"></a>Dispositivo()`device`

Esta função devolve o dispositivo-mãe do sensor atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName)`extendedProperty`

Esta função devolve a propriedade estendida e o seu valor para o sensor atual.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *propriedadeName* | `string` | Nome da propriedade estendida |

#### <a name="value--value"></a>Valor()`value`

Esta função devolve o valor do sensor atual.

#### <a name="history--value"></a>História()`value[]`

Esta função devolve os valores históricos do sensor atual.

#### <a name="notifypayload"></a>Notificação (carga útil)

Esta função envia uma notificação com a carga útil especificada.

| Parâmetro  | Tipo                | Descrição  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Carga útil da JSON para incluir na notificação |

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

- Conheça as [funções definidas pelo utilizador da Azure Digital Twins.](./concepts-user-defined-functions.md)

- Aprenda [a criar funções definidas pelo utilizador](./how-to-user-defined-functions.md).

- Aprenda [a depurar funções definidas pelo utilizador](./how-to-diagnose-user-defined-functions.md).
