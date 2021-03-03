---
title: Políticas-chave de conteúdo em serviços de mídia - Azure
description: Este artigo dá uma explicação sobre quais são as Políticas Chave de Conteúdo e como são usadas pela Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 1333bbcc7e2ea9219d28fcc1e63f21a833c2bc6f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709568"
---
# <a name="content-key-policies"></a>Políticas de Chave de Conteúdo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Com os Media Services, pode fornecer o seu conteúdo ao vivo e a pedido encriptado dinamicamente com o Advanced Encryption Standard (AES-128) ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Media Services também fornecem um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. 

Para especificar opções de encriptação no seu fluxo, precisa criar uma [Política de Streaming](streaming-policy-concept.md) e associá-la ao seu [Localizador de Streaming.](streaming-locators-concept.md) Cria a [Política de Chave de Conteúdo](/rest/api/media/contentkeypolicies) para configurar a forma como a chave de conteúdo (que fornece acesso seguro aos seus [Ativos)](assets-concept.md)é entregue aos clientes finais. É necessário definir os requisitos (restrições) sobre a Política de Chave de Conteúdo que devem ser cumpridos para que as chaves com a configuração especificada sejam entregues aos clientes. A política-chave de conteúdo não é necessária para um streaming claro ou para o download. 

Normalmente, associa a sua política de chave de conteúdo ao seu [Localizador de Streaming.](streaming-locators-concept.md) Em alternativa, pode especificar a política de chave de conteúdo dentro de uma [Política de Streaming](streaming-policy-concept.md) (ao criar uma política de streaming personalizada para cenários avançados). 

## <a name="best-practices-and-considerations"></a>Melhores práticas e considerações

> [!IMPORTANT]
> Por favor, reveja as seguintes recomendações.

* Deve conceber um conjunto limitado de políticas para a sua conta Media Service e reutilizá-las para os seus localizadores de streaming sempre que forem necessárias as mesmas opções. Para mais informações, consulte [Quotas e limites.](limits-quotas-constraints.md)
* As políticas-chave de conteúdo são exequíveis. Pode levar até 15 minutos para que os caches de entrega das chaves atualizem e apanhem a política atualizada. 

   Ao atualizar a política, está a sobrepor-se à cache cdN existente, o que pode causar problemas de reprodução para clientes que estão a usar conteúdo em cache.  
* Recomendamos que não crie uma nova política de chave de conteúdo para cada ativo. Os principais benefícios da partilha da mesma política de conteúdo entre ativos que necessitam das mesmas opções políticas são:
   
   * É mais fácil gerir um pequeno número de políticas.
   * Se precisar de fazer atualizações para a política de chave de conteúdo, as alterações entram em vigor em todos os novos pedidos de licença quase imediatamente.
* Se precisar de criar uma nova política, tem de criar um novo localizador de streaming para o ativo.
* Recomenda-se que os Serviços de Comunicação Social autogeram a chave de conteúdo. 

   Normalmente, usaria uma chave de longa duração e verificaria a existência da política de chave de conteúdo com [a Get](/rest/api/media/contentkeypolicies/get). Para obter a chave, você precisa chamar um método de ação separado para obter segredos ou credenciais, veja o exemplo que se segue.

## <a name="example"></a>Exemplo

Para chegar à chave, use `GetPolicyPropertiesWithSecretsAsync` , como mostrado na Chave Obter uma chave de assinatura do exemplo de política [existente.](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)

## <a name="filtering-ordering-paging"></a>Filtragem, encomenda, paging

Ver [Filtragem, encomenda, paging de entidades de Serviços de Comunicação](entities-overview.md)Social.

## <a name="additional-notes"></a>Notas adicionais

* As propriedades das Políticas chave de conteúdo que são do `Datetime` tipo estão sempre em formato UTC.
* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Utilize encriptação dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
* [Encriptação básica da chave AES e código de amostra de streaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
