---
title: 'Início rápido: Criar, carregar e consultar índices com o Postman e APIs REST - Azure Search'
description: Saiba como chamar as APIs de REST de pesquisa do Azure com o Postman e dados de exemplo e definições.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 07/11/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 015dd3631322978d6416041a3eea8390a72b0c17
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840222"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Início rápido: Criar um índice da Azure Search no Postman com REST APIs
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Uma das formas mais simples de explorar a [as APIs de REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice) está a utilizar o Postman ou web outra ferramenta de teste para formular pedidos HTTP e inspecionar as respostas. Com as ferramentas certas e estas instruções, pode enviar pedidos e ver respostas antes de escrever código.

Este artigo explica como formular pedidos de forma interativa. Em alternativa, pode [transferir e importar uma coleção do Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) utilizar pedidos predefinidos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços e ferramentas são utilizadas neste início rápido. 

+ [Aplicação de ambiente de trabalho postman](https://www.getpostman.com/) é utilizado para enviar pedidos para o Azure Search.

+ [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este início rápido. 

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

![Obter uma chave de acesso e de ponto final HTTP](media/search-get-started-postman/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-search"></a>Ligar ao Azure Search

Nesta secção, utilize a sua ferramenta de web à escolha para configurar ligações para o Azure Search. Cada ferramenta mantém as informações de cabeçalho de pedido para a sessão, o que significa que apenas tem de introduzir a chave de api e Content-Type única.

Para qualquer uma das ferramentas, precisa de escolher um comando (GET, POST, PUT e assim por diante), fornecer um ponto de final do URL e, para algumas tarefas, forneça JSON no corpo do pedido. Substitua o nome do serviço de pesquisa (seu-pesquisa-SERVICE-NAME) com um valor válido. Adicionar `$select=name` para devolver apenas o nome de cada índice. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Tenha em atenção o prefixo HTTPS, o nome do serviço, o nome de um objeto (nesse caso, a coleção de índices) e o [versão de api](search-api-versions.md). A api-version é uma cadeia de caracteres em minúsculas, necessária especificada como `?api-version=2019-05-06` para a versão atual. Versões de API são atualizadas regularmente. Incluir a versão de api em cada pedido dá-lhe controlo total sobre qual das versões é utilizada.  

Composição do cabeçalho do pedido inclui dois elementos, tipo de conteúdo, além da chave de api, utilizado para autenticar para o Azure Search. Substitua a chave de API de administração (YOUR-AZURE-SEARCH-ADMIN-API-KEY) com um valor válido. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

No Postman, formule um pedido parecido com a seguinte captura de ecrã. Escolher **Obtenha** como o verbo, forneça o URL e clique em **enviar**. Este comando liga ao Azure Search, lê a coleção de índices e retorna o código de estado HTTP 200 numa ligação com êxito. Se o seu serviço já foi de índices, a resposta também irão incluir definições de índice.

![URL de pedido do postman e cabeçalho](media/search-get-started-postman/postman-url.png "URL de pedido do Postman e cabeçalho")

## <a name="1---create-an-index"></a>1 - Criar um índice

No Azure Search, normalmente, crie o índice antes de carregá-lo com dados. O [criar API REST do índice](https://docs.microsoft.com/rest/api/searchservice/create-index) é utilizado para esta tarefa. 

O URL é expandido para incluir o `hotels` nome do índice.

Para fazer isso no Postman:

1. Altere o verbo para **colocar**.

2. Copie este URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. Fornece a definição de índice (código de prontas para a cópia é apresentado abaixo) no corpo do pedido.

4. Clique em **enviar**.

![Documento de índice JSON no corpo do pedido](media/search-get-started-postman/postman-request.png "documento JSON de indexação no corpo do pedido")

### <a name="index-definition"></a>Definição do índice

A coleção de campos define a estrutura do documento. Cada documento tem de ter estes campos e cada campo tem de ter um tipo de dados. Os campos de cadeia são utilizados em pesquisas em texto completo, pelo que pode transmitir os dados numéricos como cadeias se quiser que esse conteúdo seja pesquisável.

Os atributos no campo determinam a ação permitida. As APIs REST permitem muitas ações, por predefinição. Por exemplo, todas as cadeias são pesquisáveis, recuperáveis, filtráveis e facetáveis por predefinição. Muitas vezes, só tem de definir atributos quando tem de desativar um comportamento.

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

## <a name="2---load-documents"></a>2 - carregar documentos

A criação e o preenchimento do índice são dois passos distintos. No Azure Search, o índice contém todos os dados pesquisáveis, que pode fornecer como documentos JSON. O [adicionar, atualizar ou eliminar API de REST de documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) é utilizado para esta tarefa. 

O URL é expandido para incluir o `docs` coleções e `index` operação.

Para fazer isso no Postman:

1. Altere o verbo para **POST**.

2. Copie este URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Forneça os documentos JSON (código preparado para cópia vem a seguir) no corpo do pedido.

4. Clique em **enviar**.

![Documentos JSON no corpo do pedido](media/search-get-started-postman/postman-docs.png "documentos JSON no corpo do pedido")

### <a name="json-documents-to-load-into-the-index"></a>Documentos JSON para carregar para o índice

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

Em alguns segundos, deverá ver uma resposta HTTP 201 na lista de sessão. Isto indica que os documentos foram criados com êxito. 

Se obtiver um 207, pelo menos um documento falhou ao carregar. Se obtiver um 404, tem um erro de sintaxe no cabeçalho ou no corpo do pedido. Confirme que alterou o ponto final, de modo a incluir `/docs/index`.

> [!Tip]
> Em determinadas origens de dados, pode escolher a abordagem de *indexador* alternativa, que simplifica e reduz a quantidade de código necessário para a indexação. Para obter mais informações, veja [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Operações do indexador).


## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Agora que um índice e os documentos são carregados, pode emitir consultas contra eles usando [API REST do Search documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents).

O URL é expandido para incluir uma expressão de consulta, especificada com o operador de pesquisa.

Para fazer isso no Postman:

1. Altere o verbo para **obter**.

2. Copie este URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Clique em **enviar**.

Esta consulta está vazio e devolve uma contagem dos documentos nos resultados da pesquisa. O pedido e resposta devem ser semelhantes à seguinte captura de ecrã do Postman depois de clicar em **enviar**. O código de estado deve ser 200.

 ![OBTER com a cadeia de pesquisa no URL](media/search-get-started-postman/postman-query.png "obter com a cadeia de pesquisa no URL")

Experimente alguns outros exemplos de consulta para ter uma noção do que a sintaxe. Pode fazer uma pesquisa de cadeia de caracteres, consultas de $filter textuais, limitar o conjunto de resultados, o escopo a pesquisa a campos específicos e muito mais.

Troca o atual URL com aqueles abaixo, clicar **enviar** cada vez para ver os resultados.

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

## <a name="get-index-properties"></a>Obter as propriedades do índice
Também pode utilizar [obter estatísticas](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) consultar contagens de documentos e tamanho de índice: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06`
```

Adicionar `/stats` ao seu URL retorna informações de índice. No Postman, o pedido deve ter um aspeto semelhante ao seguinte e a resposta inclui uma contagem de documentos e o espaço utilizado em bytes.

 ![Obtenha informações de índice](media/search-get-started-postman/postman-system-query.png "obter informações de índice")

Repare que a sintaxe da versão de api é diferente. Para este pedido, utilize `?` para acrescentar a versão de api. O `?` separa o caminho do URL da cadeia de consulta, ao passo que & separa cada ' name = value' par na cadeia de consulta. Nesta consulta, a versão de api é o primeiro e único item na cadeia de consulta.

## <a name="clean-up"></a>Limpeza

Quando está trabalhando na sua própria subscrição, é uma boa idéia no final de um projeto para identificar se ainda precisa que os recursos que criou. Pode executar esquerda de recursos custa dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode localizar e gerir recursos no portal, utilizando o **todos os recursos** ou **grupos de recursos** ligação no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e origens de dados. Pode eliminar os itens individuais no portal para se manter sob o limite. 

## <a name="next-steps"></a>Passos Seguintes

Os clientes REST são indispensáveis para explorações improvisadas, mas agora que já sabe como é que as APIs REST funcionam, pode avançar com código. Para o próximo passo, consulte a seguinte hiperligação:

+ [Quickstart: Criar um índice com o .NET SDK](search-get-started-dotnet.md)
