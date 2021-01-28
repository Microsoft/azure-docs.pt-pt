---
title: Filtragem, encomenda e paging de entidades
description: Saiba mais sobre filtragem, encomenda e paging de entidades V3 da Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 19febc833f087afdfecde3274a1044598d082521
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955314"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtragem, encomenda e paging de entidades de Serviços de Comunicação Social

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tópico discute as opções de consulta oD e suporte de paginação disponível quando está a listar as entidades Azure Media Services v3.

## <a name="considerations"></a>Considerações

* Os imóveis de entidades do `Datetime` tipo estão sempre em formato UTC.
* O espaço branco na cadeia de consulta deve ser codificado por URL antes de enviar um pedido.

## <a name="comparison-operators"></a>Operadores de comparação

Pode utilizar os seguintes operadores para comparar um campo com um valor constante:

Operadores para a igualdade:

- `eq`: Teste se um campo é *igual a* um valor constante.
- `ne`: Teste se um campo não é *igual a* um valor constante.

Operadores de gama:

- `gt`: Teste se um campo é *maior do que* um valor constante.
- `lt`: Teste se um campo é *inferior a* um valor constante.
- `ge`: Teste se um campo é *maior ou igual a* um valor constante.
- `le`: Teste se um campo é *inferior ou igual a* um valor constante.

## <a name="filter"></a>Filtro

Utilize `$filter` para fornecer um parâmetro de filtro OData para encontrar apenas os objetos em que está interessado.

O exemplo REST seguinte filtra o `alternateId` valor de um ativo:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Os seguintes filtros de exemplo C# na data criada pelo ativo:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Encomenda por

Utilize `$orderby` para separar os objetos devolvidos pelo parâmetro especificado. Por exemplo:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Para classificar os resultados em ordem ascendente ou descendente, apendam-se `asc` ou ao nome do `desc` campo, separados por um espaço. Por exemplo: `$orderby properties/created desc`.

## <a name="skip-token"></a>Saltar token

Se uma resposta de consulta contiver muitos itens, o serviço devolve um `$skiptoken` `@odata.nextLink` valor () que utiliza para obter a próxima página de resultados. Utilize-o para páginar em todo o conjunto de resultados.

No Media Services v3, não é possível configurar o tamanho da página. O tamanho da página varia em consoam em comparação com o tipo de entidade. Leia as secções individuais que seguem para mais detalhes.

Se as entidades forem criadas ou eliminadas enquanto estiver a fazer o paging através da coleção, as alterações refletem-se nos resultados devolvidos (se essas alterações estiverem na parte da coleção que não foi descarregada).

> [!TIP]
> Use sempre `nextLink` para enumerar a coleção e não dependa de um determinado tamanho da página.
>
> O `nextLink` valor só estará presente se houver mais de uma página de entidades.

Considere o seguinte exemplo de onde `$skiptoken` é usado. Certifique-se de que substitui *a conta amstest* pelo nome da sua conta e desconte o valor da *versão API* para a versão mais recente.

Se solicitar uma lista de ativos como este:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Receberá uma resposta semelhante a esta:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Em seguida, solicitaria a página seguinte enviando um pedido de:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

O exemplo C# a seguir mostra como enumerar através de todos os localizadores de streaming na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Usando operadores lógicos para combinar opções de consulta

A Media Services v3 suporta operadores **OR** e **AND** lógicos. 

O seguinte exemplo DE REST verifica o estado do trabalho:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Você constrói a mesma consulta em C# como esta: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opções de filtragem e encomenda de entidades

A tabela a seguir mostra como pode aplicar as opções de filtragem e encomenda a diferentes entidades:

|Nome da entidade|Nome da propriedade|Filtro|Encomenda|
|---|---|---|---|
|[Recursos](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||propriedades.alternateId |`eq`||
||propriedades.assetId |`eq`||
||propriedades.criadas| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Políticas de chave de conteúdo](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.criadas    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.descrição    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||propriedades.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.policyId|`eq`, `ne`||
|[Trabalhos](/rest/api/media/jobs)| name  | `eq`            | `asc` e `desc`|
||propriedades.estado        | `eq`, `ne`        |                         |
||propriedades.criadas      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
||propriedades.lastModified | `gt`, `ge`, `lt`, `le` | `asc` e `desc`| 
|[Localizadores de transmissão em fluxo](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.criadas    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` e `desc`|
||propriedades.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Políticas de transmissão em fluxo](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.criadas    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Transformações](/rest/api/media/transforms)| name | `eq`            | `asc` e `desc`|
|| propriedades.criadas      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
|| propriedades.lastModified | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|

## <a name="next-steps"></a>Passos seguintes

* [Listar Ativos](/rest/api/media/assets/list)
* [Políticas chave de conteúdo da lista](/rest/api/media/contentkeypolicies/list)
* [Empregos de Lista](/rest/api/media/jobs/list)
* [Políticas de streaming de listas](/rest/api/media/streamingpolicies/list)
* [Localizadores de streaming de listas](/rest/api/media/streaminglocators/list)
* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Quotas e limites](limits-quotas-constraints.md)
