---
title: 'Início rápido: Python e APIs REST - Azure Search'
description: Criar, carregar e consultar um índice com o Python, blocos de notas do Jupyter e a API de REST do Azure Search.
ms.date: 06/20/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 227da2739216961dcd1f2fb8c643703a1b62e51a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302285"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>Início rápido: Criar um índice da Azure Search utilizando blocos de notas do Jupyter Python
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Criar um bloco de notas do Jupyter que cria, carrega e consulta o índice da Azure Search com o Python e o [as APIs de REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Este artigo explica como criar um bloco de notas passo a passo, começando do zero. Em alternativa, pode executar um bloco de notas terminado. Para transferir uma cópia, vá para o[repositório azure-aearch-python-samples](https://github.com/Azure-Samples/azure-search-python-samples).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços e ferramentas são utilizadas neste início rápido. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), fornecendo o Python 3.x e blocos de notas do Jupyter.

+ [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode utilizar o escalão gratuito para este início rápido. 

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

![Obter uma chave de acesso e de ponto final HTTP](media/search-fiddler/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-search"></a>Ligar ao Azure Search

Nesta tarefa, iniciar um bloco de notas do Jupyter e certifique-se de que pode ligar para o Azure Search. Vai fazê-lo ao solicitar uma lista de índices do seu serviço. No Windows com Anaconda3, pode utilizar Anaconda navegador para iniciar um bloco de notas.

1. Crie um novo bloco de notas do Python3.

1. A primeira célula, carregar as bibliotecas usadas para trabalhar com JSON e formular pedidos HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Na segunda célula, os elementos de pedido que serão constantes em cada solicitação de entrada. Substitua o nome do serviço de pesquisa (seu-pesquisa-SERVICE-NAME) e a chave de API de administração (seu-ADMIN-API-KEY) com valores válidos. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. Na terceira célula, formule a solicitação. Este pedido GET destina-se a coleção de índices de seu serviço de pesquisa e seleciona a propriedade name de índices existentes.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Execute cada passo. Se existirem a índices, a resposta contém uma lista de nomes de índice. A captura de ecrã abaixo, o serviço já tem um índice de azureblob e um índice de realestate-us-sample.

   ![Script de Python no bloco de notas do Jupyter com HTTP pedidos para o Azure Search](media/search-get-started-python/connect-azure-search.png "pedidos de script de Python no bloco de notas do Jupyter com HTTP para o Azure Search")

   Por outro lado, uma coleção de índice vazio retorna essa resposta: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Criar um índice

A menos que estiver a utilizar o portal, tem de existir um índice no serviço antes de carregar dados. Este passo utiliza a [criar API REST do índice](https://docs.microsoft.com/rest/api/searchservice/create-index) para enviar um esquema de índice para o serviço.

Elementos necessários de um índice incluem um nome, uma coleção de campos e uma chave. A coleção de campos define a estrutura de um *documento*. Cada campo tem um nome, tipo e atributos que determinam como o campo é utilizado (por exemplo, se é texto completo pesquisável, filtrável ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos do tipo `Edm.String` tem de ser designado como o *chave* para a identidade do documento.

Este índice com o nome "Hotéis-quickstart" e tem as definições de campo apresentado abaixo. É um subconjunto de uma maior [índice de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) utilizado em outra orientações passo a passo. Podemos cortados neste início rápido para fins de brevidade.

1. Na próxima célula, cole o exemplo a seguir numa célula para fornecer o esquema. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Em outra célula, formule a solicitação. Isto, COLOQUE pedido destina-se a coleção de índices de seu serviço de pesquisa e cria um índice com base no esquema de índice fornecidas na célula anterior.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Execute cada passo.

   A resposta inclui a representação JSON do esquema. Captura de ecrã seguinte mostra apenas uma parte da resposta.

    ![O pedido para criar um índice](media/search-get-started-python/create-index.png "pedido para criar um índice")

> [!Tip]
> Outra forma de verificar a criação de índices é verificar a lista de índices no portal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - carregar documentos

Para enviar documentos, utilize um pedido de HTTP POST ao ponto final do URL de seu índice. A API de REST está [adicionar, atualizar ou eliminar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Documentos provêm [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) no GitHub.

1. Uma nova célula, fornece quatro documentos que estão em conformidade com o esquema de índice. Especifique uma ação de carregamento para cada documento.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
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

2. Em outra célula, formule a solicitação. Este pedido POST destina-se a coleção de documentos do índice de hotéis-quickstart e envia os documentos fornecidos no passo anterior.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Execute cada passo para enviar documentos para um índice no seu serviço de pesquisa. Os resultados devem ter um aspeto semelhantes ao seguinte exemplo. 

    ![Enviar documentos para um índice](media/search-get-started-python/load-index.png "enviar documentos para um índice")

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Este passo mostra-lhe como consultar um índice com o [API REST do Search documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Numa célula, forneça uma expressão de consulta que executa uma pesquisa em branco (pesquisa = *), retornando uma lista unranked (pontuação de pesquisa = 1,0) de documentos arbitrários. Por predefinição, o Azure Search devolve as 50 correspondências cada vez. Estruturados, como esta consulta devolve uma estrutura de todo o documento e os valores. Adicionar $count = true para obter uma contagem de todos os documentos nos resultados.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Fornece uma nova célula, o exemplo a seguir para pesquisar os termos "Hotéis" e "Wi-Fi". Adicione $select para especificar quais campos serão incluídos nos resultados da pesquisa.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Em outra célula, formule um pedido. Este pedido GET destina-se a coleção de documentos do índice de hotéis-quickstart e anexa a consulta que especificou no passo anterior.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Execute cada passo. Resultados devem ser semelhantes à saída seguinte. 

    ![Pesquisar um índice](media/search-get-started-python/search-index.png "pesquisar um índice")

1. Experimente alguns outros exemplos de consulta para ter uma noção do que a sintaxe. Pode substituir o searchstring com os exemplos seguintes e, em seguida, volte a executar o pedido de pesquisa. 

   Aplica um filtro: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Siga os dois resultados principais:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Ordenar por um campo específico:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Limpeza 

Deve excluir o índice se já não precisam dele. Um serviço gratuito está limitado a três índices. Deve excluir qualquer índice que não estiver ativamente a utilizar para criar espaço para outros tutoriais.

A maneira mais fácil eliminar objetos é através do portal, mas como esse é um guia de introdução do Python, a seguinte sintaxe produz o mesmo resultado:

   ```python
  url = endpoint + "indexes/hotels-quickstart" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Pode confirmar a eliminação do índice ao solicitar uma lista de índices existentes. Se não existe mais, hotéis-início rápido, em seguida, sabe que o seu pedido foi concluída com êxito.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>Passos Seguintes

Como uma simplificação, este início rápido utiliza uma versão abreviada do índice de hotéis. É possível criar a versão completa para experimentar o mais interessantes de consultas. Para obter a versão completa e todos os documentos de 50, execute o **importar dados** assistente, selecionando *hotéis-sample* das fontes de dados de exemplo incorporado.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice no portal do Azure](search-get-started-portal.md)
