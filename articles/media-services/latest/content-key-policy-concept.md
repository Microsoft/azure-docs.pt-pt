---
title: Conteúdo de diretivas de chave nos serviços de multimédia - Azure | Documentos da Microsoft
description: Este artigo fornece uma explicação das quais são as diretivas de chave de conteúdo e como elas são usadas pelos serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68778cea51144ec33efd4d5843a51b489ea17ca4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155730"
---
# <a name="content-key-policies"></a>Políticas de Chave de Conteúdo

Com os serviços de multimédia, pode fornecer seu conteúdo ao vivo e sob demanda dinamicamente encriptado com o Advanced Encryption Standard (AES-128) ou qualquer um dos três sistemas de gestão (DRM) de direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados.

Para especificar opções de encriptação no seu fluxo, tem de criar uma [política de transmissão em fluxo](streaming-policy-concept.md) e associá-lo com sua [localizador de transmissão em fluxo](streaming-locators-concept.md). Tem de criar uma [política de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) para configurar como a chave de conteúdo (que fornece acesso seguro à sua [ativos](assets-concept.md)) é entregue para clientes finais. O **política de chave de conteúdo** também está associado com seu **localizador de transmissão em fluxo**. Tem de definir os requisitos (restrições) na política de chave de conteúdo que devem ser satisfeitas para que as chaves com a configuração especificada para ser distribuída aos clientes. 

Recomenda-se para permitir que os serviços de multimédia para gerar automaticamente chaves de conteúdo. Normalmente, usaria uma chave de longa duração e verificar a existência de políticas com **obter**. Para obter a chave, terá de chamar um método de ação separada para obter segredos ou as credenciais, consulte o exemplo a seguir.

**Políticas de chaves de conteúdo** são atualizáveis. Por exemplo, pode querer atualizar a política se tiver de fazer uma rotação de chaves. Pode atualizar a chave de verificação primária e a lista de chaves de verificação alternativo na política existente. Pode demorar até 15 minutos para os caches de entrega de chave atualizar e escolher a política atualizada. 

> [!IMPORTANT]
> * Propriedades de **diretivas de chave de conteúdo** que são de Datetime tipo são sempre em formato UTC.
> * Deve criar um conjunto limitado de políticas para a sua conta de serviço de suporte de dados e utilizá-los novamente para os localizadores de transmissão em fluxo sempre que as mesmas opções são necessárias. Para obter mais informações, consulte [Quotas e limitações](limits-quotas-constraints.md).

## <a name="example"></a>Exemplo

Para obter a chave, utilize **GetPolicyPropertiesWithSecretsAsync**, conforme mostrado no exemplo abaixo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Paginação de filtragem, ordenação,

Ver [filtragem, ordenação, a paginação de entidades de serviços de multimédia](entities-overview.md).

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Utilizar DRM dinâmico licença e de encriptação de serviço de entrega](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
