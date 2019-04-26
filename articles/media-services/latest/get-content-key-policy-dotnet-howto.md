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
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322639"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obter uma chave de assinatura da política existente

Um dos principais princípios de design da API v3 é tornar a API mais segura. v3 APIs não devolverem segredos ou as credenciais no **Obtenha** ou **lista** operações. Ver a explicação detalhada aqui: Para obter mais informações, consulte [contas RBAC e dos serviços de multimédia](rbac-overview.md)

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

[Design of a multi-DRM content protection system with access control](design-multi-drm-system-with-access-control.md) (Design de um sistema de proteção de conteúdo multi-DRM com controlo de acesso) 
