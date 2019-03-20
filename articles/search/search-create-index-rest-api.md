---
title: Criar um índice no código com o PowerShell e a API REST - Azure Search
description: Crie um índice pesquisável de texto completo em código usando a pedidos HTTP e a API de REST do Azure Search.
ms.date: 03/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 0524bd224e3da3e6a9b18a4225c88e9c43d07606
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223415"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>Início rápido: Criar um índice da Azure Search com o PowerShell e a API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Este artigo o orienta pelo processo de criação, para carregar e consultar um Azure Search [índice](search-what-is-an-index.md) com o PowerShell e o [API de REST do serviço do Azure Search](https://docs.microsoft.com/rest/api/searchservice/). A definição de índice e o conteúdo pesquisável é fornecido no corpo do pedido como conteúdo JSON bem formado.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este início rápido. Outros pré-requisitos incluem os seguintes itens.

[PowerShell 5.1 ou posterior](https://github.com/PowerShell/PowerShell), utilizando [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) para obter os passos seqüenciais e interativos.

Um ponto final do URL e o administrador a chave de api do seu serviço de pesquisa. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. No portal do Azure, no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto de extremidade de exemplo poderá ser semelhante a https:\//my-service-name.search.windows.net.

2. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

   ![Obter uma chave de acesso e de ponto final HTTP](media/search-fiddler/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

   Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-search"></a>Ligar ao Azure Search

No PowerShell, crie uma **$headers** objeto para armazenar o tipo de conteúdo e a chave de API. Só tem de definir este cabeçalho de uma vez para a duração da sessão, mas irá adicioná-la para cada solicitação. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

Criar uma **$url** objeto que especifica o serviço indexa a coleção. O `mydemo` nome do serviço destina-se como um marcador de posição. Substitua-o com um serviço de pesquisa válido numa subscrição atual ao longo deste exemplo.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

Execute **Invoke-RestMethod** para enviar um pedido GET para o serviço e verificar a ligação. Adicione **ConvertTo-Json** para que possa visualizar as respostas enviadas a resposta do serviço.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Se o serviço está vazio e tem sem índices, os resultados são semelhantes ao seguinte exemplo. Caso contrário, verá uma representação JSON das definições de índice.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
    "value":  [

              ]
}
```

## <a name="1---create-an-index"></a>1 - criar um índice

A menos que estiver a utilizar o portal, tem de existir um índice no serviço antes de carregar dados. Este passo define o índice e envia-o para o serviço. O [criar índice (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) é utilizado para este passo.

Elementos necessários de um índice incluem um nome e uma coleção de campos. A coleção de campos define a estrutura de um *documento*. Cada campo tem um nome, tipo e atributos que determinam como são utilizadas (por exemplo, se é texto completo pesquisável, filtrável ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos do tipo `Edm.String` tem de ser designado como o *chave* para a identidade do documento.

Este índice com o nome "Hotéis" e tem as definições de campo apresentado abaixo. A definição de índice Especifica um [analisador de idioma](index-add-language-analyzers.md) para o `description_fr` campo porque destina-se para armazenar texto em francês, que vamos adicionar um exemplo mais adiante.

Cole este exemplo do PowerShell para criar uma **$body** objeto que contém o esquema de índice.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

Defina o URI para a coleção de índices no seu serviço e o *hotéis* índice.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

Execute o comando com **$url**, **$headers**, e **$body** para criar o índice no serviço. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
Resultados devem ser semelhantes ao seguinte (truncado para os primeiros dois campos para fins de brevidade):

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> Para efeitos de verificação, poderia também Verifique a lista de índices no portal do ou volte a executar o comando utilizado para verificar a ligação de serviço para ver os *hotéis* índice listados na coleção de índices.

## <a name="2---load-documents"></a>2 - carregar documentos

Para enviar documentos, utilize um pedido de HTTP POST ao ponto final do URL de seu índice. A API REST para esta tarefa é [adicionar, atualizar ou eliminar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Cole este exemplo do PowerShell para criar uma **$body** objeto que contém os documentos que pretende carregar. 

Este pedido inclui dois completo e um registo parcial. O registo parcial demonstra que pode carregar documentos incompletos. O `@search.action` parâmetro especifica a forma como a indexação é feita. Valores válidos incluem carregar, intercalar, mergeOrUpload e delete. O comportamento de mergeOrUpload ou cria um novo documento para hotelId = 3, ou atualiza os conteúdos se já existir.

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

Definir o ponto final o *hotéis* coleção de documentos e incluir a operação de índice (índices/hotéis/docs/índice).

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

Execute o comando com **$url**, **$headers**, e **$body** para carregar documentos para o índice de hotéis.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
Os resultados devem ter um aspeto semelhantes ao seguinte exemplo. Deverá ver um código de estado de 201. Para obter uma descrição de todos os códigos de estado, consulte [códigos de estado HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
    "value":  [
                  {
                      "key":  "1",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "2",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "3",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3 - um índice de pesquisa

Este passo mostra-lhe como consultar um índice com o [API de pesquisa de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Definir o ponto final o *hotéis* coleção de documentos e adicione um **pesquisa** parâmetro para incluir as cadeias de consulta. Esta cadeia é uma pesquisa em branco e retorna uma lista unranked de todos os documentos.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

Execute o comando para enviar a **$url** ao serviço.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Resultados devem ser semelhantes à saída seguinte.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

Experimente alguns outros exemplos de consulta para ter uma noção do que a sintaxe. Pode fazer uma pesquisa de cadeia de caracteres, consultas de $filter textuais, limitar o conjunto de resultados, o escopo a pesquisa a campos específicos e muito mais.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Limpeza 

Deve excluir o índice se já não precisam dele. Um serviço gratuito está limitado a três índices. Pode querer eliminar os índices que não estiver ativamente a utilizar, para que pode percorrer a outros tutoriais.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Passos Seguintes

Tente adicionar descrições francês para o índice. O exemplo seguinte inclui cadeias de caracteres francês e demonstra ações adicionais de pesquisa. Utilize mergeOrUpload para criar ou adicionar a campos existentes. As seguintes cadeias de caracteres precisam ser codificados com UTF-8.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville",
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```
