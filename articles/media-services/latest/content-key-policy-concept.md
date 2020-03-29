---
title: Políticas-chave de conteúdo nos Serviços de Media - Azure Microsoft Docs
description: Este artigo dá uma explicação sobre quais são as Políticas Chave de Conteúdo e como são usadas pela Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969909"
---
# <a name="content-key-policies"></a>Políticas de Chave de Conteúdo

Com os Media Services, pode fornecer os seus conteúdos ao vivo e a pedido encriptados dinamicamente com advanced Encryption Standard (AES-128) ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. A Media Services também fornece um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. 

Para especificar opções de encriptação no seu stream, precisa criar uma Política de [Streaming](streaming-policy-concept.md) e associá-la ao seu Localizador de [Streaming](streaming-locators-concept.md). Cria a [Política chave](https://docs.microsoft.com/rest/api/media/contentkeypolicies) de conteúdo para configurar como a chave de conteúdo (que fornece acesso seguro aos seus [Ativos)](assets-concept.md)é entregue aos clientes finais. É necessário definir os requisitos (restrições) na Política de Chave de Conteúdo que devem ser cumpridas para que as chaves com a configuração especificada sejam entregues aos clientes. A política chave de conteúdo não é necessária para um streaming ou download claros. 

Normalmente, associa a sua política de chave de conteúdo ao seu Localizador de [Streaming.](streaming-locators-concept.md) Em alternativa, pode especificar a política de chave de conteúdo dentro de uma Política de [Streaming](streaming-policy-concept.md) (quando criar uma política de streaming personalizada para cenários avançados). 

## <a name="best-practices-and-considerations"></a>Boas práticas e considerações

> [!IMPORTANT]
> Por favor, reveja as seguintes recomendações.

* Deverá conceber um conjunto limitado de políticas para a sua conta de Serviço de Media e reutilizá-las para os seus localizadores de streaming sempre que forem necessárias as mesmas opções. Para mais informações, consulte [Quotas e limitações.](limits-quotas-constraints.md)
* As políticas-chave de conteúdo são instáveis. Pode levar até 15 minutos para os caches de entrega chave atualizarem e pegarem na política atualizada. 

   Ao atualizar a apólice, está a sobrepor-se ao seu cache CDN existente, o que pode causar problemas de reprodução para os clientes que estão a utilizar conteúdo em cache.  
* Recomendamos que não crie uma nova política de chave de conteúdo para cada ativo. Os principais benefícios da partilha da mesma política de conteúdo entre ativos que necessitam das mesmas opções políticas são:
   
   * É mais fácil gerir um pequeno número de políticas.
   * Se precisar de fazer atualizações sobre a política chave de conteúdo, as alterações entram em vigor em todos os novos pedidos de licença quase imediatamente.
* Se precisa de criar uma nova política, tem de criar um novo localizador de streaming para o ativo.
* Recomenda-se que os Serviços de Media gerem automaticamente a chave de conteúdo. 

   Normalmente, você usaria uma chave de longa duração e verificaria a existência da política chave de conteúdo com [get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Para obter a chave, você precisa chamar um método de ação separado para obter segredos ou credenciais, ver o exemplo que se segue.

## <a name="example"></a>Exemplo

Para chegar à chave, utilize `GetPolicyPropertiesWithSecretsAsync`, como mostra a chave de assinatura Do [B.](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)

## <a name="filtering-ordering-paging"></a>Filtragem, encomenda, paging

Ver [Filtragem, encomenda, paging de entidades dos Serviços de Media.](entities-overview.md)

## <a name="additional-notes"></a>Notas adicionais

* As propriedades das Políticas chave `Datetime` de conteúdo que são do tipo estão sempre em formato UTC.
* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Utilize encriptação dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncriptado](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
