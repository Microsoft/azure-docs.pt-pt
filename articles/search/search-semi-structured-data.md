---
title: 'Tutorial de REST: Indexação de dados strutured em BLOBs JSON-Azure Search'
description: Saiba como indexar e Pesquisar BLOBs do Azure JSON semiestruturados usando Azure Search as APIs REST e o postmaster.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6362e3cccd3c306a210f0241214206204b5ee96e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840702"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>Tutorial de REST: Indexar e pesquisar dados semiestruturados (BLOBs JSON) no Azure Search

Azure Search pode indexar documentos e matrizes JSON no armazenamento de BLOBs do Azure usando um [indexador](search-indexer-overview.md) que sabe como ler dados semiestruturados. Os dados semiestruturados contêm etiquetas ou marcações que separam o conteúdo dentro dos dados. Ele divide a diferença entre dados não estruturados, que devem ser totalmente indexados e dados estruturados formalmente que aderem a um modelo de dados, como um esquema de banco de dado relacional, que pode ser indexado em uma base por campo.

Neste tutorial, use as [APIs rest Azure Search](https://docs.microsoft.com/rest/api/searchservice/) e um cliente REST para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma origem de dados do Azure Search para um contentor de blobs do Azure
> * Criar um índice de Azure Search para conter conteúdo pesquisável
> * Configurar e executar um indexador para ler o contêiner e extrair o conteúdo pesquisável do armazenamento de BLOBs do Azure
> * Pesquisar o índice que acabou de criar

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste guia de início rápido. 

[Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial. 

[Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo.

[Aplicativo de área de trabalho do postmaster](https://www.getpostman.com/) para enviar solicitações para Azure Search.

[Clinical-Trials-JSON. zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contém os dados usados neste tutorial. Baixe e descompacte esse arquivo em sua própria pasta. Os dados são provenientes de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), convertidos em JSON para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade http e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade http e uma chave de acesso")

Todas as solicitações exigem uma chave de API em cada solicitação enviada ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento do Azure, cliqueem BLOBs e, em seguida, clique em **+ contêiner**.

1. [Crie um contêiner](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) de BLOBs para conter dados de exemplo. Você pode definir o nível de acesso público para qualquer um de seus valores válidos.

1. Depois que o contêiner for criado, abra-o e selecione **carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "Carregar na barra de comandos")

1. Navegue até a pasta que contém os arquivos de exemplo. Selecione todos eles e clique em **carregar**.

   ![Carregar arquivos](media/search-semi-structured-data/clinicalupload.png "Carregar arquivos")

Quando o carregamento estiver concluído, os ficheiros devem aparecer na sua própria subpasta dentro do contentor de dados.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure um pedido de HTTP. Se você não estiver familiarizado com essa ferramenta, consulte [explorar Azure Search APIs REST usando o postmaster](search-get-started-postman.md).

O método de solicitação para cada chamada neste tutorial é **post**. As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e a sua "admin key" (a chave de administração é um marcador de posição para a sua chave primária de pesquisa), respetivamente. O corpo é onde vai colocar o conteúdo efetivo da chamada. Consoante o cliente que estiver a utilizar, poderão existir algumas variações em relação à forma como constrói a sua consulta, mas estas são as essenciais.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/postmanoverview.png)

Estamos a utilizar o Postman para fazer três chamadas à API para o serviço de pesquisa para criar uma origem de dados, um índice e um indexador. A origem de dados inclui um ponteiro para a sua conta de armazenamento e os dados JSON. O serviço de pesquisa faz a ligação ao carregar os dados.

As cadeias de caracteres de consulta devem especificar uma versão de API e cada chamada deve retornar um **201 criado**. A versão de API geralmente disponível para usar matrizes JSON `2019-05-06`é.

Execute as três chamadas à API seguintes a partir do seu cliente REST.

## <a name="create-a-data-source"></a>Criar uma origem de dados

A [API criar fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)cria um objeto Azure Search que especifica quais dados indexar.

O ponto final desta chamada é `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Substitua `[service name]` pelo nome do seu serviço de pesquisa. 

Para essa chamada, o corpo da solicitação deve incluir o nome da sua conta de armazenamento, a chave da conta de armazenamento e o nome do contêiner de BLOB. Pode encontrar a chave da conta de armazenamento no portal do Azure, dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é apresentada na imagem seguinte:

  ![Pesquisa semiestruturada](media/search-semi-structured-data/storagekeys.png)

Certifique-se de `[storage account name]`substituir `[storage account key]`, e `[blob container name]` no corpo da chamada antes de executar a chamada.

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
    
A segunda chamada é [CREATE INDEX API](https://docs.microsoft.com/rest/api/searchservice/create-indexer), criando um índice de Azure Search que armazena todos os dados pesquisáveis. Um índice especifica todos os parâmetros e os respetivos atributos.

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

Um indexador conecta a fonte de dados, importa dados para o índice de pesquisa de destino e, opcionalmente, fornece uma agenda para automatizar a atualização de dados. A API REST é [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

O URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

Primeiro, substitua o URL. Em seguida, copie e cole o código a seguir em seu corpo e envie a solicitação. A solicitação é processada imediatamente. Quando a resposta voltar, você terá um índice que é pesquisável por texto completo.

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

Você pode iniciar a pesquisa assim que o primeiro documento for carregado. Para essa tarefa, use o [**Search Explorer**](search-explorer.md) no Portal.

No portal do Azure, abra a página **visão geral** do serviço de pesquisa, localize o índice que você criou na lista **índices** .

Certifique-se de escolher o índice que você acabou de criar. 

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

Há várias abordagens e várias opções para indexação de BLOBs JSON. Como uma próxima etapa, revise e teste as várias opções para ver o que funciona melhor para seu cenário.

> [!div class="nextstepaction"]
> [Como indexar BLOBs JSON usando Azure Search indexador de BLOB](search-howto-index-json-blobs.md)
