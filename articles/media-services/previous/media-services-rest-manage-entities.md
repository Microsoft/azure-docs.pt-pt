---
title: Gerenciando entidades de serviços de mídia com REST | Microsoft Docs
description: Este artigo demonstra como gerenciar entidades de serviços de mídia com a API REST.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 1ecbca99a65d99cc3b8d842a7b61d858398a80ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885709"
---
# <a name="managing-media-services-entities-with-rest"></a>Gerenciando entidades de serviços de mídia com REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Serviços de Mídia do Microsoft Azure é um serviço baseado em REST criado no OData v3. Você pode adicionar, consultar, atualizar e excluir entidades praticamente da mesma forma que você pode em qualquer outro serviço OData. As exceções serão chamadas quando aplicável. Para obter mais informações sobre o OData, consulte a [documentação do protocolo Open Data](https://www.odata.org/documentation/).

Este tópico mostra como gerenciar entidades dos serviços de mídia do Azure com REST.

>[!NOTE]
> A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Por exemplo, em 1º de abril de 2017, qualquer registro de trabalho em sua conta com mais de 31 de dezembro de 2016 será excluído automaticamente. Se você precisar arquivar as informações de trabalho/tarefa, poderá usar o código descrito neste tópico.

## <a name="considerations"></a>Considerações  

Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos em suas solicitações HTTP. Para obter mais informações, consulte [configuração para desenvolvimento da API REST dos serviços de mídia](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligue-se aos Serviços Multimédia

Para obter informações sobre como se conectar à API do AMS, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Adicionando entidades
Cada entidade nos serviços de mídia é adicionada a um conjunto de entidades, como ativos, por meio de uma solicitação HTTP POST.

O exemplo a seguir mostra como criar um AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Consultando entidades
Consultar e listar entidades é simples e envolve apenas uma solicitação HTTP GET e operações OData opcionais.
O exemplo a seguir recupera uma lista de todas as entidades MediaProcessor.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Você também pode recuperar uma entidade específica ou todos os conjuntos de entidades associados a uma entidade específica, como nos exemplos a seguir:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

O exemplo a seguir retorna apenas a propriedade State de todos os trabalhos.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

O exemplo a seguir retorna todos os os jobtemplates com o nome "Sampletemplate".

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> Não há suporte para a operação de $expand nos serviços de mídia, bem como os métodos LINQ sem suporte descritos em considerações de LINQ (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerando por meio de grandes coleções de entidades
Ao consultar entidades, há um limite de 1000 entidades retornadas ao mesmo tempo porque o REST público v2 limita os resultados da consulta a 1000 resultados. Use **Skip** e **Top** para enumerar por meio da grande coleção de entidades. 

O exemplo a seguir mostra como usar **Skip** e **Top** para ignorar os primeiros 2000 trabalhos e obter os próximos 1000 trabalhos.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Atualizando entidades
Dependendo do tipo de entidade e do estado em que ele está, você pode atualizar as propriedades dessa entidade por meio de solicitações HTTP de PATCH, PUT ou MERGE. Para obter mais informações sobre essas operações, consulte [patch/Put/Merge](https://msdn.microsoft.com/library/dd541276.aspx).

O exemplo de código a seguir mostra como atualizar a propriedade Name em uma entidade de ativo.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Excluindo entidades
As entidades podem ser excluídas nos serviços de mídia usando uma solicitação HTTP DELETE. Dependendo da entidade, a ordem na qual você exclui entidades pode ser importante. Por exemplo, entidades como ativos exigem que você revogue (ou exclua) todos os localizadores que fazem referência a esse ativo específico antes de excluir o ativo.

O exemplo a seguir mostra como excluir um localizador que foi usado para carregar um arquivo no armazenamento de BLOBs.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

