---
title: 'Quickstart: Começar com a Azure Cognitive Search usando o Código do Estúdio Visual'
titleSuffix: Azure Cognitive Search
description: Aprenda a instalar e a utilizar a extensão visual studio code para a pesquisa cognitiva do Azure.
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/10/2021
ms.openlocfilehash: 3237a32a90e3964644ff84958a065656cdf7f3ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015850"
---
# <a name="get-started-with-azure-cognitive-search-using-visual-studio-code"></a>Começar com a Azure Cognitive Search usando o Código do Estúdio Visual

Este artigo explica como formular pedidos de API rest interactivemente usando as APIs de [REPOUSO DE PESQUISA Cognitiva Azure](/rest/api/searchservice) e Código do Estúdio [Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch). Com a extensão de [código do Estúdio Visual para Azure Cognitive Search (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) e estas instruções, pode enviar pedidos e visualizar respostas antes de escrever qualquer código.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT] 
> Esta funcionalidade encontra-se atualmente em visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este arranque rápido. 

+ [Visual Studio Code](https://code.visualstudio.com/download)

+ [Pesquisa cognitiva Azure para Código do Estúdio Visual (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

## <a name="install-the-extension"></a>Instalar a extensão

Comece por abrir o [Código VS.](https://code.visualstudio.com) Selecione o **separador Extensões** na barra de atividade e, em seguida, procure *a Azure Cognitive Search*. Encontre a extensão nos resultados da pesquisa e selecione **Instalar**.

![Painel de extensão do código VS](media/search-get-started-rest/download-extension.png "Descarregar a extensão do Código VS")

Em alternativa, pode instalar a [extensão de Pesquisa Cognitiva Azure](https://aka.ms/vscode-search) a partir do mercado do Código VS num navegador web.

Devia ver um novo separador Azure aparecer na barra de atividades se ainda não o tivesse.

![Painel vs código Azure](media/search-get-started-rest/azure-pane.png "Painel de azure no código VS")

## <a name="connect-to-your-subscription"></a>Estabelecer a ligação à subscrição

Selecione **Iniciar sessão no Azure...** e inicie sessão na sua Conta Azure.

Deve ver as suas assinaturas aparecerem. Selecione a subscrição para ver uma lista dos serviços de pesquisa na subscrição.

![Assinaturas VS Code Azure](media/search-get-started-rest/subscriptions.png "Assinaturas em Código VS")

Para limitar as subscrições apresentadas, abra a paleta de comandos (Ctrl+Shift+P ou Cmd+Shift+P) e procure por *Azure* ou *Selecione Subscrições*. Existem também comandos disponíveis para iniciar sessão dentro e fora da sua conta Azure.

Quando expandir o serviço de pesquisa, verá itens de árvores para cada um dos recursos de Pesquisa Cognitiva: índices, fontes de dados, indexadores, skillsets e mapas de sinónimo.

![VS Código Azure árvore de pesquisa](media/search-get-started-rest/search-tree.png "VS Código Azure árvore de pesquisa")

Estes itens de árvore podem ser expandidos para mostrar todos os recursos que você tem no seu serviço de pesquisa

## <a name="1---create-an-index"></a>1 - Criar um índice

Para começar com a Azure Cognitive Search, primeiro precisa de criar um índice de pesquisa. Isto é feito usando a [API do Índice de Criação.](/rest/api/searchservice/create-index) 

Com a extensão do Código VS, só precisa de se preocupar com o corpo do pedido. Para este arranque rápido, fornecemos uma definição de índice de amostra e documentos correspondentes.

### <a name="index-definition"></a>Definição do índice

A definição de índice abaixo é um esquema de amostra para hotéis fictícios.

A `fields` coleção define a estrutura dos documentos no índice de pesquisa. Cada campo tem um tipo de dados e uma série de atributos adicionais que determinam como o campo pode ser usado.

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Para criar um novo índice, clique com o botão direito nos **Índices** e, em seguida, **selecione Criar novo índice**. Um editor com um nome semelhante `indexes-new-28c972f661.azsindex` vai aparecer. 

Cole a definição de índice de cima para a janela. Guarde o ficheiro e **selecione Upload** quando solicitado se pretender atualizar o índice. Isto irá criar o índice e estará disponível na vista da árvore.

![Gif de criar um índice](media/search-get-started-rest/create-index.gif)

Se houver algum problema com a definição de índice, deve ver uma mensagem de erro a aparecer explicando o erro.

![Criar mensagem de erro de índice](media/search-get-started-rest/create-index-error.png)

Se isto acontecer, corrija o problema e ressate o ficheiro.

## <a name="2---load-documents"></a>2 - Documentos de carga

A criação e o preenchimento do índice são dois passos distintos. Na Pesquisa Cognitiva Azure, o índice contém todos os dados pesmáveis. Neste cenário, os dados são fornecidos como documentos JSON. A [AAPI de Adicionar, Atualizar ou Eliminar Documentos REST](/rest/api/searchservice/addupdate-or-delete-documents) É utilizada para esta tarefa. 

Para adicionar novos documentos no Código VS:

1. Expandir o `hotels-quickstart` índice que criou. Clique com o botão direito em **Documentos** e **selecione Criar novo documento.**

    ![Criar um documento](media/search-get-started-rest/create-document.png)

2. Isto abrirá um editor da JSON que indicou o esquema do seu índice.

    ![Criar um documento json](media/search-get-started-rest/create-document-2.png)

3. Cole no JSON abaixo e, em seguida, guarde o ficheiro. Um aviso abrir-se-á pedindo-lhe que confirme as suas alterações. Selecione **Upload** para guardar as alterações.

    ```json
    {
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
        } 
    }
    ```

4. Repita este processo para os três documentos restantes

    Documento 2:
    ```json
    {
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
        } 
    }
    ```

    Documento 3:
    ```json
    {
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        } 
    }
    ```

    Documento 4:
    ```json
    {
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
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
    ```

Neste momento, deverá ver os quatro documentos disponíveis na secção de documentos.

![estado após o upload de todos os documentos](media/search-get-started-rest/create-document-finish.png)

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Agora que o índice e o conjunto de documentos estão carregados, pode emitir consultas contra eles usando [documentos de pesquisa REST API](/rest/api/searchservice/search-documents).

Para fazê-lo no Código VS:

1. Clique com o botão direito no índice que pretende pesquisar e selecione **Índice de Pesquisa**. Isto abrirá um editor com um nome semelhante a `sandbox-b946dcda48.azs` .

    ![vista de pesquisa de extensão](media/search-get-started-rest/search-vscode.png)

2. Uma simples consulta é autopovoada. Prima **Ctrl+Alt+R** ou **Cmd+Alt+R** para submeter a consulta. Verá os resultados aparecerem numa janela à esquerda.

    ![resultados de pesquisa em extensão](media/search-get-started-rest/search-results.png)


### <a name="example-queries"></a>Consultas de exemplo

Experimente outros exemplos de consulta para sentir a sintaxe. Há quatro consultas adicionais abaixo para que possa tentar. Pode adicionar várias consultas ao mesmo editor. Quando premir **Ctrl+Alt+R** ou **Cmd+Alt+R,** a linha que o cursor determina qual a consulta que será submetida.

![consultas e resultados lado a lado](media/search-get-started-rest/all-searches.png)

Na primeira consulta, procuramos `boutique` e `select` apenas certos campos. É uma boa prática apenas para `select` os campos de que precisa, porque retirar dados desnecessários pode adicionar latência às suas consultas. A consulta também define `$count=true` para devolver o número total de resultados com os resultados da pesquisa.

```
// Query example 1 - Search `boutique` with select and return count
search=boutique&$count=true&$select=HotelId,HotelName,Rating,Category
```

Na próxima consulta, especificamos o termo de pesquisa `wifi` e também incluímos um filtro para apenas devolver resultados em que o estado é igual a `'FL'` . Os resultados também são encomendados pelo `Rating` Hotel.

```
// Query example 2 - Search with filter, orderBy, select, and count
search=wifi&$filter=Address/StateProvince eq 'FL'&$select=HotelId,HotelName,Rating&$orderby=Rating desc
```

Em seguida, a pesquisa é limitada a um único campo pesmável usando o `searchFields` parâmetro. Esta é uma ótima opção para tornar a sua consulta mais eficiente se você sabe que você só está interessado em jogos em determinados campos.

```
// Query example 3 - Limit searchFields
search=submlime cliff&$select=HotelId,HotelName,Rating&searchFields=HotelName
```

Outra opção comum a incluir numa consulta `facets` é. As facetas permitem-lhe construir filtros na sua UI para facilitar aos utilizadores saberem a que valores podem filtrar.

```
// Query example 4 - Take the top two results, and show only HotelName and Category in the results
search=*&$select=HotelId,HotelName,Rating&searchFields=HotelName&facet=Category
```

## <a name="open-index-in-the-portal"></a>Índice aberto no portal

Se quiser ver o seu serviço de pesquisa no portal, clique com o botão direito no nome do serviço de pesquisa e selecione **Abrir no Portal**. Isto irá levá-lo ao serviço de pesquisa no portal Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe executar tarefas fundamentais, pode avançar com pedidos adicionais de API rest para funcionalidades mais avançadas, como indexantes ou [criação de um pipeline de enriquecimento](cognitive-search-tutorial-blob.md) que adicione transformações de conteúdo à indexação. Para o seu próximo passo, recomendamos o seguinte link:

> [!div class="nextstepaction"]
> [Tutorial: Use REST e IA para gerar conteúdo pesmável a partir de bolhas Azure](cognitive-search-tutorial-blob.md)