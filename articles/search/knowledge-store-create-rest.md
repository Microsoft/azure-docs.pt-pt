---
title: Criar uma loja de conhecimento usando REST
titleSuffix: Azure Cognitive Search
description: Utilize a API REST e o Carteiro para criar uma loja de conhecimentos de Pesquisa Cognitiva Azure para enriquecimentos persistentes a partir de um oleoduto de enriquecimento de IA.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/18/2020
ms.openlocfilehash: 6af9b8f97d622ae10cfdbcaa8ca50abb42ec7332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94889059"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Criar uma loja de conhecimentos usando REST e Carteiro

Uma loja de conhecimento contém saída de um oleoduto de enriquecimento de pesquisa cognitiva Azure para análise posterior ou outro processamento a jusante. Um pipeline enriquecido com IA aceita ficheiros de imagem ou ficheiros de texto não estruturados, indexa-os utilizando a Azure Cognitive Search, aplica enriquecimentos de IA dos Serviços Cognitivos (como análise de imagem e processamento de linguagem natural), e depois guarda os resultados para uma loja de conhecimento no Azure Storage. Pode utilizar ferramentas como o Power BI ou o Storage Explorer no portal Azure para explorar a loja de conhecimento.

Neste artigo, você usa a interface REST API para ingerir, indexar e aplicar enriquecimentos de IA a um conjunto de avaliações de hotéis. As avaliações do hotel são importadas para o armazém da Azure Blob. Os resultados são guardados como uma loja de conhecimento no armazenamento da Mesa Azure.

Depois de criar a loja de conhecimentos, poderá aprender como aceder à loja de conhecimentos utilizando o [Storage Explorer](knowledge-store-view-storage-explorer.md) ou o [Power BI](knowledge-store-connect-power-bi.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!TIP]
> Recomendamos [a aplicação de desktop do Carteiro](https://www.getpostman.com/) para este artigo. O [código fonte](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) deste artigo inclui uma coleção do Carteiro contendo todos os pedidos. 

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este quickstart usa Azure Cognitive Search, Azure Blob storage, e [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) para a IA. 

Como a carga de trabalho é tão pequena, os Serviços Cognitivos são aproveitados nos bastidores para fornecer processamento gratuito para até 20 transações diárias. Como o conjunto de dados é tão pequeno, pode ignorar a criação ou anexação de um recurso de Serviços Cognitivos.

1. [Descarregue HotelReviews_Free.csv. ](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D) Estes dados são dados de revisão do hotel guardados num ficheiro CSV (originários de Kaggle.com) e contém 19 peças de feedback do cliente sobre um único hotel. 

1. [Crie uma conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [encontre uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) na sua subscrição atual. Você usará o armazenamento Azure para que tanto o conteúdo bruto seja importado, como a loja de conhecimento que é o resultado final.

   Escolha o tipo de conta **StorageV2 (finalidade geral V2).**

1. Abra as páginas de serviços Blob e crie um recipiente chamado *hot-reviews*.

1. Clique em **Carregar**.

    ![Faça o upload dos dados](media/knowledge-store-create-portal/upload-command-bar.png "Faça upload das avaliações do hotel")

1. Selecione o ficheiro **HotelReviews-Free.csv** que descarregou no primeiro passo.

    ![Criar o recipiente Azure Blob](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o recipiente Azure Blob")

1. Está quase pronto com este recurso, mas antes de deixar estas páginas, use um link no painel de navegação esquerdo para abrir a página **'Chaves de Acesso'.** Obtenha uma cadeia de ligação para obter dados do armazenamento blob. Uma cadeia de ligação é semelhante ao seguinte exemplo: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Ainda no portal, mude para Azure Cognitive Search. [Crie um novo serviço](search-create-service-portal.md) ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Você pode usar um serviço gratuito para este exercício.

## <a name="configure-postman"></a>Configurar o Postman

Instale e instale o Carteiro.

### <a name="download-and-install-postman"></a>Descarregue e instale o Carteiro

1. Descarregue o [código fonte de recolha do Carteiro.](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)
1. Selecione **a Importação de**  >  **Ficheiros** para importar o código fonte para o Carteiro.
1. Selecione o separador **Coleções** e, em seguida, selecione o botão **...** (elipse).
1. Selecione **Editar**. 
   
   ![Aplicativo de carteiro mostrando navegação](media/knowledge-store-create-rest/postman-edit-menu.png "Ir ao menu Editar no Carteiro")
1. Na caixa de diálogo **Editar,** selecione o **separador Variáveis.** 

No separador **Variáveis,** pode adicionar valores que o Carteiro troca sempre que encontra uma variável específica dentro de aparelhos duplos. Por exemplo, o Carteiro substitui o símbolo `{{admin-key}}` pelo valor atual que definiu para `admin-key` . O carteiro faz a substituição em URLs, cabeçalhos, o corpo de pedido, e assim por diante. 

Para obter o valor `admin-key` para , vá ao serviço de Pesquisa Cognitiva Azure e selecione o separador **Chaves.** `search-service-name` `storage-account-name` [](#create-services-and-load-data) Desa `storage-connection-string` cos para o valor do separador Chaves de Acesso da conta de **armazenamento.** Pode deixar os padrão para os outros valores.

![Separador de variáveis de aplicativo do carteiro](media/knowledge-store-create-rest/postman-variables-window.png "Janela de variáveis do carteiro")


| Variável    | Onde obtê-lo |
|-------------|-----------------|
| `admin-key` | Na página **Chaves** do serviço de Pesquisa Cognitiva Azure.  |
| `api-version` | Sair como **2020-06-30**. |
| `datasource-name` | Saia como **hotel-reviews-ds**. | 
| `indexer-name` | Saia como **hotel-reviews-ixr**. | 
| `index-name` | Saia como **hotel-reviews-ix**. | 
| `search-service-name` | O nome do serviço de Pesquisa Cognitiva Azure. A URL `https://{{search-service-name}}.search.windows.net` é. | 
| `skillset-name` | Saia como **hotel-reviews-ss**. | 
| `storage-account-name` | O nome da conta de armazenamento Azure. | 
| `storage-connection-string` | Na conta de armazenamento, no separador **Chaves de Acesso,** selecione **a tecla1** Connection  >  **string**. | 
| `storage-container-name` | Saia como **avaliações de hotéis.** | 

### <a name="review-the-request-collection-in-postman"></a>Reveja a recolha de pedidos no Carteiro

Quando criar uma loja de conhecimento, deve emitir quatro pedidos HTTP: 

- **Pedido DE PUT para criar o índice**: Este índice contém os dados que a Azure Cognitive Search utiliza e devolve.
- **Pedido de PUBLICAÇÃO para criar a fonte de dados:** Esta fonte de dados liga o seu comportamento de Pesquisa Cognitiva Azure à conta de armazenamento da loja de dados e conhecimentos. 
- **Pedido DE PUT para criar o skillset**: O skillset especifica os enriquecimentos que são aplicados aos seus dados e à estrutura da loja de conhecimentos.
- **PUT pedido para criar o indexante**: Executar o indexante lê os dados, aplica o skillset e armazena os resultados. Tem de fazer este pedido por último.

O [código fonte](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) contém uma coleção de carteiros que tem os quatro pedidos. Para emitir os pedidos, no Carteiro, selecione o separador para o pedido. Em seguida, adicione `api-key` e `Content-Type` solicite cabeçalhos. Desa estação o valor de `api-key` `{{admin-key}}` . Desa estação o valor `Content-type` para `application/json` . 

![Screenshot mostrando a interface do carteiro para cabeçalhos](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Deve definir `api-key` e `Content-type` cabeçalhos em todos os seus pedidos. Se o Carteiro reconhecer uma variável, a variável aparece em texto laranja, como `{{admin-key}}` na imagem anterior. Se a variável estiver mal escrita, aparece em texto vermelho.
>

## <a name="create-an-azure-cognitive-search-index"></a>Criar um índice do Azure Cognitive Search

Crie um índice de Pesquisa Cognitiva Azure para representar os dados que está interessado em pesquisar, filtrar e aplicar melhorias. Crie o índice emitindo um pedido DEM para `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` . O carteiro substitui os símbolos que são incluídos em aparelhos duplos (tais `{{search-service-name}}` `{{index-name}}` como, e `{{api-version}}` ) com os valores que definiu no [Configure Postman](#configure-postman). Se utilizar uma ferramenta diferente para emitir os seus comandos REST, deve substituir essas variáveis por si mesmo.

Desconfie da estrutura do seu índice de Pesquisa Cognitiva Azure no corpo do pedido. No Carteiro, depois de definir os `api-key` `Content-type` cabeçalhos e cabeçalhos, vá para o painel do **corpo** do pedido. Devia ver o seguinte JSON. Se não o fizer, selecione **Raw**  >  **JSON (aplicação/json)** e, em seguida, cole o seguinte código como o corpo:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Esta definição de índice é uma combinação de dados que gostaria de apresentar ao utilizador (o nome do hotel, conteúdo de revisão, data), metadados de pesquisa e dados de melhoria de IA (Sentimento, Frases-Chave e Linguagem).

Selecione **Enviar** para emitir o pedido PUT. Devia ver o `201 - Created` estado. Se vir um estado diferente, no painel **do corpo,** procure uma resposta JSON que contenha uma mensagem de erro. 

## <a name="create-the-datasource"></a>Criar a fonte de dados

Em seguida, ligue a Azure Cognitive Search aos dados do hotel que armazenou no armazenamento blob. Para criar a fonte de dados, envie um pedido DEM para `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` . Tens de definir os `api-key` `Content-Type` cabeçalhos e os cabeçalhos, como discutido anteriormente. 

No Carteiro, vá ao pedido **de Criar Fonte de Dados** e, em seguida, ao painel do **corpo.** Deverá ver o seguinte código:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Selecione **Enviar** para emitir o pedido DE ENVIO. 

## <a name="create-the-skillset"></a>Criar o skillset 

O próximo passo é especificar o skillset, que especifica tanto as melhorias a aplicar como a loja de conhecimentos onde os resultados serão armazenados. No Carteiro, selecione o **separador Criar o Skillset.** Este pedido envia um PUT para `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` . Defina os `api-key` `Content-type` cabeçalhos e os cabeçalhos como fez antes. 

Existem dois grandes objetos de alto nível: `skills` e `knowledgeStore` . Cada objeto dentro do `skills` objeto é um serviço de enriquecimento. Cada serviço de enriquecimento tem `inputs` `outputs` e. O `LanguageDetectionSkill` tem uma saída de `targetName` `Language` . O valor deste nó é usado pela maioria das outras habilidades como entrada. A fonte `document/Language` é. A capacidade de utilizar a saída de um nó como entrada para outro é ainda mais evidente em `ShaperSkill` , que especifica como os dados fluem para as tabelas da loja de conhecimento.

O `knowledge_store` objeto liga-se à conta de armazenamento através da `{{storage-connection-string}}` variável Carteiro. `knowledge_store` contém um conjunto de mapeamentos entre o documento melhorado e tabelas e colunas na loja de conhecimento. 

Para gerar o skillset, selecione o botão **Enviar** no Carteiro para colocar o pedido:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Criar o indexador

O passo final é criar o indexante. O indexante lê os dados e ativa o skillset. No Carteiro, selecione o pedido **de Criar Indexer** e, em seguida, reveja o corpo. A definição do indexante refere-se a vários outros recursos que já criou: a fonte de dados, o índice e o skillset. 

O `parameters/configuration` objeto controla a forma como o indexante ingere os dados. Neste caso, os dados de entrada estão num único documento que tem uma linha de cabeçalho e valores separados por vírgula. A chave do documento é um identificador único para o documento. Antes de codificar, a chave do documento é o URL do documento de origem. Finalmente, os valores de produção de skillset, como código de linguagem, sentimento e frases-chave, são mapeados para as suas localizações no documento. Embora haja um único valor para `Language` , é aplicado a cada elemento na matriz de `Sentiment` `pages` . `Keyphrases` é uma matriz que também é aplicada a cada elemento na `pages` matriz.

Depois de definir os `api-key` `Content-type` cabeçalhos e confirmar que o corpo do pedido é semelhante ao seguinte código fonte, selecione **Enviar** o Carteiro. O carteiro envia um pedido de PUT para `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` . A Azure Cognitive Search cria e executa o indexante. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Executar o indexador 

No portal Azure, aceda à página **geral** do serviço de Pesquisa Cognitiva do Azure. Selecione o **separador Indexers** e, em seguida, selecione **hotéis-reviews-ixr**. Se o indexante ainda não tiver funcionaido, selecione **Executar**. A tarefa de indexação pode levantar alguns avisos relacionados com o reconhecimento da linguagem. Os dados incluem algumas análises que são escritas em línguas que ainda não são suportadas pelas habilidades cognitivas. 

## <a name="next-steps"></a>Passos seguintes

Agora que enriqueceu os seus dados utilizando os Serviços Cognitivos e projetou os resultados para uma loja de conhecimento, pode utilizar o Storage Explorer ou o Power BI para explorar o seu conjunto de dados enriquecido.

Para aprender a explorar esta loja de conhecimentos utilizando o Storage Explorer, consulte este walkthrough:

> [!div class="nextstepaction"]
> [Ver com o Explorador de Armazenamento](knowledge-store-view-storage-explorer.md)

Para aprender a ligar esta loja de conhecimentos ao Power BI, consulte esta passagem:

> [!div class="nextstepaction"]
> [Ligar com o Power BI](knowledge-store-connect-power-bi.md)

Se quiser repetir este exercício ou experimentar uma passagem diferente de enriquecimento de IA, elimine o indexante **de avaliações de hotéis-idxr.** A eliminação do indexante repõe o contador de transações diárias gratuitos para zero.