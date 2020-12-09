---
title: 'Quickstart: Criar um índice de pesquisa em Python'
titleSuffix: Azure Cognitive Search
description: Explica como criar um índice, carregar dados e executar consultas usando Python, cadernos e os Azure.Documents. Biblioteca de pesquisa.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/19/2020
ms.custom: devx-track-python
ms.openlocfilehash: 126fc69678148d4d478c96ff8d05f194c7e3d1b3
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861872"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Quickstart: Criar um índice de pesquisa cognitiva Azure em Python usando cadernos Jupyter

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](./search-get-started-powershell.md)
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
>

Construa um Caderno Jupyter que cria, carrega e consulta um índice de Pesquisa Cognitiva Azure usando Python e a [biblioteca de documentos de pesquisa de](/python/api/overview/azure/search-documents-readme) azul no Azure SDK para Python. Este artigo explica como construir um caderno passo a passo. Em alternativa, você pode [baixar e executar um caderno Jupyter Python acabado.](https://github.com/Azure-Samples/azure-search-python-samples)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este arranque rápido.

* [Anaconda 3.x,](https://www.anaconda.com/distribution/#download-section)fornecendo Python 3.x e Jupyter Notebook.

* [pacote de documentos de pesquisa de azul](https://pypi.org/project/azure-search-documents/)

* [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar o nível Free para este arranque rápido. 

## <a name="copy-a-key-and-url"></a>Copiar uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

![Obtenha uma chave de acesso http e acesso](media/search-get-started-rest/get-url-key.png "Obtenha uma chave de acesso http e acesso")

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-cognitive-search"></a>Ligue-se à Pesquisa Cognitiva Azure

Nesta tarefa, inicie um Caderno Jupyter e verifique se pode ligar-se à Pesquisa Cognitiva do Azure. Fá-lo-á solicitando uma lista de índices do seu serviço. No Windows com Anaconda3, pode utilizar o Anaconda Navigator para lançar um portátil.

1. Crie um novo caderno Python3.

1. Na primeira célula, carregue as bibliotecas do Azure SDK para Python, incluindo [documentos de pesquisa de](/python/api/azure-search-documents)azul.

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents

    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. Na segunda célula, insira os elementos de pedido que serão constantes em cada pedido. Forneça o nome do seu serviço de pesquisa, a chave API de administração e a chave API de consulta, copiada num passo anterior. Esta célula também configura os clientes que você usará para operações específicas: [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) para criar um índice, e [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) para consultar um índice.

   ```python
    service_name = ["SEARCH_ENDPOINT - do not include search.windows.net"]
    admin_key = ["Cognitive Search Admin API Key"]

    index_name = "hotels-quickstart"

    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))

    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. Na terceira célula, executar uma operação delete_index para limpar o seu serviço de quaisquer *índices de hotéis-quickstart* existentes. A eliminação do índice *permite-lhe* criar outro índice de hotéis com o mesmo nome.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Corra cada passo.

## <a name="1---create-an-index"></a>1 - Criar um índice

Os elementos necessários de um índice incluem um nome, uma coleção de campos e uma chave. A recolha de campos define a estrutura de um documento de *pesquisa* lógico, utilizado tanto para os dados de carregamento como para os resultados de retorno. 

Cada campo tem um nome, tipo e atributos que determinam como o campo é usado (por exemplo, se é de texto completo pescável, filtrado ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos do tipo `Edm.String` deve ser designado como a *chave* para a identidade do documento.

Este índice chama-se "hotels-quickstart" e tem as definições de campo que vê abaixo. É um subconjunto de um índice de [hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) maior usado em outras caminhadas. Nós o aparámos neste quickstart para a brevidade.

1. Na célula seguinte, cole o exemplo seguinte numa célula para fornecer o esquema.

    ```python
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),

            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),

            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),

            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. Noutra cela, formula o pedido. Este create_index pedido visa a recolha de índices do seu serviço de pesquisa e cria um [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) com base no esquema de índice que forneceu na célula anterior.

   ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)

    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
   ```

1. Corra cada passo.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documentos de carga

Para carregar documentos, crie uma recolha de documentos, utilizando uma [ação de índice](/python/api/azure-search-documents/azure.search.documents.models.indexaction) para o tipo de operação (carregar, fundir e carregar, etc.). Os documentos são originários do [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) no GitHub.

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

1. Noutra cela, formula o pedido. Este upload_documents pedido visa a recolha de docs do índice hotéis-quickstart e empurra os documentos fornecidos no passo anterior para o índice de Pesquisa Cognitiva.


   ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
   ```

1. Executar cada passo para empurrar os documentos para um índice no seu serviço de pesquisa.

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Este passo mostra-lhe como consultar um índice utilizando a [API de Documentos de Pesquisa](/rest/api/searchservice/search-documents)REST .

1. Para esta operação, utilize search_client. Esta consulta executa uma pesquisa vazia `search=*` (), devolvendo uma lista não reclassiizada (pontuação de pesquisa = 1.0) de documentos arbitrários. Como não existem critérios, todos os documentos estão incluídos nos resultados. Esta consulta imprime apenas dois dos campos em cada documento. Acrescenta ainda `include_total_count=True` para obter uma contagem de todos os documentos (4) nos resultados.

   ```python
    results =  search_client.search(search_text="*", include_total_count=True)

    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
   ```

1. A próxima consulta adiciona termos inteiros à expressão de pesquisa ("wifi"). Esta consulta especifica que os resultados contêm apenas os campos na `select` declaração. Limitar os campos que voltam minimiza a quantidade de dados enviados de volta sobre o fio e reduz a latência de pesquisa.

   ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')

    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
   ```

1. Em seguida, aplique uma expressão de filtro, devolvendo apenas os hotéis com uma classificação superior a 4, classificados em ordem descendente.

   ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')

    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
   ```

1. Adicione `search_fields` à consulta de âmbito que corresponde a um único campo.

   ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')

    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
   ```

1. As facetas são etiquetas que podem ser usadas para compor a estrutura de navegação faceta. Esta consulta devolve as facetas e conta para categoria.

   ```python
    results =  search_client.search(search_text="*", facets=["Category"])

    facets = results.get_facets()

    for facet in facets["Category"]:
        print("    {}".format(facet))
   ```

1. Neste exemplo, procure um documento específico com base na sua chave. Normalmente, pretende-se devolver um documento quando um utilizador clica num documento num resultado de pesquisa.

   ```python
    result = search_client.get_document(key="3")

    print("Details for hotel '3' are:")
    print("        Name: {}".format(result["HotelName"]))
    print("      Rating: {}".format(result["Rating"]))
    print("    Category: {}".format(result["Category"]))
   ```

1. Neste exemplo, usaremos a função autocompleta. Isto é normalmente usado numa caixa de pesquisa para ajudar a completar automaticamente potenciais correspondências como os tipos de utilizador na caixa de pesquisa.

   Quando o índice foi criado, um sugestivo chamado "sg" também foi criado como parte do pedido. Uma definição sugestiva especifica quais os campos que podem ser usados para encontrar potenciais correspondências para sugestionar pedidos. Neste exemplo, esses campos são 'Tags', 'Address/City', 'Address/Country'. Para simular auto-completação, passe nas letras "sa" como uma corda parcial. O método auto-completa do [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) devolve os jogos de fim de prazo potenciais.

   ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')

    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
   ```

## <a name="clean-up"></a>Limpeza

Ao trabalhar na sua própria subscrição, depois de concluir um projeto, recomendamos que verifique se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Como simplificação, este quickstart usa uma versão abreviada do índice Hotels. Pode criar a versão completa para experimentar consultas mais interessantes. Para obter a versão completa e todos os 50 documentos, execute o assistente **de dados De importação,** selecionando *a amostra de hotéis* a partir das fontes de dados de amostra incorporadas.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice no portal Azure](search-get-started-portal.md)
