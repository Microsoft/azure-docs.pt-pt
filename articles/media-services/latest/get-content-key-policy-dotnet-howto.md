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
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80065966"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obter uma chave de assinatura da política existente

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

[Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md) 
