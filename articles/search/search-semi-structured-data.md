---
title: 'Tutorial: Indexar dados semiestruturados strutured em blobs JSON - Azure Search'
description: Aprenda a indexar e pesquisar semiestruturados blobs de JSON do Azure com as APIs de REST do Azure Search e Postman.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4b996effbc03bd1f7c446965b0aa5fb6fa2d0175
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024376"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>Tutorial REST: Indexar e pesquisar dados semiestruturados (JSON blobs) no Azure Search

O Azure Search pode indexar documentos JSON e matrizes no armazenamento de Blobs do Azure com um [indexador](search-indexer-overview.md) que sabe como ler dados semiestruturados. Os dados semiestruturados contêm etiquetas ou marcações que separam o conteúdo dentro dos dados. Ele divide a diferença entre dados não estruturados, o que devem ser totalmente indexadas e estruturados formalmente dados que seguem um modelo de dados, como um esquema de banco de dados relacional, que pode ser indexado numa base por campo.

Neste tutorial, utilize o [as APIs de REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/) e um cliente REST para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma origem de dados do Azure Search para um contentor de blobs do Azure
> * Criar um índice da Azure Search para conter o conteúdo pesquisável
> * Configurar e executar um indexador para ler o contentor e extrair conteúdo pesquisável de armazenamento de Blobs do Azure
> * Pesquisar o índice que acabou de criar

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste início rápido. 

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial. 

[Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo.

[Aplicação de ambiente de trabalho postman](https://www.getpostman.com/) para enviar pedidos para o Azure Search.

[Json.zip de versões de avaliação clínica](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contém os dados utilizados neste tutorial. Transfira e deszipe esse arquivo para a sua própria pasta. Dados são originados [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), convertido em JSON para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

![Obter uma chave de acesso e de ponto final HTTP](media/search-fiddler/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

1. [Inicie sessão no portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento do Azure, clique em **Blobs**e, em seguida, clique em **+ contentor**.

1. [Criar um contentor de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Pode definir o nível de acesso público a qualquer um dos respetivos valores válidos.

1. Depois do contentor é criado, abra-o e selecione **carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "carregar na barra de comando")

1. Navegue para a pasta que contém os ficheiros de exemplo. Selecione todos eles e, em seguida, clique em **carregar**.

   ![Carregar ficheiros](media/search-semi-structured-data/clinicalupload.png "carregar ficheiros")

Quando o carregamento estiver concluído, os ficheiros devem aparecer na sua própria subpasta dentro do contentor de dados.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure um pedido de HTTP. Se não estiver familiarizado com essa ferramenta, consulte [Explore pesquisa APIs REST do Azure com o Postman](search-fiddler.md).

O método de pedido para todas as chamadas neste tutorial é **POST**. As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e a sua "admin key" (a chave de administração é um marcador de posição para a sua chave primária de pesquisa), respetivamente. O corpo é onde vai colocar o conteúdo efetivo da chamada. Consoante o cliente que estiver a utilizar, poderão existir algumas variações em relação à forma como constrói a sua consulta, mas estas são as essenciais.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/postmanoverview.png)

Estamos a utilizar o Postman para fazer três chamadas à API para o serviço de pesquisa para criar uma origem de dados, um índice e um indexador. A origem de dados inclui um ponteiro para a sua conta de armazenamento e os dados JSON. O serviço de pesquisa faz a ligação ao carregar os dados.

Consulta de cadeias de caracteres tem de especificar uma versão de api e cada chamada deve retornar um **201 criado**. A versão de api em disponibilidade geral para usar matrizes JSON é `2019-05-06`.

Execute as três chamadas à API seguintes a partir do seu cliente REST.

## <a name="create-a-data-source"></a>Criar uma origem de dados

O [API de origem de dados criar](https://docs.microsoft.com/rest/api/searchservice/create-data-source)cria um objeto de Azure Search que especifica quais os dados para o índice.

O ponto final desta chamada é `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Substitua `[service name]` pelo nome do seu serviço de pesquisa. 

Para esta chamada, o corpo do pedido tem de incluir o nome da sua conta de armazenamento, a chave da conta de armazenamento e o nome do contentor de Blobs. Pode encontrar a chave da conta de armazenamento no portal do Azure, dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é apresentada na imagem seguinte:

  ![Pesquisa semiestruturada](media/search-semi-structured-data/storagekeys.png)

Certifique-se de substituir `[storage account name]`, `[storage account key]`, e `[blob container name]` no corpo da sua chamada antes de executar a chamada.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Criar um índice
    
A segunda está [criar o índice API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), criar um índice da Azure Search que armazena todos os dados pesquisáveis. Um índice especifica todos os parâmetros e os respetivos atributos.

O URL para esta chamada é `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

Primeiro, substitua o URL. Em seguida, copie e cole o seguinte código para o corpo e execute a consulta.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Criar e executar um indexador

Um indexador liga-se a origem de dados, importa dados para o índice de pesquisa de destino e, opcionalmente, fornece uma agenda para automatizar a atualização de dados. A API de REST está [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

O URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

Primeiro, substitua o URL. Em seguida, copie e cole o seguinte código para o corpo e enviar o pedido. O pedido é processado imediatamente. Quando a resposta está prestes a voltar, terá um índice que é o texto completo pesquisável.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Pesquisar os ficheiros JSON

Pode começar a procurar assim que o primeiro documento é carregado. Para esta tarefa, utilize [ **Explorador de pesquisa** ](search-explorer.md) no portal.

No portal do Azure, abra o serviço de pesquisa **descrição geral** página, localizar o índice que criou o **índices** lista.

Certifique-se de que escolher o índice que acabou de criar. 

  ![Pesquisa não estruturada](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Pesquisa de metadados definidos pelo utilizador

Tal como antes, os dados podem ser consultados de várias formas: pesquisa em texto completo, propriedades do sistema ou metadados definidos pelo utilizador. Tanto as propriedades do sistema como os metadados definidos pelo utilizador só podem ser pesquisados com o parâmetro `$select` se tiverem sido marcados como **recuperável** durante a criação do índice de destino. Os parâmetros no índice não podem ser alterados depois de serem criados. No entanto, podem ser adicionados mais parâmetros.

Um exemplo de uma consulta básica é `$select=Gender,metadata_storage_size`, que limita os resultados devolvidos a esses dois parâmetros.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/lastquery.png)

Um exemplo de uma consulta mais complexa seria `$filter=MinimumAge ge 30 and MaximumAge lt 75`, que devolve apenas resultados quando o parâmetro MinimumAge é maior ou igual a 30 e o parâmetro MaximumAge é menor que 75.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/metadatashort.png)

Se quiser experimentar e tentar fazer sozinho mais algumas consultas, pode fazê-lo. Sabe que pode utilizar os operadores lógicos (and, or, not) e os operadores de comparação (eq, ne, gt, lt, ge, le). As comparações de cadeias são sensíveis às maiúsculas e minúsculas.

O parâmetro `$filter` só funciona com os metadados que foram marcados como «filtrável» na criação do seu índice.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar após o tutorial é eliminar o grupo de recursos que contém o serviço Azure Search. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. No portal, o nome do grupo de recursos está na página Descrição Geral do serviço Azure Search.

## <a name="next-steps"></a>Passos Seguintes

Existem várias abordagens e várias opções para indexar JSON blobs. Como passo seguinte, revisar e testar as várias opções para ver o que funciona melhor para o seu cenário.

> [!div class="nextstepaction"]
> [Como indexar blobs JSON com o indexador Blob do Azure Search](search-howto-index-json-blobs.md)