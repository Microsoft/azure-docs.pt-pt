---
title: Entidades de Gestão de Serviços de Mídia com | REST Microsoft Docs
description: Este artigo demonstra como gerir entidades de Serviços de Mídia com a REST API.
author: IngridAtMicrosoft
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2821c4c2b13edaf5dfb34664c4b9f78ab56d98a7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015561"
---
# <a name="managing-media-services-entities-with-rest"></a>Entidades de Gestão de Serviços de Mídia com REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services é um serviço baseado em REST construído em OData v3. Pode adicionar, consultar, atualizar e eliminar as entidades da mesma forma que possível em qualquer outro serviço OData. As exceções serão convocadas quando aplicável. Para obter mais informações sobre o OData, consulte [a documentação do Protocolo de Dados Abertos](https://www.odata.org/documentation/).

Este tópico mostra-lhe como gerir entidades da Azure Media Services com a REST.

>[!NOTE]
> A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Por exemplo, no dia 1 de abril de 2017, qualquer registo de Emprego na sua conta com mais de 31 de dezembro de 2016 será automaticamente eliminado. Se precisar de arquivar as informações de trabalho/tarefa, pode utilizar o código descrito neste tópico.

## <a name="considerations"></a>Considerações  

Ao aceder a entidades nos Serviços de Media, deve definir campos e valores específicos de cabeçalho nos seus pedidos HTTP. Para obter mais informações, consulte [Configuração para Serviços de Mídia REST Desenvolvimento da API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [Aceda à API dos Serviços de Media Azure com autenticação AD Azure](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Adicionar entidades
Todas as entidades dos Serviços de Media são adicionadas a um conjunto de entidades, como o Ativo, através de um pedido DE HTTP.

O exemplo a seguir mostra como criar uma Política de Acesso.

```console
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
```

## <a name="querying-entities"></a>Entidades de consulta
As entidades de consulta e listagem são simples e envolvem apenas um pedido GET HTTP e operações oD OData opcionais.
O exemplo a seguir recupera uma lista de todas as entidades do MediaProcessor.

```console
GET https://media.windows.net/API/MediaProcessors HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Também pode recuperar uma entidade específica ou todos os conjuntos de entidades associados a uma entidade específica, como nos seguintes exemplos:

```console
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
```

O exemplo a seguir devolve apenas a propriedade do Estado de todos os Empregos.

```console
GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

O exemplo a seguir devolve todos os JobTemplates com o nome "SampleTemplate".

```console
GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

> [!NOTE]
> A operação $expand não é suportada nos Serviços de Comunicação Social, bem como nos métodos LINQ não suportados descritos em LinQ Considerations (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Enumeração através de grandes coleções de entidades
Ao consultar entidades, há um limite de 1000 entidades devolvidas de uma só vez porque o REST v2 público limita os resultados da consulta a 1000 resultados. Use **o skip** e o **topo** para enumerar através da grande coleção de entidades. 

O exemplo que se segue mostra como usar **o skip** and **top** para saltar os primeiros 2000 postos de trabalho e conseguir os próximos 1000 postos de trabalho.  

```console
GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
```

## <a name="updating-entities"></a>Atualizar entidades
Dependendo do tipo de entidade e do estado em que se encontra, pode atualizar propriedades nessa entidade através de pedidos PATCH, PUT ou MERGE HTTP. Para obter mais informações sobre estas operações, consulte [PATCH/PUT/MERGE](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893).

O exemplo de código que se segue mostra como atualizar a propriedade Nome numa entidade Do Ativo.

```console
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
```

## <a name="deleting-entities"></a>Exclusão de entidades
As entidades podem ser eliminadas nos Serviços de Comunicação Social utilizando um pedido DELETE HTTP. Dependendo da entidade, a ordem pela qual elimina entidades pode ser importante. Por exemplo, entidades como o Ativo exigem que revogue (ou elimine) todos os Localizadores que referenciam esse Ativo em particular antes de eliminar o Ativo.

O exemplo a seguir mostra como eliminar um Localizador que foi usado para carregar um ficheiro para o armazenamento de bolhas.

```console
DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 0
```

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
