---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: e3ef7e7b8f527cde1fcfbb13141fd3a520921267
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673429"
---
O próximo passo é configurar a aplicação para utilizar o identificador de conta e a chave de conta. Copiou-os para um editor de texto ao [criar o recurso Spatial Anchors.](#create-a-spatial-anchors-resource)

No painel do **Projeto,** navegue para `Assets\AzureSpatialAnchors.SDK\Resources` . Selecione `SpatialAnchorConfig`. Em seguida, no painel do **Inspetor,** insira o `Account Key` valor para e como o valor para `Spatial Anchors Account Key` `Account ID` `Spatial Anchors Account Id` .

Em seguida, `SpatialAnchorManager.cs` abra. Encontre `CreateSessionAsync()` e adicione a seguinte linha, substituindo no domínio da sua conta anterior: `session.Configuration.AccountId = "MyAccountDomain";` . Pode adicionar esta linha diretamente antes deste comentário `// Configure authentication` .
