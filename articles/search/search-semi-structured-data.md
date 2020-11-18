---
title: 'Tutorial: Dados semi-estruturados do índice em bolhas JSON'
titleSuffix: Azure Cognitive Search
description: Aprenda a indexar e pesquisar bolhas Azure JSON semi-estruturadas usando APIs e Carteiro de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 7c88aea6aff942cdcf5cbc022df8f07cfe0d4cce
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701284"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Tutorial: Blobs índice JSON do Azure Storage usando REST

A Azure Cognitive Search pode indexar documentos e matrizes JSON no armazenamento de blob Azure usando um [indexante](search-indexer-overview.md) que sabe ler dados semi-estruturados. Os dados semiestruturados contêm etiquetas ou marcações que separam o conteúdo dentro dos dados. Divide a diferença entre dados não estruturados, que devem ser totalmente indexados, e dados formalmente estruturados que aderem a um modelo de dados, como um esquema de base de dados relacional, que pode ser indexado numa base de campo.

Este tutorial utiliza o Carteiro e as [APIs search REST](/rest/api/searchservice/) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configure uma fonte de dados de pesquisa cognitiva Azure para um recipiente de bolhas Azure
> * Criar um índice de Pesquisa Cognitiva Azure para conter conteúdo pesmável
> * Configure e execute um indexante para ler o recipiente e extrair conteúdo pesmável do armazenamento de bolhas Azure
> * Pesquisar o índice que acabou de criar

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](../storage/common/storage-account-create.md)
+ [Aplicação de ambiente de trabalho Postman](https://www.getpostman.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Você pode usar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexantes e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de ter espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contém os dados utilizados neste tutorial. Descarregue e desaperte este ficheiro para a sua própria pasta. Os dados são originários de [clinicaltrials.gov,](https://clinicaltrials.gov/ct2/results)convertidos em JSON para este tutorial.

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial usa a Azure Cognitive Search para indexar e consultas, e o armazenamento de Azure Blob para fornecer os dados. 

Se possível, crie tanto na mesma região como no grupo de recursos para proximidade e gestão. Na prática, a sua conta de Armazenamento Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Comece com o armazenamento Azure

1. [Inscreva-se no portal Azure](https://portal.azure.com/) e clique **+ Criar Recurso.**

1. Procure a *conta de armazenamento* e selecione a oferta de Conta de Armazenamento da Microsoft.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Criar conta de Armazenamento" border="false":::

1. No separador Básicos, são necessários os seguintes itens. Aceite os incumprimentos para tudo o resto.

   + **Grupo de recursos**. Selecione um existente ou crie um novo, mas use o mesmo grupo para todos os serviços para que possa geri-los coletivamente.

   + **Nome da conta de armazenamento**. Se acha que pode ter múltiplos recursos do mesmo tipo, use o nome para desambiguar por tipo e região, por *exemplo, blobstoragewestus*. 

   + **Localização**. Se possível, escolha o mesmo local utilizado para a Azure Cognitive Search and Cognitive Services. Um único local anula as cargas de largura de banda.

   + **Tipo de Conta**. Escolha o predefinido, *StorageV2 (finalidade geral v2)*.

1. Clique **em 'Rever + Criar'** para criar o serviço.

1. Uma vez criado, clique em **Ir ao recurso** para abrir a página 'Vista Geral'.

1. Clique no serviço **Blobs.**

1. [Crie um recipiente Blob](../storage/blobs/storage-quickstart-blobs-portal.md) para conter os dados da amostra. Pode definir o Nível de Acesso Público a qualquer um dos seus valores válidos.

1. Depois de criado o recipiente, abra-o e selecione **upload** na barra de comando.

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="Upload na barra de comando" border="false":::

1. Navegue para a pasta que contém os ficheiros de amostragem. Selecione todos e, em seguida, clique em **Upload**.

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="Carregar ficheiros" border="false":::

Quando o carregamento estiver concluído, os ficheiros devem aparecer na sua própria subpasta dentro do contentor de dados.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O próximo recurso é a Azure Cognitive Search, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o nível Free para completar este walkthrough. 

Tal como acontece com o armazenamento da Azure Blob, aproveite um momento para recolher a chave de acesso. Mais à frente, quando iniciar os pedidos estruturantes, terá de fornecer o ponto final e a tecla api de administração utilizada para autenticar cada pedido.

### <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Obtenha uma chave de acesso http e acesso" border="false":::

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="2---set-up-postman"></a>2 - Configurar carteiro

Inicie o Postman e configure um pedido de HTTP. Se não estiver familiarizado com esta ferramenta, consulte As APIs de [Pesquisa Cognitiva explore Azure](search-get-started-rest.md).

Os métodos de pedido para cada chamada neste tutorial são **POST** e **GET.** Você fará três chamadas API para o seu serviço de pesquisa para criar uma fonte de dados, um índice e um indexante. A origem de dados inclui um ponteiro para a sua conta de armazenamento e os dados JSON. O serviço de pesquisa faz a ligação ao carregar os dados.

Em Cabeçalhos, desconfie "Content-type" `application/json` e desaver-se `api-key` com a chave api de administração do seu serviço de Pesquisa Cognitiva Azure. Uma vez definidos os cabeçalhos, pode usá-los para todos os pedidos neste exercício.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Carteiro solicita URL e cabeçalho" border="false":::

Os URIs devem especificar uma versão api e cada chamada deve devolver um **201 Criado**. A versão api geralmente disponível para a utilização de matrizes JSON é `2020-06-30` .

## <a name="3---create-a-data-source"></a>3 - Criar uma fonte de dados

A [API create Data Source](/rest/api/searchservice/create-data-source) cria um objeto de Pesquisa Cognitiva Azure que especifica quais os dados a indexar.

1. Desaponte o ponto final desta chamada para `https://[service name].search.windows.net/datasources?api-version=2020-06-30` . Substitua `[service name]` pelo nome do seu serviço de pesquisa. 

1. Copie o seguinte JSON para o corpo de pedido.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Substitua a cadeia de ligação por uma corda válida para a sua conta.

1. Substitua "[nome do recipiente blob]" pelo recipiente que criou para os dados da amostra. 

1. Envie o pedido. A resposta deve ser semelhante a:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
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

## <a name="4---create-an-index"></a>4 - Criar um índice
    
A segunda chamada é [Criar API índice,](/rest/api/searchservice/create-index)criando um índice de Pesquisa Cognitiva Azure que armazena todos os dados pes pesjáveis. Um índice especifica todos os parâmetros e os respetivos atributos.

1. Desaponte o ponto final desta chamada para `https://[service name].search.windows.net/indexes?api-version=2020-06-30` . Substitua `[service name]` pelo nome do seu serviço de pesquisa.

1. Copie o seguinte JSON para o corpo de pedido.

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
      ]
    }
   ```

1. Envie o pedido. A resposta deve ser semelhante a:

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - Criar e executar um indexante

Um indexante conecta-se à fonte de dados, importa dados para o índice de pesquisa alvo, e fornece opcionalmente um calendário para automatizar a atualização de dados. A API REST é [Criar Indexer.](/rest/api/searchservice/create-indexer)

1. Desa estada o URI para esta chamada para `https://[service name].search.windows.net/indexers?api-version=2020-06-30` . Substitua `[service name]` pelo nome do seu serviço de pesquisa.

1. Copie o seguinte JSON para o corpo de pedido.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Envie o pedido. O pedido é processado imediatamente. Quando a resposta voltar, terá um índice que é pesmável por texto completo. A resposta deve ser semelhante a:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 - Pesquisar os seus ficheiros JSON

Pode começar a procurar assim que o primeiro documento estiver carregado.

1. Mude o verbo para **GET**.

1. Desa estada o URI para esta chamada para `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true` . Substitua `[service name]` pelo nome do seu serviço de pesquisa.

1. Envie o pedido. Esta é uma consulta de pesquisa completa de texto não especificada que devolve todos os campos marcados como recuperáveis no índice, juntamente com uma contagem de documentos. A resposta deve ser semelhante a:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Adicione o `$select` parâmetro de consulta para limitar os resultados a menos campos: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true` .  Para esta consulta, 100 documentos coincidem, mas por padrão, a Azure Cognitive Search apenas devolve 50 nos resultados.

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="Consulta parametrizada" border="false":::

1. Um exemplo de consulta mais complexa incluiria `$filter=MinimumAge ge 30 and MaximumAge lt 75` , que retorna apenas resultados quando os parâmetros MinimumAge é maior ou igual a 30 e MaximumAge é inferior a 75. Substitua a `$select` expressão pela `$filter` expressão.

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="Pesquisa semiestruturada" border="false":::

Também pode utilizar operadores lógicos (e, ou não) e operadores de comparação (eq, ne, gt, lt, ge, le). As comparações de cadeias são sensíveis às maiúsculas e minúsculas. Para obter mais informações e exemplos, consulte [Criar uma consulta simples.](search-query-simple-examples.md)

> [!NOTE]
> O parâmetro `$filter` só funciona com os metadados que foram marcados como «filtrável» na criação do seu índice.

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração de design é eliminar os objetos da Azure Cognitive Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Pode utilizar o portal para eliminar índices, indexadores e fontes de dados. Ou use **DELETE** e forneça URLs a cada objeto. O seguinte comando elimina um indexante.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

O código de estado 204 é devolvido após uma eliminação com êxito.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando se está a trabalhar na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação de todos os recursos ou grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o básico da indexação Azure Blob, vamos olhar mais de perto a configuração do indexante para as bolhas JSON no Azure Storage.

> [!div class="nextstepaction"]
> [Configure a indexação da bolha JSON](search-howto-index-json-blobs.md)