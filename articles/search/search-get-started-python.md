---
title: 'Quickstart: Criar um índice de pesquisa em Python usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Explica como criar um índice, carregar dados e executar consultas usando Python, Jupyter Notebooks e a API de Pesquisa Cognitiva Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: dca53dc27eacc5c7e04bbf6cb5df82a8e8da0dfc
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694556"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Quickstart: Criar um índice de pesquisa cognitiva Azure em Python usando cadernos Jupyter

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](./search-get-started-powershell.md)
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
> 

Construa um caderno Jupyter que cria, carrega e consulta um índice de Pesquisa Cognitiva Azure usando Python e as APIs de [Repouso de Pesquisa Cognitiva Azure](/rest/api/searchservice/). Este artigo explica como construir um caderno passo a passo. Em alternativa, você pode [baixar e executar um caderno Jupyter Python acabado.](https://github.com/Azure-Samples/azure-search-python-samples)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este arranque rápido. 

+ [Anaconda 3.x,](https://www.anaconda.com/distribution/#download-section)fornecendo python 3.x e Jupyter Notebooks.

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar o nível Free para este arranque rápido. 

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

![Obtenha uma chave de acesso http e acesso](media/search-get-started-rest/get-url-key.png "Obtenha uma chave de acesso http e acesso")

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-cognitive-search"></a>Ligue-se à Pesquisa Cognitiva Azure

Nesta tarefa, inicie um caderno Jupyter e verifique se pode ligar-se à Pesquisa Cognitiva do Azure. Fá-lo-á solicitando uma lista de índices do seu serviço. No Windows com Anaconda3, pode utilizar o Anaconda Navigator para lançar um portátil.

1. Crie um novo caderno Python3.

1. Na primeira célula, carregue as bibliotecas utilizadas para trabalhar com json e formular pedidos HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Na segunda célula, insira os elementos de pedido que serão constantes em cada pedido. Substitua o nome do serviço de pesquisa (O SEU NOME DE SERVIÇO DE BUSCA) e a tecla API de administração (YOUR-ADMIN-API-KEY) por valores válidos. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2020-06-30'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Se tiver o ConnectionError, `"Failed to establish a new connection"` verifique se a chave api é uma chave de administração primária ou secundária e que todos os caracteres principais e em fuga ( e ) `?` `/` estão no lugar.

1. Na terceira cela, formula o pedido. Este pedido GET visa a recolha de índices do seu serviço de pesquisa e seleciona a propriedade de nome dos índices existentes.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Corra cada passo. Se existirem índices, a resposta contém uma lista de nomes de índice. Na imagem abaixo, o serviço já tem um índice azureblob e um índice de amostra realestate-us.

   ![Script python no caderno Jupyter com pedidos HTTP para Azure Cognitive Search](media/search-get-started-python/connect-azure-search.png "Script python no caderno Jupyter com pedidos HTTP para Azure Cognitive Search")

   Em contraste, uma coleção de índice vazio devolve esta resposta: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Criar um índice

A menos que esteja a usar o portal, deve existir um índice no serviço antes de poder carregar dados. Este passo utiliza a [API Do Índice de Criação](/rest/api/searchservice/create-index) para empurrar um esquema de índice para o serviço.

Os elementos necessários de um índice incluem um nome, uma coleção de campos e uma chave. A recolha de campos define a estrutura de um *documento.* Cada campo tem um nome, tipo e atributos que determinam como o campo é usado (por exemplo, se é de texto completo pescável, filtrado ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos do tipo `Edm.String` deve ser designado como a *chave* para a identidade do documento.

Este índice chama-se "hotels-quickstart" e tem as definições de campo que vê abaixo. É um subconjunto de um índice de [hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) maior usado em outras caminhadas. Nós o aparámos neste quickstart para a brevidade.

1. Na célula seguinte, cole o exemplo seguinte numa célula para fornecer o esquema. 

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

2. Noutra cela, formula o pedido. Este pedido de POST visa a recolha de índices do seu serviço de pesquisa e cria um índice baseado no esquema de índice que forneceu na célula anterior.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Corra cada passo.

   A resposta inclui a representação JSON do esquema. A imagem que se segue mostra apenas uma parte da resposta.

    ![Pedido de criação de um índice](media/search-get-started-python/create-index.png "Pedido de criação de um índice")

> [!Tip]
> Outra forma de verificar a criação de índices é verificar a lista de Índices no portal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documentos de carga

Para empurrar documentos, utilize um pedido HTTP POST para o ponto final url do seu índice. A API REST é [Adicionar, Atualizar ou Eliminar Documentos](/rest/api/searchservice/addupdate-or-delete-documents). Os documentos são originários do [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) no GitHub.

1. Numa nova célula, forneça quatro documentos que estejam em conformidade com o esquema de índice. Especifique uma ação de upload para cada documento.

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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
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

2. Noutra cela, formula o pedido. Este pedido de POST visa a recolha de docs do índice hotéis-quickstart e empurra os documentos fornecidos no passo anterior.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Executar cada passo para empurrar os documentos para um índice no seu serviço de pesquisa. Os resultados devem ser semelhantes ao exemplo seguinte. 

    ![Enviar documentos para um índice](media/search-get-started-python/load-index.png "Enviar documentos para um índice")

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Este passo mostra-lhe como consultar um índice utilizando a [API de Documentos de Pesquisa](/rest/api/searchservice/search-documents)REST .

1. Numa célula, forneça uma expressão de consulta que execute uma pesquisa vazia (search=*), devolvendo uma lista não reclassiizada (pontuação de pesquisa = 1.0) de documentos arbitrários. Por padrão, a Azure Cognitive Search devolve 50 partidas de cada vez. Como estruturado, esta consulta devolve toda uma estrutura documental e valores. Adicione $count=verdadeiro para obter uma contagem de todos os documentos nos resultados.

   ```python
   searchstring = '&search=*&$count=true'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Numa nova célula, forneça o seguinte exemplo para pesquisar nos termos "hotéis" e "wifi". Adicione $select para especificar quais os campos a incluir nos resultados da pesquisa.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)   
   ```

   Os resultados devem ser semelhantes à seguinte saída. 

    ![Pesquisar um índice](media/search-get-started-python/search-index.png "Pesquisar um índice")

1. Em seguida, aplique uma expressão $filter que selecione apenas os hotéis com uma classificação superior a 4. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)     
   ```

1. Por predefinição, o motor de busca devolve os 50 melhores documentos, mas pode usar a parte superior e saltar para adicionar paginação e escolher quantos documentos em cada resultado. Esta consulta devolve dois documentos em cada conjunto de resultados.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Neste último exemplo, use $orderby para classificar resultados por cidade. Este exemplo inclui campos da coleção Address.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

## <a name="clean-up"></a>Limpeza

Ao trabalhar na sua própria subscrição, depois de concluir um projeto, recomendamos que verifique se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Como simplificação, este quickstart usa uma versão abreviada do índice Hotels. Pode criar a versão completa para experimentar consultas mais interessantes. Para obter a versão completa e todos os 50 documentos, execute o assistente **de dados De importação,** selecionando *a amostra de hotéis* a partir das fontes de dados de amostra incorporadas.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice no portal Azure](search-get-started-portal.md)