---
title: 'Tutorial: Dados semi-estruturados do índice nas bolhas JSON'
titleSuffix: Azure Cognitive Search
description: Aprenda a indexar e pesquisar bolhas Azure JSON semi-estruturadas usando APIs de pesquisa cognitiva azure e carteiro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0603ad1fbecf33e5880fd7f18d35af51795f8e39
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251996"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-cognitive-search"></a>TUTORDE REST: Index e pesquisa de dados semi-estruturados (bolhas JSON) em Pesquisa Cognitiva Azure

A Pesquisa Cognitiva Azure pode indexar documentos e matrizes JSON no armazenamento de blob Azure usando um [indexante](search-indexer-overview.md) que sabe ler dados semi-estruturados. Os dados semiestruturados contêm etiquetas ou marcações que separam o conteúdo dentro dos dados. Divide a diferença entre dados não estruturados, que devem ser totalmente indexados, e dados formalmente estruturados que aderem a um modelo de dados, como um esquema de base de dados relacional, que pode ser indexado numa base por campo.

Neste tutorial, utilize as APIs de [REPOUSO de Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/) e um cliente REST para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configure uma fonte de dados de pesquisa cognitiva Azure para um recipiente de blob Azure
> * Crie um índice de pesquisa cognitiva Azure para conter conteúdo pesquisável
> * Configure e execute um indexante para ler o recipiente e extrair conteúdo pesquisável do armazenamento de blob Azure
> * Pesquisar o índice que acabou de criar

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste arranque rápido. 

[Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Você pode usar um serviço gratuito para este tutorial. 

[Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) de armazenamento Azure para armazenar os dados da amostra.

Aplicativo de [secretária postman](https://www.getpostman.com/) para envio de pedidos para Pesquisa Cognitiva Azure.

[Os ensaios clínicos-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contém os dados utilizados neste tutorial. Descarregue e desaperte este ficheiro para a sua própria pasta. Os dados são originários de [clinicaltrials.gov,](https://clinicaltrials.gov/ct2/results)convertidos para JSON para este tutorial.

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="prepare-sample-data"></a>Preparar dados da amostra

1. [Inscreva-se no portal Azure,](https://portal.azure.com)navegue na sua conta de armazenamento Azure, clique em **Blobs,** e depois clique em **+ Recipiente**.

1. [Crie um recipiente Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados da amostra. Pode definir o Nível de Acesso Público a qualquer um dos seus valores válidos.

1. Depois de criado o recipiente, abra-o e selecione **Upload** na barra de comando.

   ![Upload na barra de comando](media/search-semi-structured-data/upload-command-bar.png "Upload na barra de comando")

1. Navegue na pasta que contém os ficheiros da amostra. Selecione todos e, em seguida, clique em **Carregar**.

   ![Carregar ficheiros](media/search-semi-structured-data/clinicalupload.png "Carregar ficheiros")

Quando o carregamento estiver concluído, os ficheiros devem aparecer na sua própria subpasta dentro do contentor de dados.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure um pedido de HTTP. Se não estiver familiarizado com esta ferramenta, consulte [a Explore Azure Cognitive Search REST APIs utilizando o Carteiro](search-get-started-postman.md).

O método de pedido para cada chamada neste tutorial é **POST**. As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e a sua "admin key" (a chave de administração é um marcador de posição para a sua chave primária de pesquisa), respetivamente. O corpo é onde vai colocar o conteúdo efetivo da chamada. Consoante o cliente que estiver a utilizar, poderão existir algumas variações em relação à forma como constrói a sua consulta, mas estas são as essenciais.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/postmanoverview.png)

Estamos a utilizar o Postman para fazer três chamadas à API para o serviço de pesquisa para criar uma origem de dados, um índice e um indexador. A origem de dados inclui um ponteiro para a sua conta de armazenamento e os dados JSON. O serviço de pesquisa faz a ligação ao carregar os dados.

As cordas de consulta devem especificar uma versão api e cada chamada deve devolver um **201 Criado**. A versão api geralmente disponível para a utilização de matrizes JSON é `2019-05-06`.

Execute as três chamadas à API seguintes a partir do seu cliente REST.

## <a name="create-a-data-source"></a>Criar uma origem de dados

A [Create Data Source API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) cria um objeto de pesquisa cognitiva Azure que especifica quais dados indexar.

O ponto final desta chamada é `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Substitua `[service name]` pelo nome do seu serviço de pesquisa. 

Para esta chamada, o organismo de pedido deve incluir o nome da sua conta de armazenamento, chave da conta de armazenamento e nome do recipiente blob. Pode encontrar a chave da conta de armazenamento no portal do Azure, dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é apresentada na imagem seguinte:

  ![Pesquisa semiestruturada](media/search-semi-structured-data/storagekeys.png)

Certifique-se de substituir `[storage account name]`, `[storage account key]`e `[blob container name]` no corpo da sua chamada antes de executar a chamada.

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
    
A segunda chamada é [Create Index API,](https://docs.microsoft.com/rest/api/searchservice/create-index)criando um índice de Pesquisa Cognitiva Azure que armazena todos os dados pesquisáveis. Um índice especifica todos os parâmetros e os respetivos atributos.

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

## <a name="create-and-run-an-indexer"></a>Criar e executar um indexante

Um indexante liga a fonte de dados, importa dados para o índice de pesquisa alvo, e opcionalmente fornece um horário para automatizar a atualização de dados. A API REST é [Criar Indexer.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

O URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

Primeiro, substitua o URL. Em seguida, copie e cole o seguinte código no seu corpo e envie o pedido. O pedido é processado imediatamente. Quando a resposta voltar, terá um índice que é pesquisável por texto completo.

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

Pode começar a procurar assim que o primeiro documento estiver carregado. Para esta tarefa, utilize o [**explorador de pesquisa**](search-explorer.md) no portal.

No portal Azure, abra a página de **visão geral** do serviço de pesquisa, encontre o índice que criou na lista de **Índices.**

Certifique-se de escolher o índice que acabou de criar. 

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

A forma mais rápida de limpar depois de um tutorial é apagando o grupo de recursos que contém o serviço de Pesquisa Cognitiva Azure. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. No portal, o nome do grupo de recursos está na página geral do serviço de Pesquisa Cognitiva Azure.

## <a name="next-steps"></a>Passos seguintes

Existem várias abordagens e múltiplas opções para indexar bolhas JSON. Como próximo passo, reveja e teste as várias opções para ver o que funciona melhor para o seu cenário.

> [!div class="nextstepaction"]
> [Como indexar as bolhas JSON usando o indexante de blob de pesquisa cognitiva Azure](search-howto-index-json-blobs.md)
