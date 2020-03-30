---
title: 'Quickstart: Criar um índice de pesquisa no Carteiro usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Neste rest API quickstart, aprenda a chamar as APIs de PESQUISA Cognitiva Azure usando o Carteiro e dados e definições de amostra.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: c502886aac9d13f7a470a9b83f1fc12334913beb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121645"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Quickstart: Criar um índice de pesquisa cognitiva azure no carteiro usando APIs REST
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C #](search-create-index-dotnet.md)
> * [Pitão](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Uma das formas mais fáceis de explorar as APIs de [pesquisa cognitiva azure](https://docs.microsoft.com/rest/api/searchservice) é usar o Carteiro ou outra ferramenta de teste web para formular pedidos HTTP e inspecionar as respostas. Com as ferramentas certas e estas instruções, pode enviar pedidos e ver respostas antes de escrever código.

Este artigo explica como formular pedidos interactivamente. Em alternativa, pode [descarregar e importar uma coleção de Carteiros](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) para utilizar pedidos predefinidos.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este arranque rápido. 

+ [A aplicação](https://www.getpostman.com/) de desktop postman é usada para o envio de pedidos para a Pesquisa Cognitiva Azure.

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Keys,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-cognitive-search"></a>Ligue-se à Pesquisa Cognitiva Azure

Nesta secção, utilize a sua ferramenta web de eleição para configurar ligações à Pesquisa Cognitiva Azure. Cada ferramenta persiste na informação do cabeçalho de pedido para a sessão, o que significa que só tem de introduzir a chave api-key e o Tipo de Conteúdo uma vez.

Para qualquer uma das ferramentas, é necessário escolher um comando (GET, POST, PUT, e assim por diante), fornecer um ponto final url, e para algumas tarefas, fornecer JSON no corpo do pedido. Substitua o nome do serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) por um valor válido. Adicione `$select=name` para devolver apenas o nome de cada índice. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Repare no prefixo HTTPS, no nome do serviço, no nome de um objeto (neste caso, na recolha de índices) e na [versão api.](search-api-versions.md) A versão api é uma cadeia retâmtal necessária e especificada quanto `?api-version=2019-05-06` à versão atual. As versões de API são atualizadas regularmente. Incluir a versão de api em cada pedido dá-lhe controlo total sobre qual das versões é utilizada.  

A composição do cabeçalho de pedido inclui dois elementos, tipo de conteúdo, além da chave api usada para autenticar a Pesquisa Cognitiva Azure. Substitua a chave API do administrador (YOUR-AZURE-SEARCH-ADMIN-API-KEY) por um valor válido. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

No Carteiro, formular um pedido que se pareça com a seguinte imagem. Escolha **GET** como verbo, forneça o URL e clique em **Enviar**. Este comando liga-se à Pesquisa Cognitiva Azure, lê a recolha de índices e devolve o código de estado HTTP 200 numa ligação bem sucedida. Se o seu serviço já tiver índices, a resposta também incluirá definições de índice.

![URL de pedido de carteiro e cabeçalho](media/search-get-started-postman/postman-url.png "URL de pedido de carteiro e cabeçalho")

## <a name="1---create-an-index"></a>1 - Criar um índice

Na Pesquisa Cognitiva Azure, normalmente cria-se o índice antes de o carregar com dados. A [API Create Index REST](https://docs.microsoft.com/rest/api/searchservice/create-index) é utilizada para esta tarefa. 

O URL é estendido `hotels` para incluir o nome do índice.

Para fazer isto no Carteiro:

1. Mude o verbo para **PUT**.

2. Copiar neste `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`URL .

3. Fornecer a definição de índice (código de cópia pronto é fornecido abaixo) no corpo do pedido.

4. Clique em **Enviar**.

![Documento Index JSON no órgão de pedido](media/search-get-started-postman/postman-request.png "Documento Index JSON no órgão de pedido")

### <a name="index-definition"></a>Definição do índice

A coleção de campos define a estrutura documental. Cada documento deve ter estes campos, e cada campo deve ter um tipo de dados. Os campos de cadeia são utilizados em pesquisas em texto completo, pelo que pode transmitir os dados numéricos como cadeias se quiser que esse conteúdo seja pesquisável.

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

## <a name="2---load-documents"></a>2 - Carregar documentos

A criação e o preenchimento do índice são dois passos distintos. Na Pesquisa Cognitiva Azure, o índice contém todos os dados pesquisáveis, que pode fornecer como documentos JSON. A [API de Adicionar, Atualização ou Eliminar Documentos REST](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) é utilizada para esta tarefa. 

O URL é estendido `docs` para `index` incluir as coleções e o funcionamento.

Para fazer isto no Carteiro:

1. Altere o verbo para **POST**.

2. Copiar neste `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`URL .

3. Forneça os documentos JSON (código pronto para cópia saem) no corpo do pedido.

4. Clique em **Enviar**.

![Documentos da JSON no órgão de pedido](media/search-get-started-postman/postman-docs.png "Documentos da JSON no órgão de pedido")

### <a name="json-documents-to-load-into-the-index"></a>Documentos da JSON para carregar no índice

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
> Em determinadas origens de dados, pode escolher a abordagem de *indexador* alternativa, que simplifica e reduz a quantidade de código necessário para a indexação. Para obter mais informações, veja [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Operações do indexador).


## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Agora que um índice e documentos estão carregados, pode emitir consultas contra eles usando documentos de [pesquisa REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

O URL é estendido para incluir uma expressão de consulta, especificada através do operador de pesquisa.

Para fazer isto no Carteiro:

1. Mude o verbo para **GET**.

2. Copiar neste `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`URL .

3. Clique em **Enviar**.

Esta consulta é vazia e devolve uma contagem dos documentos nos resultados da pesquisa. O pedido e a resposta devem ser semelhantes aos seguintes screenshot para o Carteiro depois de clicar em **Enviar**. O código de estado deve ser 200.

 ![GET com cadeia de pesquisa no URL](media/search-get-started-postman/postman-query.png "GET com cadeia de pesquisa no URL")

Experimente outros exemplos de consulta para sentir a sintaxe. Pode fazer uma pesquisa de cordas, verbatim $filter consultas, limitar o conjunto de resultados, pesquisar para campos específicos, e muito mais.

Troque o URL atual com os abaixo, clicando **Enviar** cada vez para ver os resultados.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2019-05-06

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2019-05-06

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2019-05-06

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2019-05-06
```

## <a name="get-index-properties"></a>Obtenha propriedades de índice
Também pode usar [estatísticas get](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) para consultar as contagens de documentos e o tamanho do índice: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Adicionar `/stats` ao seu URL devolve informações do índice. No Postman, o pedido deve ter um aspeto semelhante ao seguinte e a resposta inclui uma contagem de documentos e o espaço utilizado em bytes.

 ![Obtenha informações sobre índices](media/search-get-started-postman/postman-system-query.png "Obtenha informações sobre índices")

Repare que a sintaxe da versão de api é diferente. Para este pedido, utilize `?` para acrescentar a versão de api. O `?` separador do caminho URL da corda de consulta, enquanto & separa cada par de 'name=value' na corda de consulta. Nesta consulta, a versão de api é o primeiro e único item na cadeia de consulta.

## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe executar tarefas fundamentais, pode avançar com a API adicional do REST a solicitar funcionalidades mais avançadas, como os indexadores ou a [criação de um pipeline](cognitive-search-tutorial-blob.md)de pesquisa cognitiva . Para o seu próximo passo, recomendamos o seguinte link:

> [!div class="nextstepaction"]
> [TUTORDE REST: Index e pesquisa de dados semi-estruturados (bolhas JSON) em Pesquisa Cognitiva Azure](search-semi-structured-data.md)
