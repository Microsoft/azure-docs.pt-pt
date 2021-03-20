---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88605973"
---
<!--Create a media services asset CLI-->

O seguinte comando Azure CLI cria um novo ativo media. Substitua os valores `assetName` `amsAccountName` e os `resourceGroup` valores com os quais está a trabalhar.  

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
