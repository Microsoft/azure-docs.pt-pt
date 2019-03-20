---
title: Tutorial de pesquisa JSON no armazenamento de Blobs do Azure - Azure Search
description: Neste tutorial, aprenda a pesquisar dados semiestruturados e dados de Blob do Azure com o Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1c8ce14dd3961eff33a54a14c2bd0b27650d8a50
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201352"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Tutorial: Pesquisar dados semiestruturados no armazenamento na cloud do Azure

O Azure Search pode indexar documentos JSON e matrizes no armazenamento de Blobs do Azure com um [indexador](search-indexer-overview.md) que sabe como ler dados semiestruturados. Os dados semiestruturados contêm etiquetas ou marcações que separam o conteúdo dentro dos dados. Ele divide a diferença entre dados não estruturados, o que devem ser totalmente indexadas e estruturados formalmente dados que seguem um modelo de dados, como um esquema de banco de dados relacional, que pode ser indexado numa base por campo.

Neste tutorial, utilize o [as APIs de REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/) e um cliente REST para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma origem de dados do Azure Search para um contentor de blobs do Azure
> * Criar um índice da Azure Search para conter o conteúdo pesquisável
> * Configurar e executar um indexador para ler o contentor e extrair conteúdo pesquisável de armazenamento de Blobs do Azure
> * Pesquisar o índice que acabou de criar

> [!NOTE]
> Este tutorial baseia-se no suporte de matriz JSON, que é atualmente uma funcionalidade de pré-visualização no Azure Search. Não está disponível no portal. Por este motivo, estamos a utilizar a API REST de pré-visualização, que fornece esta funcionalidade, e uma ferramenta de cliente REST para chamar a API.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial.

[Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para conter dados de exemplo.

[Utilizar o Postman](https://www.getpostman.com/) ou outro cliente REST para enviar os pedidos. Instruções sobre como configurar um pedido HTTP no Postman é fornecidos na secção seguinte.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure um pedido de HTTP. Se não estiver familiarizado com essa ferramenta, consulte [Explore pesquisa APIs REST do Azure com o Postman](search-fiddler.md).

O método de pedido para todas as chamadas neste tutorial é "POST". As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e a sua "admin key" (a chave de administração é um marcador de posição para a sua chave primária de pesquisa), respetivamente. O corpo é onde vai colocar o conteúdo efetivo da chamada. Consoante o cliente que estiver a utilizar, poderão existir algumas variações em relação à forma como constrói a sua consulta, mas estas são as essenciais.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/postmanoverview.png)

Para as chamadas REST abrangidas neste tutorial, é precisa a sua api-key de pesquisa. Pode encontrar a sua api-key em **Chaves** dentro do serviço de pesquisa. Esta api-key tem de estar no cabeçalho de todas as chamadas à API (substitua "admin key" na captura de ecrã anterior pela mesma) que este tutorial lhe dá instruções para fazer. Mantenha a chave, uma vez que vai precisar dela para cada chamada.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/keys.png)

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

1. **Transfira [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** e deszipe-o para a própria pasta. Dados são originados [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), convertido em JSON para este tutorial.

2. Entrar para o [portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento do Azure, abra o **dados** contentor e clique em **carregar**.

3. Clique em **Avançadas**, introduza "clinical-trials-json" e, em seguida, carregue todos os ficheiros JSON que transferiu.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/clinicalupload.png)

Quando o carregamento estiver concluído, os ficheiros devem aparecer na sua própria subpasta dentro do contentor de dados.

## <a name="connect-your-search-service-to-your-container"></a>Ligar o serviço de pesquisa ao contentor

Estamos a utilizar o Postman para fazer três chamadas à API para o serviço de pesquisa para criar uma origem de dados, um índice e um indexador. A origem de dados inclui um ponteiro para a sua conta de armazenamento e os dados JSON. O serviço de pesquisa faz a ligação ao carregar os dados.

A cadeia de consulta tem de conter uma API de pré-visualização (como **a api-version = 2017-11-11-pré-visualização**) e cada chamada deve retornar um **201 criado**. A versão da API disponível globalmente ainda não tem a capacidade de processar json como jsonArray. Atualmente, apenas a versão da API de pré-visualização tem.

Execute as três chamadas à API seguintes a partir do seu cliente REST.

## <a name="create-a-data-source"></a>Criar uma origem de dados

Uma origem de dados é um objeto de Azure Search que especifica quais os dados para o índice.

O ponto final desta chamada é `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do seu serviço de pesquisa. Para esta chamada, precisa do nome da sua conta de armazenamento e da chave da sua conta de armazenamento. Pode encontrar a chave da conta de armazenamento no portal do Azure, dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é apresentada na imagem seguinte:

  ![Pesquisa semiestruturada](media/search-semi-structured-data/storagekeys.png)

Certifique-se de que substitui `[storage account name]` e `[storage account key]` no corpo da sua chamada antes de executar a chamada.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
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
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Criar um índice
    
A segunda chamada à API cria um índice da Azure Search. Um índice especifica todos os parâmetros e os respetivos atributos.

O URL para esta chamada é `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

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

Um indexador liga-se a origem de dados, importa dados para o índice de pesquisa de destino e, opcionalmente, fornece uma agenda para automatizar a atualização de dados.

O URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

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

Agora, pode emitir consultas contra o índice. Para esta tarefa, utilize [ **Explorador de pesquisa** ](search-explorer.md) no portal.

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

Pode anexar algoritmos com tecnologia de IA a um pipeline de indexador. Como próximo passo, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Indexar Documentos no Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)