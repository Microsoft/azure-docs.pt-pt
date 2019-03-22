---
title: Transmissão em fluxo localizadores nos serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo fornece uma explicação sobre o que são os localizadores de transmissão em fluxo e como elas são usadas pelos serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 51aa33e4ff387a1030dac42bce8d12cf72343b35
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317481"
---
# <a name="streaming-locators"></a>Localizadores de Transmissão em Fluxo

Para fazer vídeos na saída Asset disponível para os clientes para a reprodução, tem de criar uma [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, crie URLs de transmissão em fluxo. Para obter exemplo de .NET, veja [obter um localizador de transmissão em fluxo](stream-files-tutorial-with-api.md#get-a-streaming-locator).

O processo de criação de um **localizador de transmissão em fluxo** é chamado de publicação. Por predefinição, o **localizador de transmissão em fluxo** é válido, imediatamente após fazer as chamadas à API e dura até serem eliminada, a menos que configure o início opcional e de horas de fim. 

Ao criar um **localizador de transmissão em fluxo**, tem de especificar o [Asset](https://docs.microsoft.com/rest/api/media/assets) nome e o [política de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingpolicies) nome. Pode utilizar uma das políticas predefinidas de transmissão em fluxo ou criado uma política personalizada. As políticas predefinidas atualmente disponíveis são: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'. Quando utilizar um personalizado transmissão em fluxo de política, deve criar um conjunto limitado dessas políticas para a sua conta de serviço de multimédia e reutilizá-los para os localizadores de transmissão em fluxo sempre que as mesmas opções e protocolos necessários. 

Se pretender especificar opções de encriptação no seu fluxo, crie o [política de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) que configura a forma como a chave de conteúdo é entregue ao fim de clientes via o componente de entrega de chave dos serviços de multimédia. Associar o localizador de transmissão em fluxo de mensagens em fila com o **política de chave de conteúdo** e a chave de conteúdo. Pode permitir que os serviços de multimédia para gerar automaticamente a chave. O exemplo de .NET seguinte mostra como configurar a encriptação AES com uma restrição de token em serviços de multimédia v3: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Políticas de chaves de conteúdo** são atualizável, poderá querer atualizar a política se tiver de fazer uma rotação de chaves. Pode demorar até 15 minutos para os caches de entrega de chave atualizar e escolher a política atualizada. É recomendável por não criar uma nova política de chave de conteúdo para cada localizador de transmissão em fluxo. Deve tentar reutilizar as políticas existentes, sempre que as mesmas opções são necessárias.

> [!IMPORTANT]
> * Propriedades de **localizadores de transmissão em fluxo** que são de Datetime tipo são sempre em formato UTC.
> * Deve criar um conjunto limitado de políticas para a sua conta de serviço de multimédia e reutilizá-los para os localizadores de transmissão em fluxo sempre que as mesmas opções são necessárias. 

## <a name="associate-filters-with-streaming-locators"></a>Associar filtros localizadores de transmissão em fluxo

Pode especificar uma lista de [filtros de conta ou asset](filters-concept.md), que seria se aplicam a seu [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). O [packager dinâmica](dynamic-packaging-overview.md) aplica-se esta lista de filtros em conjunto com o seu cliente especifica no URL. Esta combinação gera uma [dyanamic manifesto](filters-dynamic-manifest-overview.md), que se baseia em filtros no URL + filtros que especificar no localizador de transmissão em fluxo. Recomendamos que utilize esta funcionalidade se pretenda aplicar filtros, mas não pretende expor os nomes de filtro no URL.

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrar, ordem, entidades de localizador de transmissão em fluxo de página

Ver [filtragem, ordenação, a paginação de entidades de serviços de multimédia](entities-overview.md).

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Carregar, codificar e transmitir vídeos com .NET](stream-files-tutorial-with-api.md)
* [Utilizar DRM dinâmico licença e de encriptação de serviço de entrega](protect-with-drm.md)
