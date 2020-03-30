---
title: 'Quickstart: Criar um índice de pesquisa no PowerShell usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Neste rest API quickstart, aprenda a criar um índice, dados de carga e executar consultas usando o Pressvo-RestMethod da PowerShell e a API de Pesquisa Cognitiva Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 612751c2405cd55ad0b3760aa8e093e434a22f57
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121603"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Quickstart: Criar um índice de pesquisa cognitiva azure no PowerShell usando APIs REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C #](search-create-index-dotnet.md)
> * [Carteiro (REST)](search-get-started-postman.md)
> * [Pitão](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

Este artigo acompanha-o através do processo de criação, carregamento e consulta de um índice de pesquisa cognitiva Azure utilizando a PowerShell e as APIs de [PESQUISA Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/). Este artigo explica como executar comandos PowerShell interativamente. Em alternativa, pode [descarregar e executar um script Powershell](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) que executa as mesmas operações.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este arranque rápido. 

+ [PowerShell 5.1 ou posterior](https://github.com/PowerShell/PowerShell), utilizando o [Invoca-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) para passos sequenciais e interativos.

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Em **Definições** > **Keys,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-cognitive-search"></a>Ligue-se à Pesquisa Cognitiva Azure

1. Na PowerShell, crie **um** $headers objeto para armazenar o tipo de conteúdo e a chave API. Substitua a chave API (YOUR-ADMIN-API-KEY) por uma chave válida para o seu serviço de pesquisa. Só tem de definir este cabeçalho uma vez durante a sessão, mas irá adicioná-lo a todos os pedidos. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Crie um objeto **$url** que especifique a recolha de índices do serviço. Substitua o nome de serviço (YOUR-SEARCH-SERVICE-NAME) por um serviço de pesquisa válido.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Executar **Invoke-RestMethod** para enviar um pedido GET para o serviço e verificar a ligação. Adicione **convertTo-Json para** que possa ver as respostas enviadas de volta do serviço.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Se o serviço estiver vazio e não tiver índices, os resultados são semelhantes ao seguinte exemplo. Caso contrário, verá uma representação json das definições de índice.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - Criar um índice

A menos que esteja a utilizar o portal, deve existir um índice no serviço antes de poder carregar dados. Este passo define o índice e empurra-o para o serviço. A [API Create Index REST](https://docs.microsoft.com/rest/api/searchservice/create-index) é utilizada para este passo.

Os elementos necessários de um índice incluem um nome e uma coleção de campos. A coleção de campos define a estrutura de um *documento.* Cada campo tem um nome, tipo e atributos que determinam como é usado (por exemplo, se é pesquisável em texto completo, filtrado ou recuperável nos resultados da pesquisa). Dentro de um índice, um `Edm.String` dos campos do tipo deve ser designado como a *chave* para a identidade do documento.

Este índice chama-se "hotéis-quickstart" e tem as definições de campo que você vê abaixo. É um subconjunto de um índice de [hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) maior usado em outros walkthroughs. Aparamo-lo neste início rápido para a brevidade.

1. Passe este exemplo no PowerShell para criar um objeto **$body** contendo o esquema do índice.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Detete o URI para a coleção de índices no seu serviço e no índice *hotéis-quickstart.*

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Executar o comando com **$url**, **$headers**, e **$body** para criar o índice no serviço. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Os resultados devem ser semelhantes a este (truncado aos dois primeiros campos para a brevidade):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Para verificação, também pode consultar a lista de Índices no portal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

Para empurrar documentos, utilize um pedido HTTP POST para o ponto final do URL do seu índice. O REST API para esta tarefa é [Adicionar, Atualizar ou Eliminar Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Passe este exemplo no PowerShell para criar um objeto **$body** contendo os documentos que pretende carregar. 

    Este pedido inclui dois registos completos e um parcial. O registo parcial demonstra que pode fazer o upload de documentos incompletos. O `@search.action` parâmetro especifica como a indexação é feita. Os valores válidos incluem upload, fusão, fusão OrUpload e eliminar. O comportamento da fusãoOrUpload ou cria um novo documento para o hotelId = 3, ou atualiza o conteúdo se já existir.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Detete o ponto final para a recolha de docs de arranque rápido dos *hotéis* e inclua a operação de índice (índices/hotéis-quickstart/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Dirija o comando com **$url**, **$headers**, e **$body** para carregar documentos no índice de arranque rápido dos hotéis.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Os resultados devem ser semelhantes aos seguintes exemplos. Devia ver um [código de estado de 201.](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
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
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Este passo mostra-lhe como consultar um índice utilizando a API de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)de Pesquisa .

Certifique-se de que utiliza citações únicas em $urls de pesquisa. As cordas de **$** consulta incluem caracteres, e você pode omitir ter que escapar deles se toda a corda estiver fechada em citações únicas.

1. Defina o ponto final para a coleção de docs de arranque rápido dos *hotéis* e adicione um parâmetro de **pesquisa** para passar numa corda de consulta. 
  
   Esta cadeia executa uma pesquisa vazia (search=*), devolvendo uma lista não classificada (pontuação de pesquisa = 1,0) de documentos arbitrários. Por padrão, a Pesquisa Cognitiva Azure devolve 50 partidas de cada vez. Tal como estruturado, esta consulta devolve toda uma estrutura e valores documentais. Adicione **$count=fiel** para obter uma contagem de todos os documentos nos resultados.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Mande o comando para enviar o **$url** para o serviço.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Os resultados devem ser semelhantes aos seguintes resultados.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Experimente outros exemplos de consulta para sentir a sintaxe. Pode fazer uma pesquisa de cordas, verbatim $filter consultas, limitar o conjunto de resultados, pesquisar para campos específicos, e muito mais.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou o PowerShell para passar pelo fluxo básico de trabalho para criar e aceder a conteúdos em Pesquisa Cognitiva Azure. Com os conceitos em mente, recomendamos passar para cenários mais avançados, como indexação a partir de fontes de dados do Azure;

> [!div class="nextstepaction"]
> [TUTORDE REST: Index e pesquisa de dados semi-estruturados (bolhas JSON) em Pesquisa Cognitiva Azure](search-semi-structured-data.md)