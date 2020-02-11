---
title: 'Quickstart: Criar um índice de pesquisa em Python usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Explica como criar um índice, dados de carga e consultas de execução usando Python, Jupyter Notebooks e a API de Pesquisa Cognitiva Azure.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 15a3801a7ea99d6d799bcd8adf340b03f36bc196
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121613"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Quickstart: Criar um índice de pesquisa cognitiva azure em Python usando cadernos Jupyter

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Carteiro (REST)](search-get-started-postman.md)
> * [Portal](search-create-index-portal.md)
> 

Construa um caderno Jupyter que cria, carrega e consulta um índice de pesquisa cognitiva azure usando Python e o [Azure Cognitive Search REST APIs](https://docs.microsoft.com/rest/api/searchservice/). Este artigo explica como construir um caderno passo a passo. Em alternativa, você pode [baixar e executar um caderno jupyter Python acabado](https://github.com/Azure-Samples/azure-search-python-samples).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este arranque rápido. 

+ [Anaconda 3.x,](https://www.anaconda.com/distribution/#download-section)fornecendo Cadernos Python 3.x e Jupyter.

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar o free tier para este arranque rápido. 

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-cognitive-search"></a>Ligue-se à Pesquisa Cognitiva Azure

Nesta tarefa, inicie um caderno Jupyter e verifique se pode ligar-se à Pesquisa Cognitiva Azure. Vai fazer isso solicitando uma lista de índices do seu serviço. No Windows com Anaconda3, pode utilizar o Anaconda Navigator para lançar um portátil.

1. Crie um novo caderno Python3.

1. Na primeira célula, carregue as bibliotecas utilizadas para trabalhar com a JSON e formular pedidos HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Na segunda célula, insera os elementos de pedido que serão constantes em todos os pedidos. Substitua o nome do serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) e a chave API de administrador (YOUR-ADMIN-API-KEY) por valores válidos. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Se tiver `"Failed to establish a new connection"`ConnectionError, verifique se a chave api é uma chave de administração primária ou secundária e que todos os caracteres principais e de rastos (`?` e `/`) estão no lugar.

1. Na terceira célula, formula risa o pedido. Este pedido GET visa a recolha de índices do seu serviço de pesquisa e seleciona a propriedade de nome dos índices existentes.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Corre em cada passo. Se existirem índices, a resposta contém uma lista de nomes de índices. Na imagem abaixo, o serviço já tem um índice de azureblob e um índice de amostra seletiva.eua.

   ![Roteiro python em caderno Jupyter com pedidos http para Pesquisa Cognitiva Azure](media/search-get-started-python/connect-azure-search.png "Roteiro python em caderno Jupyter com pedidos http para Pesquisa Cognitiva Azure")

   Em contraste, uma coleção de índice vazio devolve esta resposta: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Criar um índice

A menos que esteja a utilizar o portal, deve existir um índice no serviço antes de poder carregar dados. Este passo utiliza a [API Create Index REST](https://docs.microsoft.com/rest/api/searchservice/create-index) para empurrar um esquema de índice para o serviço.

Os elementos necessários de um índice incluem um nome, uma coleção de campos e uma chave. A coleção de campos define a estrutura de um *documento.* Cada campo tem um nome, tipo e atributos que determinam como o campo é utilizado (por exemplo, se é pesquisável em texto completo, filtrado ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos de `Edm.String` tipo deve ser designado como a *chave* para a identidade do documento.

Este índice chama-se "hotéis-quickstart" e tem as definições de campo que você vê abaixo. É um subconjunto de um índice de [hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) maior usado em outros walkthroughs. Aparamo-lo neste início rápido para a brevidade.

1. Na célula seguinte, cola o seguinte exemplo numa célula para fornecer o esquema. 

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

2. Noutra cela, formula o pedido. Este pedido PUT visa a recolha de índices do seu serviço de pesquisa e cria um índice baseado no esquema de índice que forneceu na célula anterior.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Corre em cada passo.

   A resposta inclui a representação da JSON do esquema. A imagem que se segue mostra apenas uma parte da resposta.

    ![Pedido para criar um índice](media/search-get-started-python/create-index.png "Pedido para criar um índice")

> [!Tip]
> Outra forma de verificar a criação de índices é verificar a lista de Índices no portal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

Para empurrar documentos, utilize um pedido HTTP POST para o ponto final do URL do seu índice. O REST API é [Adicionar, Atualizar ou Eliminar Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Os documentos têm origem no [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) no GitHub.

1. Numa nova célula, forneça quatro documentos que estejam em conformidade com o esquema do índice. Especifique uma ação de upload para cada documento.

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

2. Noutra cela, formula o pedido. Este pedido do POST visa a recolha de docs do índice hotéis-quickstart e empurra os documentos fornecidos na etapa anterior.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Corra cada passo para empurrar os documentos para um índice no seu serviço de pesquisa. Os resultados devem ser semelhantes aos seguintes exemplos. 

    ![Enviar documentos para um índice](media/search-get-started-python/load-index.png "Enviar documentos para um índice")

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Este passo mostra-lhe como consultar um índice utilizando os documentos de [pesquisa REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Numa célula, forneça uma expressão de consulta que execute uma pesquisa vazia (search=*), devolvendo uma lista não classificada (pontuação de pesquisa = 1,0) de documentos arbitrários. Por padrão, a Pesquisa Cognitiva Azure devolve 50 partidas de cada vez. Tal como estruturado, esta consulta devolve toda uma estrutura e valores documentais. Adicione $count=fiel para obter uma contagem de todos os documentos nos resultados.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Numa nova célula, forneça o seguinte exemplo para pesquisar nos termos "hotéis" e "wifi". Adicione $select especificar quais os campos a incluir nos resultados da pesquisa.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Noutra cela, formula um pedido. Este pedido GET visa a recolha de docs do índice hotéis-quickstart, e anexa a consulta que especificou no passo anterior.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Corre em cada passo. Os resultados devem ser semelhantes aos seguintes resultados. 

    ![Pesquisar um índice](media/search-get-started-python/search-index.png "Pesquisar um índice")

1. Experimente outros exemplos de consulta para sentir a sintaxe. Pode substituir o `searchstring` pelos seguintes exemplos e, em seguida, reexecutar o pedido de pesquisa. 

   Aplicar um filtro: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Veja os dois primeiros resultados:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Encomenda por um campo específico:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Limpeza

Quando está a trabalhar na sua própria subscrição, é uma boa ideia no final de um projeto identificar se ainda precisa dos recursos que criou. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Como simplificação, este quickstart usa uma versão abreviada do índice Hotéis. Pode criar a versão completa para experimentar consultas mais interessantes. Para obter a versão completa e todos os 50 documentos, executar o assistente de **dados da Importação,** selecionando *a amostra de hotéis* a partir das fontes de dados de amostra incorporadas.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice no portal Azure](search-get-started-portal.md)
