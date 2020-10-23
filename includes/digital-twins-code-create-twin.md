---
author: baanders
description: incluir arquivo para Azure Digital Twins - código para criar um gémeo
ms.service: digital-twins
ms.topic: include
ms.date: 10/21/2020
ms.author: baanders
ms.openlocfilehash: a5a6246bd6667a3aee3774f5dd309f8af1e86344
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426135"
---
```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
//Create the twin
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```