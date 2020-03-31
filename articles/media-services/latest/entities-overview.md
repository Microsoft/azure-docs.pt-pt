---
title: Filtragem, encomenda e paging de entidades dos Serviços de Media
titleSuffix: Azure Media Services
description: Conheça a filtragem, encomenda e apagões das entidades V3 da Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: bc5c983bc98c3b62df977c6765978cd45cd3c93b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500044"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtragem, encomenda e paging de entidades dos Serviços de Media

Este tópico discute as opções de consulta oOData e o suporte de paginação disponíveis quando está a listar as entidades V3 do Azure Media Services.

## <a name="considerations"></a>Considerações

* Propriedades de entidades do `Datetime` tipo estão sempre em formato UTC.
* O espaço branco na corda de consulta deve ser codificado por URL antes de enviar um pedido.

## <a name="comparison-operators"></a>Operadores de Comparação

Pode utilizar os seguintes operadores para comparar um campo com um valor constante:

Operadores de igualdade:

- `eq`: Teste se um campo é *igual a* um valor constante.
- `ne`: Teste se um campo não é *igual a* um valor constante.

Operadores de gama:

- `gt`: Teste se um campo é *maior do que* um valor constante.
- `lt`: Teste se um campo é *inferior* a um valor constante.
- `ge`: Teste se um campo é *maior ou igual a* um valor constante.
- `le`: Teste se um campo é *inferior ou igual a* um valor constante.

## <a name="filter"></a>Filtro

Utilize `$filter` para fornecer um parâmetro de filtro OData para encontrar apenas os objetos em que está interessado.

O exemplo REST seguinte `alternateId` filtra o valor de um ativo:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Os filtros de exemplo C# seguintes na data criada pelo ativo:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Ordem por

Utilize `$orderby` para separar os objetos devolvidos pelo parâmetro especificado. Por exemplo:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Para classificar os resultados em ordem ascendente `asc` `desc` ou descendente, anexar ou ao nome de campo, separado por um espaço. Por exemplo: `$orderby properties/created desc`.

## <a name="skip-token"></a>Token skip

Se uma resposta de consulta contiver muitos `$skiptoken` itens, o serviço devolve um (`@odata.nextLink`) valor que utiliza para obter a próxima página de resultados. Use-o para página ruminar todo o conjunto de resultados.

No Media Services v3, não é possível configurar o tamanho da página. O tamanho da página varia pelo tipo de entidade. Leia as secções individuais que se seguem para mais detalhes.

Se as entidades forem criadas ou eliminadas durante a recolha, as alterações refletem-se nos resultados devolvidos (se essas alterações estiverem na parte da coleção que não foi descarregada).

> [!TIP]
> Use `nextLink` sempre para enumerar a coleção e não dependa de um determinado tamanho da página.
>
> O `nextLink` valor só estará presente se houver mais de uma página de entidades.

Considere o seguinte `$skiptoken` exemplo de onde é usado. Certifique-se de que substitui a *amstestaccount* pelo nome da sua conta e detete o valor da *versão api* para a versão mais recente.

Se solicitar uma lista de bens como este:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Terá uma resposta semelhante a esta:

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

Em seguida, solicitaria a página seguinte enviando um pedido de pedido de:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

O exemplo c# seguinte mostra como enumerar todos os localizadores de streaming na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Usando operadores lógicos para combinar opções de consulta

Media Services v3 suporta **operadores de OR** e **e lógicos.** 

O exemplo do REST que se segue verifica o estado do trabalho:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Constrói a mesma consulta em C como esta: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opções de filtragem e encomenda de entidades

A tabela que se segue mostra como pode aplicar as opções de filtragem e encomenda a diferentes entidades:

|Nome da entidade|Nome da propriedade|Filtro|Encomenda|
|---|---|---|---|
|[Recursos](https://docs.microsoft.com/rest/api/media/assets/)|nome|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||propriedades.alternateId |`eq`||
||propriedades.assetId |`eq`||
||propriedades.criado| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Políticas-chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|nome|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.criado    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.descrição    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||propriedades.lastModificado|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.policyId|`eq`, `ne`||
|[Tarefas](https://docs.microsoft.com/rest/api/media/jobs)| nome  | `eq`            | `asc` e `desc`|
||propriedades.estado        | `eq`, `ne`        |                         |
||propriedades.criado      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
||propriedades.lastModificado | `gt`, `ge`, `lt`, `le` | `asc` e `desc`| 
|[Localizadores de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators)|nome|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.criado    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` e `desc`|
||propriedades.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Políticas de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)|nome|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||propriedades.criado    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Transformações](https://docs.microsoft.com/rest/api/media/transforms)| nome | `eq`            | `asc` e `desc`|
|| propriedades.criado      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
|| propriedades.lastModificado | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|

## <a name="next-steps"></a>Passos seguintes

* [Ativos da lista](https://docs.microsoft.com/rest/api/media/assets/list)
* [Lista de Políticas-chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Empregos de Lista](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Lista de Políticas de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Lista de localizadores de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Quotas e limitações](limits-quotas-constraints.md)
