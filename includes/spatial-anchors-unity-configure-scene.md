---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998110"
---
O próximo passo é configurar a aplicação para utilizar o identificador de conta e a chave de conta. Copiou-os para um editor de texto ao [criar o recurso Spatial Anchors.](#create-a-spatial-anchors-resource)

No painel do **Projeto,** navegue para `Assets\AzureSpatialAnchors.SDK\Resources` . Selecione `SpatialAnchorConfig`. Em seguida, no painel do **Inspetor,** insira o `Account Key` valor para e como o valor para `Spatial Anchors Account Key` `Account ID` `Spatial Anchors Account Id` .

Em seguida, `SpatialAnchorManager.cs` abra. Encontre `CreateSessionAsync()` e adicione a seguinte linha, substituindo no domínio da sua conta anterior: `session.Configuration.AccountDomain = "MyAccountDomain";` . Pode adicionar esta linha diretamente antes deste comentário `// Configure authentication` .
