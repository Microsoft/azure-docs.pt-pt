---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: 820353ffbb5e23e27c3039ad27a8f6507b9d2167
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608894"
---
<!--Create a media services asset REST-->

O seguinte comando Azure .NET cria um novo ativo media. Substitua os valores `subscriptionID` `resourceGroup` , e pelos `amsAccountName` valores com os quais está a trabalhar. Dê um nome ao seu ativo definindo `assetName` aqui.

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```
