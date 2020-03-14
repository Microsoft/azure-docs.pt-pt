---
title: Obtenha uma chave de assinatura de uma política utilizando o Azure Media Services v3 .NET
description: Este tópico mostra como obter uma chave de assinatura da política existente usando Media Services v3 .NET SDK.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: ff27ae0fd639316f03fe89ffc906561b3ef85f6f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251456"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obtenha uma chave de assinatura da política existente

Um dos principais princípios de design da API v3 é tornar a API mais segura. v3 APIs não devolvem segredos ou credenciais nas operações **de Get** ou **List.** Consulte a explicação detalhada aqui: Para mais informações, consulte [as contas rBAC e Media Services](rbac-overview.md)

O exemplo deste artigo mostra como usar .NET para obter uma chave de assinatura da política existente. 
 
## <a name="download"></a>Transferência 

Clone um repositório GitHub que contenha a amostra completa .NET à sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo de ContentKeyPolicy com segredos está localizado na pasta [EncryptWithDRM.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)

## <a name="get-contentkeypolicy-with-secrets"></a>Obtenha ContentKeyPolicy com segredos 

Para chegar à chave, utilize o **GetPolicyPropertiesWithSecretsAsync,** como mostra o exemplo abaixo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Passos seguintes

[Design of a multi-DRM content protection system with access control](design-multi-drm-system-with-access-control.md) (Design de um sistema de proteção de conteúdo multi-DRM com controlo de acesso) 
