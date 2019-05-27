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
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120342"
---
# <a name="streaming-locators"></a>Localizadores de Transmissão em Fluxo

Para fazer vídeos no Recurso de saída disponível para a reprodução em clientes, tem de criar um [Localizador de Transmissões em Fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, criar os URLs das transmissões. Para um exemplo de .NET, veja [Get a Streaming Locator](stream-files-tutorial-with-api.md#get-a-streaming-locator) (Obter um Localizador de Transmissão em Fluxo).

O processo de criação de um **localizador de transmissão em fluxo** é chamado de publicação. Por predefinição, o **localizador de transmissão em fluxo** é válido, imediatamente após fazer as chamadas à API e dura até serem eliminada, a menos que configure o início opcional e de horas de fim. 

Ao criar um **localizador de transmissão em fluxo**, tem de especificar um **Asset** nome e um **política de transmissão em fluxo** nome. Para obter mais informações, consulte os seguintes tópicos:

* [Ativos](assets-concept.md)
* [Streaming Policies](streaming-policy-concept.md) (Políticas de Transmissão em Fluxo)
* [Content Key Policies](content-key-policy-concept.md) (Políticas de Chaves de Conteúdos)

> [!IMPORTANT]
> * Propriedades de **localizadores de transmissão em fluxo** que são de Datetime tipo são sempre em formato UTC.
> * Deve criar um conjunto limitado de políticas para a sua conta de serviço de multimédia e reutilizá-los para os localizadores de transmissão em fluxo sempre que as mesmas opções são necessárias. Para obter mais informações, consulte [Quotas e limitações](limits-quotas-constraints.md).

## <a name="associate-filters-with-streaming-locators"></a>Associar filtros localizadores de transmissão em fluxo

Ver [filtros: associar às localizadores de transmissão em fluxo](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrar, ordem, entidades de localizador de transmissão em fluxo de página

Ver [filtragem, ordenação, a paginação de entidades de serviços de multimédia](entities-overview.md).

## <a name="next-steps"></a>Passos Seguintes

[Tutorial: Upload, encode, and stream videos using .NET](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir vídeos em fluxo com .NET)
