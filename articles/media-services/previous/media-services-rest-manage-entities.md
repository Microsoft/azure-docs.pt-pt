---
title: Gerenciando entidades de serviços de multimédia com REST | Documentos da Microsoft
description: Saiba como gerir as entidades de serviços de multimédia com a REST API.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: ff7bb6fa087ab7fa362c13ca03d6f3100a505ce3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858962"
---
# <a name="managing-media-services-entities-with-rest"></a>Gerenciando entidades de serviços de multimédia com REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Serviços de multimédia do Microsoft Azure é um serviço baseado em REST, criado sobre OData v3. Pode adicionar, consultar, atualizar e eliminar entidades praticamente da mesma forma como pode, em qualquer outro serviço OData. Exceções serão destacadas quando aplicável. Para obter mais informações sobre OData, veja [documentação Open Data Protocol](https://www.odata.org/documentation/).

Este tópico mostra-lhe como gerir entidades de serviços de multimédia do Azure com REST.

>[!NOTE]
> A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Por exemplo, 1 de Abril de 2017, qualquer registo de tarefa na sua conta com mais de 31 de Dezembro de 2016, serão automaticamente eliminado. Se precisar de arquivar as informações de tarefas, pode usar o código descrito neste tópico.

## <a name="considerations"></a>Considerações  

Ao aceder a entidades nos serviços de multimédia, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Adicionar entidades
Cada entidade nos serviços de multimédia é adicionada a um conjunto de entidades, como ativos, através de um pedido de POST HTTP.

O exemplo seguinte mostra como criar um AccessPolicy.

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

## <a name="querying-entities"></a>Consultar entidades
Consultar e entidades de listagem é simples e envolve apenas uma solicitação GET HTTP e as operações de OData opcionais.
O exemplo seguinte obtém uma lista de todas as entidades de MediaProcessor.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Também pode obter uma entidade específica ou todos os conjuntos de entidades associados uma entidade específica, tal como nos exemplos a seguir:

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

O exemplo seguinte devolve apenas a propriedade de estado de todas as tarefas.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

O exemplo seguinte devolve todas as JobTemplates com o nome "SampleTemplate."

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> O $expand operação não é suportada em serviços de multimédia, bem como os métodos LINQ não suportados, descritos em considerações de LINQ (Serviços de dados do WCF).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerando pelas grandes coleções de entidades
Ao consultar entidades, existe um limite de 1000 entidades retornadas, ao mesmo tempo, porque a v2 REST pública limita os resultados da consulta para resultados de 1000. Uso **ignore** e **superior** a enumeração por meio de grande coleção de entidades. 

O exemplo seguinte mostra como usar **ignore** e **superior** para ignorar as tarefas de primeiro 2000 e obter as tarefas a seguir 1000.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>A atualização de entidades
Consoante o tipo de entidade e o estado que está a ser, pode atualizar propriedades nessa entidade por meio de um PATCH, pedidos PUT ou intercalar HTTP. Para obter mais informações sobre estas operações, consulte [PATCH/PUT/intercalação](https://msdn.microsoft.com/library/dd541276.aspx).

O exemplo de código seguinte mostra como atualizar a propriedade de nome numa entidade de recurso.

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

## <a name="deleting-entities"></a>A eliminar entidades
Entidades podem ser eliminadas nos serviços de multimédia através de um pedido de HTTP de eliminar. Dependendo da entidade, a ordem na qual excluir entidades pode ser importante. Por exemplo, entidades como recursos requerem que revogar (ou eliminar) todos os localizadores que fazem referência a esse recurso em questão antes de eliminar o recurso.

O exemplo seguinte mostra como eliminar um localizador de que foi utilizado para carregar um ficheiro no armazenamento de Blobs.

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

