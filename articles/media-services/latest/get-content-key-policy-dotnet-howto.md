---
title: Obtenha uma chave de assinatura de uma política utilizando a Azure Media Services v3 .NET
description: Este tópico mostra como obter uma chave de assinatura da política existente usando Os Serviços de Mídia v3 .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 7107a2acdc0ca430797245125ca903f7414908da
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427079"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obter uma chave de assinatura da política existente

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um dos principais princípios de design da API v3 é tornar a API mais segura. V3 APIs não devolvem segredos ou credenciais em operações **Get** ou **List.** Consulte aqui a explicação detalhada: Para mais informações, consulte as [contas do Azure RBAC e dos Serviços de Comunicação Social](rbac-overview.md)

O exemplo deste artigo mostra como usar .NET para obter uma chave de assinatura da política existente. 
 
## <a name="download"></a>Download 

Clone um repositório GitHub que contenha a amostra completa .NET à sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo ContentKeyPolicy com segredos está localizado na pasta [EncryptWithDRM.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)

## <a name="get-contentkeypolicy-with-secrets"></a>Obtenha contentKeyPolicy com segredos 

Para chegar à chave, use **GetPolicyPropertiesWithSecretsAsync**, como mostra o exemplo abaixo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Passos seguintes

[Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md) 
