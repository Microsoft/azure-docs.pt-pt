---
title: Gestão de Entidades de Serviços de Media com REST / Microsoft Docs
description: Este artigo demonstra como gerir entidades de Serviços de Media com a REST API.
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
ms.openlocfilehash: a03bc24b689df342be40536c26149a7611fc5176
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283319"
---
# <a name="managing-media-services-entities-with-rest"></a>Gerir entidades de Serviços de Media com REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

O Microsoft Azure Media Services é um serviço baseado em REST construído no OData v3. Pode adicionar, consultar, atualizar e eliminar entidades da mesma forma que pode em qualquer outro serviço OData. As exceções serão convocadas quando aplicável. Para mais informações sobre o OData, consulte documentação do Protocolo de [Dados Abertos.](https://www.odata.org/documentation/)

Este tópico mostra-lhe como gerir entidades da Azure Media Services com o REST.

>[!NOTE]
> A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Por exemplo, no dia 1 de abril de 2017, qualquer registo de Emprego na sua conta com mais de 31 de dezembro de 2016 será automaticamente eliminado. Se precisar de arquivar as informações de trabalho/tarefa, pode utilizar o código descrito neste tópico.

## <a name="considerations"></a>Considerações  

Ao aceder a entidades em Serviços de Media, deve definir campos e valores específicos nos seus pedidos HTTP. Para mais informações, consulte [Configuração para Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como se conectar à AMS API, consulte [Aceda à API dos Serviços de Mídia Azure com autenticação Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Adicionar entidades
Todas as entidades dos Serviços de Media são adicionadas a um conjunto de entidades, como ativos, através de um pedido POST HTTP.

O exemplo que se segue mostra como criar uma Política de Acesso.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Entidades de consulta
As entidades de consulta e listagem são simples e envolvem apenas um pedido GET HTTP e operações opcionais de OData.
O exemplo seguinte recupera uma lista de todas as entidades do MediaProcessor.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Também pode recuperar uma entidade específica ou todos os conjuntos de entidades associados a uma entidade específica, como nos seguintes exemplos:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

O exemplo seguinte devolve apenas a propriedade do Estado de todos os Empregos.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

O exemplo seguinte devolve todos os Modelos de Trabalho com o nome "SampleTemplate".

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> A operação $expand não é suportada nos Serviços de Media, bem como nos métodos LINQ não suportados descritos em considerações LINQ (Serviços de Dados wCF).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerando através de grandes coleções de entidades
Quando consultam entidades, há um limite de 1000 entidades devolvidas ao mesmo tempo porque o REST v2 público limita os resultados da consulta a 1000 resultados. Use **skip** e **top** para enumerar através da grande coleção de entidades. 

O exemplo que se segue mostra como usar **o skip** e **o top** para saltar os primeiros 2000 postos de trabalho e conseguir os próximos 1000 postos de trabalho.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Entidades de atualização
Dependendo do tipo de entidade e do estado em que se encontra, pode atualizar propriedades dessa entidade através de um patch, PUT ou merge HTTP pedidos. Para obter mais informações sobre estas operações, consulte [PATCH/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

O exemplo de código que se segue mostra como atualizar a propriedade Nome numa entidade de Ativo.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Entidades de desisminagem
As entidades podem ser eliminadas nos Serviços de Comunicação Social através de um pedido DELETE HTTP. Dependendo da entidade, a ordem em que elimina as entidades pode ser importante. Por exemplo, entidades como o Ativo exigem que revogue (ou elimine) todos os Locators que referenciam esse Ativo específico antes de eliminar o Ativo.

O exemplo que se segue mostra como eliminar um Localizador que foi usado para carregar um ficheiro no armazenamento de bolhas.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

