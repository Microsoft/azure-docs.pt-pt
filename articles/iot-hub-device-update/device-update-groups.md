---
title: Compreenda a atualização do dispositivo para grupos de dispositivos Azure IoT Hub | Microsoft Docs
description: Entenda como os grupos de dispositivos são usados.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101680012"
---
# <a name="device-groups"></a>Device groups

Um grupo de dispositivos é uma coleção de dispositivos. Os grupos de dispositivos fornecem uma forma de escalar as implementações em muitos dispositivos. Cada dispositivo pertence exatamente a um grupo de dispositivos de cada vez.
Pode optar por criar vários grupos de dispositivos para organizar os seus dispositivos. Por exemplo, a Contoso poderá utilizar o grupo de dispositivos "Flighting" para os dispositivos do seu laboratório de teste e o grupo de dispositivos "Avaliação" para os dispositivos que a sua equipa de campo utiliza no centro de operações. Além disso, a Contoso poderá optar por agrupar os seus dispositivos de Produção com base nas suas regiões geográficas, para que possam atualizar os dispositivos num horário alinhado com os seus fusos horários regionais. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Usando a etiqueta twin do dispositivo para a criação do grupo de dispositivos

As etiquetas gémeas do dispositivo permitem aos utilizadores agrupar dispositivos. Os dispositivos precisam de ter uma chave ADUGroup e um valor no seu dispositivo gémeo para permitir que sejam agrupados.

### <a name="device-twin-tag-format"></a>Formato de tag dupla do dispositivo

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Grupo de dispositivos não categorizados

Não categorizado é uma palavra reservada que é usada para agrupar dispositivos que:
- Não tem a etiqueta gémea do dispositivo ADUGroup.
- Tenha a etiqueta twin do dispositivo ADUGroup, mas um grupo não é criado com este nome de grupo.

Por exemplo, considere os dispositivos com as suas etiquetas gémeas do dispositivo abaixo:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Abaixo estão os dispositivos e os possíveis grupos que podem ser criados para eles.

|Dispositivo |Group  |
|-----------|--------------|
|Dispositivo1    |Grupo1|
|Dispositivo2    |Grupo1|
|Dispositivo3    |Grupo 2|
|Dispositivo4    |Não categorizado|



## <a name="next-steps"></a>Passos seguintes

[Criar grupo de dispositivos](./create-update-group.md)
