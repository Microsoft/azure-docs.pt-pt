---
title: Tutorial de pesquisa JSON no armazenamento de Blobs do Azure - Azure Search
description: Neste tutorial, aprenda a pesquisar dados semiestruturados e dados de Blob do Azure com o Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 44c818cba760fb5cd7d496fd45ea321ef38248f3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445097"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Tutorial: Pesquisar dados semiestruturados no armazenamento na cloud do Azure

Numa série de tutoriais de duas partes, vai aprender a pesquisar dados semiestruturados e não estruturados com o Azure Search. A [Parte 1](../storage/blobs/storage-unstructured-search.md) explicou a pesquisa de dados não estruturados, mas também incluiu pré-requisitos importantes para este tutorial, como criar a conta de armazenamento. 

Na Parte 2, o foco muda para os dados semiestruturados, tais como JSON, armazenados nos blobs do Azure. Os dados semiestruturados contêm etiquetas ou marcações que separam o conteúdo dentro dos dados. Ele divide a diferença entre dados não estruturados que têm de ser indexados totalmente e estruturados formalmente dados que seguem um modelo de dados, como um esquema de banco de dados relacional, que pode ser pesquisado numa base por campo.

Na Parte 2, saiba como:

> [!div class="checklist"]
> * Configurar uma origem de dados do Azure Search para um contentor de blobs do Azure
> * Criar e preencher um índice do Azure Search e o indexador para pesquisar o contentor e extrair conteúdo pesquisável
> * Pesquisar o índice que acabou de criar

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [tutorial anterior](../storage/blobs/storage-unstructured-search.md) que fornece a conta de armazenamento e o serviço de pesquisa criado no tutorial anterior.

* Instalação de um cliente REST e compreender como se constrói um pedido de HTTP. Para este tutorial, utilizamos o [Postman](https://www.getpostman.com/). Pode utilizar outro cliente REST se já estiver familiarizado com um cliente específico.

> [!NOTE]
> Este tutorial baseia-se no suporte de matriz JSON, que é atualmente uma funcionalidade de pré-visualização no Azure Search. Não está disponível no portal. Por este motivo, estamos a utilizar a API REST de pré-visualização, que fornece esta funcionalidade, e uma ferramenta de cliente REST para chamar a API.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure um pedido de HTTP. Se não estiver familiarizado com esta ferramenta, veja [Explorar as APIs REST do Azure Search com o Fiddler ou o Postman](search-fiddler.md) para obter mais informações.

O método de pedido para todas as chamadas neste tutorial é "POST". As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e a sua "admin key" (a chave de administração é um marcador de posição para a sua chave primária de pesquisa), respetivamente. O corpo é onde vai colocar o conteúdo efetivo da chamada. Consoante o cliente que estiver a utilizar, poderão existir algumas variações em relação à forma como constrói a sua consulta, mas estas são as essenciais.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/postmanoverview.png)

Para as chamadas REST abrangidas neste tutorial, é precisa a sua api-key de pesquisa. Pode encontrar a sua api-key em **Chaves** dentro do serviço de pesquisa. Esta api-key tem de estar no cabeçalho de todas as chamadas à API (substitua "admin key" na captura de ecrã anterior pela mesma) que este tutorial lhe dá instruções para fazer. Mantenha a chave, uma vez que vai precisar dela para cada chamada.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Transferir os dados de exemplo

Um conjunto de dados de exemplo foi preparado para si. **Transfira [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** e deszipe-o para a própria pasta.

A amostra contém ficheiros JSON de exemplo, que eram originalmente ficheiros de texto obtidos a partir de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Convertemo-los para JSON para sua comodidade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="upload-the-sample-data"></a>Carregar os dados de exemplo

No portal do Azure, navegue de volta para a conta de armazenamento criada no [tutorial anterior](../storage/blobs/storage-unstructured-search.md). Em seguida, abra o contentor de **dados** e clique em **Carregar**.

Clique em **Avançadas**, introduza "clinical-trials-json" e, em seguida, carregue todos os ficheiros JSON que transferiu.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/clinicalupload.png)

Quando o carregamento estiver concluído, os ficheiros devem aparecer na sua própria subpasta dentro do contentor de dados.

## <a name="connect-your-search-service-to-your-container"></a>Ligar o serviço de pesquisa ao contentor

Estamos a utilizar o Postman para fazer três chamadas à API para o serviço de pesquisa para criar uma origem de dados, um índice e um indexador. A origem de dados inclui um ponteiro para a sua conta de armazenamento e os dados JSON. O serviço de pesquisa faz a ligação ao carregar os dados.

A cadeia de consulta tem de conter **api-version=2016-09-01-Preview** e cada chamada deve devolver um **201 Criado**. A versão da API disponível globalmente ainda não tem a capacidade de processar json como jsonArray. Atualmente, apenas a versão da API de pré-visualização tem.

Execute as três chamadas à API seguintes a partir do seu cliente REST.

### <a name="create-a-datasource"></a>Criar uma origem de dados

Uma origem de dados especifica quais os dados a indexar.

O ponto final desta chamada é `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

Para esta chamada, precisa do nome da sua conta de armazenamento e da chave da sua conta de armazenamento. Pode encontrar a chave da conta de armazenamento no portal do Azure, dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é apresentada na imagem seguinte:

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

### <a name="create-an-index"></a>Criar um índice
    
A segunda chamada à API cria um índice. Um índice especifica todos os parâmetros e os respetivos atributos.

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

### <a name="create-an-indexer"></a>Criar um indexador

Um indexador liga a origem de dados ao índice de pesquisa de destino e, opcionalmente, fornece uma agenda para automatizar a atualização de dados.

O URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

Primeiro, substitua o URL. Em seguida, copie e cole o seguinte código para o corpo e execute a consulta.

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

Agora que o serviço de pesquisa foi ligado ao contentor de dados, pode começar a pesquisar os seus ficheiros.

Abra o portal do Azure e navegue de volta para o serviço de pesquisa, tal como fez no tutorial anterior.

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