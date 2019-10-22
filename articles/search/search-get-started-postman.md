---
title: 'Início rápido: criar um índice de pesquisa no postmaster usando APIs REST-Azure Search'
description: Saiba como chamar as APIs REST do Azure Search usando o post e definições e dados de exemplo.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: heidist
ms.openlocfilehash: ffa20599ae57908f9b0ea848ab68f41a3d0e2a14
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72176038"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Início rápido: criar um índice de Azure Search no postmaster usando APIs REST
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Uma das maneiras mais fáceis de explorar as [APIs REST Azure Search](https://docs.microsoft.com/rest/api/searchservice) está usando o postmaster ou outra ferramenta de teste da Web para formular solicitações HTTP e inspecionar as respostas. Com as ferramentas certas e estas instruções, pode enviar pedidos e ver respostas antes de escrever código.

Este artigo explica como formular solicitações interativamente. Como alternativa, você pode [baixar e importar uma coleção de postmaster](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) para usar solicitações predefinidas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços e ferramentas são necessários para este guia de início rápido. 

+ O [aplicativo de área de trabalho do postmaster](https://www.getpostman.com/) é usado para enviar solicitações para Azure Search.

+ [Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este guia de início rápido. 

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **configurações**  > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações exigem uma chave de API em cada solicitação enviada ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-search"></a>Conectar-se ao Azure Search

Nesta seção, use a ferramenta da Web de sua escolha para configurar conexões com Azure Search. Cada ferramenta mantém informações de cabeçalho de solicitação para a sessão, o que significa que você só precisa inserir a chave de API e o tipo de conteúdo uma vez.

Para qualquer ferramenta, você precisa escolher um comando (GET, POST, PUT e assim por diante), fornecer um ponto de extremidade de URL e, para algumas tarefas, fornecer JSON no corpo da solicitação. Substitua o nome do serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) por um valor válido. Adicione `$select=name` para retornar apenas o nome de cada índice. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Observe o prefixo HTTPS, o nome do serviço, o nome de um objeto (nesse caso, a coleção de índices) e a versão de [API](search-api-versions.md). A versão de API é uma cadeia de caracteres obrigatória e em minúsculas especificada como `?api-version=2019-05-06` para a versão atual. As versões de API são atualizadas regularmente. Incluir a versão de api em cada pedido dá-lhe controlo total sobre qual das versões é utilizada.  

A composição de cabeçalho de solicitação inclui dois elementos, tipo de conteúdo, mais a chave de API usada para autenticar para Azure Search. Substitua a chave de API de administração (YOUR-AZURE-SEARCH-ADMIN-API-KEY) por um valor válido. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

No postmaster, formule uma solicitação semelhante à captura de tela a seguir. Escolha **obter** como o verbo, forneça a URL e clique em **Enviar**. Esse comando conecta-se a Azure Search, lê a coleção de índices e retorna o código de status HTTP 200 em uma conexão bem-sucedida. Se o seu serviço já tiver índices, a resposta também incluirá definições de índice.

![URL e cabeçalho da solicitação do postmaster](media/search-get-started-postman/postman-url.png "URL e cabeçalho da solicitação do postmaster")

## <a name="1---create-an-index"></a>1 - Criar um índice

Em Azure Search, geralmente você cria o índice antes de carregá-lo com os dados. A [API REST criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index) é usada para esta tarefa. 

A URL é estendida para incluir o nome do índice `hotels`.

Para fazer isso no postmaster:

1. Altere o verbo para **Put**.

2. Copiar nesta URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`.

3. Forneça a definição de índice (o código pronto para cópia é fornecido abaixo) no corpo da solicitação.

4. Clique em **Enviar**.

![Indexar documento JSON no corpo da solicitação](media/search-get-started-postman/postman-request.png "Indexar documento JSON no corpo da solicitação")

### <a name="index-definition"></a>Definição do índice

A coleção Fields define a estrutura do documento. Cada documento deve ter esses campos, e cada campo deve ter um tipo de dados. Os campos de cadeia são utilizados em pesquisas em texto completo, pelo que pode transmitir os dados numéricos como cadeias se quiser que esse conteúdo seja pesquisável.

Os atributos no campo determinam a ação permitida. As APIs REST permitem muitas ações, por predefinição. Por exemplo, todas as cadeias são pesquisáveis, recuperáveis, filtráveis e facetáveis por predefinição. Geralmente, você só precisa definir atributos quando precisa desativar um comportamento.

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

## <a name="2---load-documents"></a>2-carregar documentos

A criação e o preenchimento do índice são dois passos distintos. No Azure Search, o índice contém todos os dados pesquisáveis, que pode fornecer como documentos JSON. A [API REST adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) é usada para esta tarefa. 

A URL é estendida para incluir as coleções `docs` e a operação `index`.

Para fazer isso no postmaster:

1. Altere o verbo para **POST**.

2. Copiar nesta URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Forneça os documentos JSON (o código pronto para cópia está abaixo) no corpo da solicitação.

4. Clique em **Enviar**.

![Documentos JSON no corpo da solicitação](media/search-get-started-postman/postman-docs.png "Documentos JSON no corpo da solicitação")

### <a name="json-documents-to-load-into-the-index"></a>Documentos JSON a serem carregados no índice

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

Em alguns segundos, você deverá ver uma resposta HTTP 201 na lista de sessões. Isto indica que os documentos foram criados com êxito. 

Se obtiver um 207, pelo menos um documento falhou ao carregar. Se obtiver um 404, tem um erro de sintaxe no cabeçalho ou no corpo do pedido. Confirme que alterou o ponto final, de modo a incluir `/docs/index`.

> [!Tip]
> Em determinadas origens de dados, pode escolher a abordagem de *indexador* alternativa, que simplifica e reduz a quantidade de código necessário para a indexação. Para obter mais informações, veja [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Operações do indexador).


## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Agora que um índice e documentos são carregados, você pode emitir consultas para eles usando a [API REST de documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

A URL é estendida para incluir uma expressão de consulta, especificada usando o operador de pesquisa.

Para fazer isso no postmaster:

1. Altere o verbo para **Get**.

2. Copiar nesta URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Clique em **Enviar**.

Essa consulta é um vazio e retorna uma contagem dos documentos nos resultados da pesquisa. A solicitação e a resposta devem ser semelhantes à captura de tela a seguir para o postmaster depois que você clicar em **Enviar**. O código de estado deve ser 200.

 ![OBTER com a cadeia de caracteres de pesquisa na URL](media/search-get-started-postman/postman-query.png "OBTER com a cadeia de caracteres de pesquisa na URL")

Experimente alguns outros exemplos de consulta para ter uma ideia da sintaxe. Você pode fazer uma pesquisa de cadeia de caracteres, textualmente $filter consultas, limitar o conjunto de resultados, definir o escopo da pesquisa para campos específicos e muito mais.

Troque a URL atual pelos itens abaixo, clicando em **Enviar** cada vez para exibir os resultados.

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

## <a name="get-index-properties"></a>Obter propriedades do índice
Você também pode usar [obter estatísticas](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) para consultar contagens de documentos e tamanho do índice: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Adicionar `/stats` à sua URL retorna informações de índice. No Postman, o pedido deve ter um aspeto semelhante ao seguinte e a resposta inclui uma contagem de documentos e o espaço utilizado em bytes.

 ![Obter informações de índice](media/search-get-started-postman/postman-system-query.png "Obter informações de índice")

Repare que a sintaxe da versão de api é diferente. Para este pedido, utilize `?` para acrescentar a versão de api. O `?` separa o caminho da URL da cadeia de caracteres de consulta, enquanto & separa cada par de ' nome = valor ' na cadeia de caracteres de consulta. Nesta consulta, a versão de api é o primeiro e único item na cadeia de consulta.

## <a name="clean-up"></a>Limpeza

Quando você está trabalhando em sua própria assinatura, é uma boa ideia no final de um projeto identificar se você ainda precisa dos recursos que criou. Os recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir o conjunto inteiro de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Agora que você sabe como executar tarefas básicas, pode avançar com chamadas adicionais à API REST para obter recursos mais avançados, como indexadores ou [configurar um pipeline de pesquisa cognitiva](cognitive-search-tutorial-blob.md). Para a próxima etapa, recomendamos o seguinte link:

> [!div class="nextstepaction"]
> [Tutorial de REST: indexe e pesquise dados semiestruturados (BLOBs JSON) no Azure Search](search-semi-structured-data.md)
