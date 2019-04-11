---
title: Obter uma chave de assinatura da política existente com o SDK de .NET do serviços de multimédia v3 - Azure | Documentos da Microsoft
description: Este tópico mostra como obter uma chave de assinatura da política existente com o SDK de .NET dos serviços de multimédia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471176"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obter uma chave de assinatura da política existente

Um dos principais princípios de design da API v3 é tornar a API mais segura. v3 APIs não devolverem segredos ou as credenciais no **Obtenha** ou **lista** operações. As chaves são sempre nulas, vazias ou saneadas da resposta. O utilizador precisa para chamar um método de ação separada para obter segredos ou as credenciais. O **leitor** função não é possível chamar operações para que ele não é possível chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite-lhe definir permissões de segurança mais granulares do RBAC numa função personalizada, se assim o desejar.

Para obter mais informações, consulte [contas RBAC e dos serviços de multimédia](rbac-overview.md)

O exemplo neste artigo mostra como usar o .NET para obter uma chave de assinatura da política existente. 
 
## <a name="download"></a>Transferência 

Clone um repositório do GitHub que contém o exemplo de .NET completo para o seu computador com o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
ContentKeyPolicy com o exemplo de segredos está localizado na [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) pasta.

## <a name="get-contentkeypolicy-with-secrets"></a>Obter ContentKeyPolicy com segredos 

Para obter a chave, utilize **GetPolicyPropertiesWithSecretsAsync**, conforme mostrado no exemplo abaixo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Passos Seguintes

[Criação de um sistema de proteção de conteúdo multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md) 
