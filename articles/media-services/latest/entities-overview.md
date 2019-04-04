---
title: Programar com v3 APIs - Azure | Documentos da Microsoft
description: Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com serviços de multimédia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a5ab0b25a2a2db764854982b1a6801ce4f857dda
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891961"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolvimento com os serviços de multimédia de v3 APIs

Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com serviços de multimédia v3.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos. 

Os nomes de recursos dos Serviços de Multimédia não podem incluir: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", a plica ou qualquer caráter de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres. 

Para obter mais informações sobre a nomenclatura do Azure Resource Manager, consulte: [Requisitos de nomes](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>princípios de design de API v3

Um dos principais princípios de design da API v3 é tornar a API mais segura. As APIs v3 não devolvem segredos ou credenciais sobre uma operação **Get** ou **List**. As chaves são sempre nulas, vazias ou saneadas da resposta. Tem de chamar um método de ação separado para obter segredos ou credenciais. As ações separadas permitem-lhe definir diferentes permissões de segurança RBAC no caso de algumas APIs obterem/mostrarem segredos e outras não. Para obter informações sobre como gerir o acesso através do RBAC, consulte [Utilizar o RBAC para gerir acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Exemplos de incluem:

* Não devolve valores ContentKey em Get do StreamingLocator.
* Não devolve as chaves de restrição em Get do ContentKeyPolicy.
* Não, retornando a parte da cadeia de caracteres de consulta do URL (para remover a assinatura) de URLs de entrada de HTTP das tarefas.

Consulte a [obter a política de chave conteúda - .NET](get-content-key-policy-dotnet-howto.md) exemplo.

## <a name="long-running-operations"></a>Operações de longa execução

As operações marcadas com `x-ms-long-running-operation` nos serviços de multimédia do Azure [swagger ficheiros](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) são longos em execução de operações. 

Para obter detalhes sobre como controlar as operações assíncronas do Azure, consulte [operações assíncronas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Serviços de multimédia têm as seguintes operações de longa execução:

* Criar LiveEvent
* Atualizar LiveEvent
* Delete LiveEvent
* Iniciar LiveEvent
* Parar LiveEvent
* Reset LiveEvent
* Criar LiveOutput
* Delete LiveOutput
* Criar StreamingEndpoint
* Atualizar StreamingEndpoint
* Eliminar StreamingEndpoint
* Iniciar StreamingEndpoint
* Parar StreamingEndpoint
* Dimensionamento StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, ordenação, paginação de entidades de serviços de multimédia

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

### <a name="page-results"></a>Resultados de página

Se uma resposta de consulta contém o número de itens, o serviço retornará um "\@OData. NextLink" propriedade para obter a próxima página de resultados. Isto pode ser utilizado para a página usando o conjunto de resultados inteiro. Não é possível configurar o tamanho da página. O tamanho da página varia consoante o tipo de entidade, leia as secções individuais que se seguem para obter detalhes.

Se estiver a entidades criadas ou eliminadas enquanto a paginação por meio da coleção, as alterações são refletidas em resultados devolvidos (se essas alterações na parte da coleção que não foi transferida). 

> [!TIP]
> Deve sempre utilizar a ligação seguinte enumere a coleção e não dependem de um tamanho de página em particular.

### <a name="assets"></a>Elementos

#### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como a filtragem e ordenação opções podem ser aplicados para o [Asset](https://docs.microsoft.com/rest/api/media/assets) propriedades: 

|Name|Filtro|Encomenda|
|---|---|---|
|ID|||
|nome|eq, gt, lt| Ascendente e descendente|
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

#### <a name="pagination"></a>Paginação 

Paginação é suportada para cada uma das quatro ordens de classificação ativado. Atualmente, o tamanho da página é 1000.

##### <a name="c-example"></a>Exemplo do c#

O exemplo do c# seguinte mostra como a enumeração por meio de todos os recursos na conta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>Exemplo do REST

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

### <a name="content-key-policies"></a>Políticas de Chave de Conteúdo

#### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas para o [diretivas de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) propriedades: 

|Name|Filtro|Encomenda|
|---|---|---|
|ID|||
|nome|eq, ne, ge, le, gt, lt|Ascendente e descendente|
|Properties.created |eq, ne, ge, le,  gt, lt|Ascendente e descendente|
|Properties.Description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|Ascendente e descendente|
|Properties.Options |||
|properties.policyId|eq, ne||
|tipo|||

#### <a name="pagination"></a>Paginação

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

### <a name="jobs"></a>Tarefas

#### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas para o [tarefas](https://docs.microsoft.com/rest/api/media/jobs) propriedades: 

| Name    | Filtro                        | Encomenda |
|---------|-------------------------------|-------|
| nome                    | eq            | Ascendente e descendente|
| properties.state        | eq, ne        |                         |
| Properties.created      | gt, ge, lt, le| Ascendente e descendente|
| properties.lastModified | gt, ge, lt, le | Ascendente e descendente| 


#### <a name="pagination"></a>Paginação

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

### <a name="streaming-locators"></a>Localizadores de Transmissão em Fluxo

#### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas às propriedades StreamingLocator: 

|Name|Filtro|Encomenda|
|---|---|---|
|ID |||
|nome|eq, ne, ge, le, gt, lt|Ascendente e descendente|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|Properties.created |eq, ne, ge, le,  gt, lt|Ascendente e descendente|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|Ascendente e descendente|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|tipo   |||

#### <a name="pagination"></a>Paginação

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

### <a name="streaming-policies"></a>Políticas de Transmissão em Fluxo

#### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas às propriedades StreamingPolicy: 

|Nome|Filtro|Encomenda|
|---|---|---|
|ID|||
|nome|eq, ne, ge, le, gt, lt|Ascendente e descendente|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|Properties.created |eq, ne, ge, le,  gt, lt|Ascendente e descendente|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|tipo|||

#### <a name="pagination"></a>Paginação

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


### <a name="transform"></a>Transformação

#### <a name="filteringordering"></a>Filtragem/ordenação

A tabela seguinte mostra como essas opções podem ser aplicadas para o [transforma](https://docs.microsoft.com/rest/api/media/transforms) propriedades: 

| Name    | Filtro                        | Encomenda |
|---------|-------------------------------|-------|
| nome                    | eq            | Ascendente e descendente|
| Properties.created      | gt, ge, lt, le| Ascendente e descendente|
| properties.lastModified | gt, ge, lt, le | Ascendente e descendente|

## <a name="next-steps"></a>Passos Seguintes

[Um ficheiro do Stream](stream-files-dotnet-quickstart.md)
