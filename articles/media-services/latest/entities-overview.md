---
title: Filtragem, ordenação e paginação de entidades de serviços de multimédia - Azure | Documentos da Microsoft
description: Este artigo aborda a filtragem, ordenação e paginação de entidades de serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 28c880e8709074d808a41d9920361eaa2b20ecc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60732370"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, ordenação, paginação de entidades de serviços de multimédia

Serviços de multimédia suportam as seguintes opções de consulta de OData para entidades de v3 de serviços de multimédia: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Descrição do operador:

* EQ = igual a
* Ne = não igual a
* A ge = maior que ou igual a
* Le = menor ou igual a
* Gt = maior que
* Lt = menor que

Propriedades das entidades que são do tipo Datetime são sempre em formato UTC.

## <a name="page-results"></a>Resultados de página

Se uma resposta de consulta contém o número de itens, o serviço retornará um "\@OData. NextLink" propriedade para obter a próxima página de resultados. Isto pode ser utilizado para a página usando o conjunto de resultados inteiro. Não é possível configurar o tamanho da página. O tamanho da página varia consoante o tipo de entidade, leia as secções individuais que se seguem para obter detalhes.

Se estiver a entidades criadas ou eliminadas enquanto a paginação por meio da coleção, as alterações são refletidas em resultados devolvidos (se essas alterações na parte da coleção que não foi transferida). 

> [!TIP]
> Deve sempre utilizar a ligação seguinte enumere a coleção e não dependem de um tamanho de página em particular.

## <a name="assets"></a>Elementos

### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como a filtragem e ordenação opções podem ser aplicados para o [Asset](https://docs.microsoft.com/rest/api/media/assets) propriedades: 

|Name|Filtro|Encomenda|
|---|---|---|
|ID|||
|name|eq, gt, lt| Ascendente e descendente|
|properties.alternateId |eq||
|properties.assetId |eq||
|Properties.Container |||
|Properties.created| eq, gt, lt| Ascendente e descendente|
|Properties.Description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|tipo|||

O exemplo seguinte c# filtra na data de criação:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Paginação 

Paginação é suportada para cada uma das quatro ordens de classificação ativado. Atualmente, o tamanho da página é 1000.

#### <a name="c-example"></a>Exemplo do c#

O exemplo do c# seguinte mostra como a enumeração por meio de todos os recursos na conta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Exemplo do REST

Considere o seguinte exemplo de onde $skiptoken é utilizado. Certifique-se de que substitua *amstestaccount* com o nome da conta e o conjunto a *api-version* valor para a versão mais recente.

Se solicitar uma lista de recursos, como este:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Obteria volta uma resposta semelhante ao seguinte:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Em seguida, poderia solicitar a página seguinte, enviando uma solicitação get:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Para obter mais exemplos REST, consulte [ativos - lista](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="content-key-policies"></a>Políticas de Chave de Conteúdo

### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas para o [diretivas de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) propriedades: 

|Name|Filtro|Encomenda|
|---|---|---|
|ID|||
|name|eq, ne, ge, le, gt, lt|Ascendente e descendente|
|Properties.created |eq, ne, ge, le,  gt, lt|Ascendente e descendente|
|Properties.Description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|Ascendente e descendente|
|Properties.Options |||
|properties.policyId|eq, ne||
|type|||

### <a name="pagination"></a>Paginação

Paginação é suportada para cada uma das quatro ordens de classificação ativado. Atualmente, o tamanho da página é 10.

O seguinte C# exemplo mostra como a enumeração por meio de tudo **diretivas de chave de conteúdo** na conta.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Para obter exemplos REST, consulte [diretivas de chave de conteúdo - lista](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="jobs"></a>Tarefas

### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas para o [tarefas](https://docs.microsoft.com/rest/api/media/jobs) propriedades: 

| Name    | Filtro                        | Encomenda |
|---------|-------------------------------|-------|
| name                    | eq            | Ascendente e descendente|
| properties.state        | eq, ne        |                         |
| Properties.created      | gt, ge, lt, le| Ascendente e descendente|
| properties.lastModified | gt, ge, lt, le | Ascendente e descendente| 

### <a name="pagination"></a>Paginação

Paginação de tarefas é suportada em serviços de multimédia v3.

O seguinte C# exemplo mostra como a enumeração por meio de tarefas na conta.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Para obter exemplos REST, consulte [tarefas - lista](https://docs.microsoft.com/rest/api/media/jobs/list)

## <a name="streaming-locators"></a>Localizadores de Transmissão em Fluxo

### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas às propriedades StreamingLocator: 

|Name|Filtro|Encomenda|
|---|---|---|
|ID |||
|name|eq, ne, ge, le, gt, lt|Ascendente e descendente|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|Properties.created |eq, ne, ge, le,  gt, lt|Ascendente e descendente|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|Ascendente e descendente|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>Paginação

Paginação é suportada para cada uma das quatro ordens de classificação ativado. Atualmente, o tamanho da página é 10.

O exemplo do c# seguinte mostra como a enumeração por meio de todos os StreamingLocators na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

Para obter exemplos REST, consulte [localizadores de transmissão em fluxo - lista](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

## <a name="streaming-policies"></a>Políticas de Transmissão em Fluxo

### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas às propriedades StreamingPolicy: 

|Nome|Filtro|Encomenda|
|---|---|---|
|ID|||
|name|eq, ne, ge, le, gt, lt|Ascendente e descendente|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|Properties.created |eq, ne, ge, le,  gt, lt|Ascendente e descendente|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>Paginação

Paginação é suportada para cada uma das quatro ordens de classificação ativado. Atualmente, o tamanho da página é 10.

O exemplo do c# seguinte mostra como a enumeração por meio de todos os StreamingPolicies na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Para obter exemplos REST, consulte [políticas de transmissão em fluxo - lista](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="transform"></a>Transformação

### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas para o [transforma](https://docs.microsoft.com/rest/api/media/transforms) propriedades: 

| Name    | Filtro                        | Encomenda |
|---------|-------------------------------|-------|
| name                    | eq            | Ascendente e descendente|
| Properties.created      | gt, ge, lt, le| Ascendente e descendente|
| properties.lastModified | gt, ge, lt, le | Ascendente e descendente|

## <a name="next-steps"></a>Passos Seguintes

[Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
