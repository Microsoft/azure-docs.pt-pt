---
title: incluir ficheiro
description: incluir ficheiro
manager: nitinme
ms.author: heidist
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.custom: include file
ms.date: 11/17/2020
ms.openlocfilehash: 0196b31cd1c8f04a883391bb3295733fe6f46160
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714219"
---
O artigo utiliza a aplicação de desktop do Carteiro. Você pode [baixar e importar uma coleção de Carteiro](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) se preferir usar pedidos predefinidos.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este arranque rápido. 

+ [A aplicação de desktop do Carteiro](https://www.getpostman.com/) é usada para enviar pedidos para a Azure Cognitive Search.

+ [Crie um serviço de Pesquisa Cognitiva Azure](../search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

## <a name="copy-a-key-and-url"></a>Copiar uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

![Obtenha uma chave de acesso http e acesso](../media/search-get-started-rest/get-url-key.png "Obtenha uma chave de acesso http e acesso")

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-cognitive-search"></a>Ligue-se à Pesquisa Cognitiva Azure

Nesta secção, utilize a sua ferramenta web de eleição para configurar ligações à Azure Cognitive Search. Cada ferramenta persiste em solicitar informações de cabeçalho para a sessão, o que significa que só tem de introduzir a tecla api e o Tipo de Conteúdo uma vez.

Para qualquer uma das ferramentas, é necessário escolher um comando (GET, POST, PUT, etc.), fornecer um ponto final URL, e para algumas tarefas, fornecer JSON no corpo do pedido. Substitua o nome do serviço de pesquisa (NOME DO SERVIÇO DE BUSCA) por um valor válido. Adicione `$select=name` para devolver apenas o nome de cada índice. 

> `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name`

Note o prefixo HTTPS, o nome do serviço, o nome de um objeto (neste caso, a coleção de índices) e a [versão api.](../search-api-versions.md) A versão api é uma corda minúscula necessária especificada como `?api-version=2020-06-30` para a versão atual. As versões de API são atualizadas regularmente. Incluir a versão de api em cada pedido dá-lhe controlo total sobre qual das versões é utilizada.  

A composição do cabeçalho do pedido inclui dois elementos: `Content-Type` e o usado para `api-key` autenticar para a Azure Cognitive Search. Substitua a tecla API de administração (YOUR-AZURE-SEARCH-ADMIN-API-KEY) por um valor válido. 

```http
api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
Content-Type: application/json
```

No Carteiro, formula um pedido que se parece com a seguinte imagem. Escolha **GET** como comando, forneça o URL e clique em **Enviar.** Este comando liga-se à Azure Cognitive Search, lê a recolha de índices e devolve o código de estado HTTP 200 numa ligação bem sucedida. Se o seu serviço já tiver índices, a resposta também incluirá definições de índice.

![Carteiro solicita URL e cabeçalho](../media/search-get-started-rest/postman-url.png "Carteiro solicita URL e cabeçalho")

## <a name="1---create-an-index"></a>1 - Criar um índice

Na Pesquisa Cognitiva Azure, normalmente cria o índice antes de o carregar com dados. A [API De Criar Índices](/rest/api/searchservice/create-index) É utilizada para esta tarefa. 

O URL é estendido para incluir o nome do `hotels` índice.

Para fazer isso no Carteiro:

1. Mude o comando para **PUT**.

2. Copiar neste `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30` URL.

3. Forneça a definição de índice (o código pronto para cópia é fornecido abaixo) no corpo do pedido.

4. Clique em **Enviar**.

![Documento JSON do índice no organismo de pedido](../media/search-get-started-rest/postman-request.png "Documento JSON do índice no organismo de pedido")

### <a name="index-definition"></a>Definição do índice

A recolha de campos define a estrutura documental. Cada documento deve ter estes campos, e cada campo deve ter um tipo de dados. Os campos de cordas são utilizados na pesquisa completa por texto. Se precisar de dados numéricos para ser pes pes pes pes pes pes pes queire, terá de lançar dados numéricos como cordas.

Os atributos no campo determinam a ação permitida. As APIs REST permitem muitas ações, por predefinição. Por exemplo, todas as cadeias são pesquisáveis, recuperáveis, filtráveis e facetáveis por predefinição. Muitas vezes, só tens de definir atributos quando precisas de desligar um comportamento.

```json
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
```

Quando submete este pedido, deverá receber uma resposta HTTP 201, que indica que o índice foi criado com êxito. Pode verificar esta ação no portal, mas tenha em conta que a página do portal tem intervalos de atualização, de modo que poderá demorar um minuto ou dois a estar atualizada.

> [!TIP]
> Se obtiver HTTP 504, certifique-se de que o URL especifica HTTPS. Se vir HTTP 400 ou 404, verifique o corpo do pedido para verificar que não ocorreram erros ao copiar-colar. Um HTTP 403 normalmente indica um problema com a chave de API (uma chave inválida ou um problema de sintaxe com a forma como a chave de API é especificada).

## <a name="2---load-documents"></a>2 - Documentos de carga

A criação e o preenchimento do índice são dois passos distintos. Na Pesquisa Cognitiva Azure, o índice contém todos os dados pesmáveis. Neste cenário, os dados são fornecidos como documentos JSON. A [AAPI de Adicionar, Atualizar ou Eliminar Documentos REST](/rest/api/searchservice/addupdate-or-delete-documents) É utilizada para esta tarefa. 

O URL é estendido para incluir as `docs` coleções e `index` o funcionamento.

Para fazer isso no Carteiro:

1. Altere o comando para **POST**.

2. Copiar neste `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30` URL.

3. Forneça os documentos JSON (código pronto para cópias em baixo) no corpo do pedido.

4. Clique em **Enviar**.

![Documentos JSON no organismo de pedido](../media/search-get-started-rest/postman-docs.png "Documentos JSON no organismo de pedido")

### <a name="json-documents-to-load-into-the-index"></a>Documentos JSON para carregar no índice

O Corpo do Pedido contém quatro documentos que devem ser adicionados ao índice dos hotéis.

```json
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
```

Em poucos segundos, deverá ver uma resposta HTTP 201 na lista de sessões. Isto indica que os documentos foram criados com êxito. 

Se obtiver um 207, pelo menos um documento falhou ao carregar. Se obtiver um 404, tem um erro de sintaxe no cabeçalho ou no corpo do pedido. Confirme que alterou o ponto final, de modo a incluir `/docs/index`.

> [!Tip]
> Em determinadas origens de dados, pode escolher a abordagem de *indexador* alternativa, que simplifica e reduz a quantidade de código necessário para a indexação. Para obter mais informações, veja [Indexer operations](/rest/api/searchservice/indexer-operations) (Operações do indexador).


## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Agora que um conjunto de índices e documentos estão carregados, pode emitir consultas contra eles usando [documentos de pesquisa REST API](/rest/api/searchservice/search-documents).

O URL é estendido para incluir uma expressão de consulta, especificada usando o operador de pesquisa.

Para fazer isso no Carteiro:

1. Altere o comando para **GET**.

2. Copiar neste `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2020-06-30` URL.

3. Clique em **Enviar**.

Esta consulta é um vazio e devolve uma contagem dos documentos nos resultados da pesquisa. O pedido e a resposta devem ser semelhantes à imagem seguinte para Carteiro depois de clicar em **Enviar**. O código de estado deve ser 200.

 ![GET com cadeia de pesquisa no URL](../media/search-get-started-rest/postman-query.png "GET com cadeia de pesquisa no URL")

Experimente outros exemplos de consulta para sentir a sintaxe. Você pode fazer uma pesquisa de cordas, perguntas verbatim $filter, limitar os resultados definidos, estender a pesquisa para campos específicos, e muito mais.

Troque o URL atual com os abaixo, clicando **Enviar** cada vez para ver os resultados.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2020-06-30

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2020-06-30

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2020-06-30

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2020-06-30
```

## <a name="get-index-properties"></a>Obtenha propriedades de índice

Também pode utilizar [Obter Estatísticas](/rest/api/searchservice/get-index-statistics) para consultar as contagens de documentos e o tamanho do índice: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2020-06-30
```

Adicionar `/stats` ao seu URL retorna informações de índice. No Postman, o pedido deve ter um aspeto semelhante ao seguinte e a resposta inclui uma contagem de documentos e o espaço utilizado em bytes.

 ![Obtenha informações de índice](../media/search-get-started-rest/postman-system-query.png "Obtenha informações de índice")

Repare que a sintaxe da versão de api é diferente. Para este pedido, utilize `?` para acrescentar a versão de api. O `?` caminho de URL separa o caminho do URL da cadeia de consulta, enquanto & separa cada par 'name=value' na cadeia de consulta. Nesta consulta, a versão de api é o primeiro e único item na cadeia de consulta.