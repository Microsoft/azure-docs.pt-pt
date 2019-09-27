---
title: Criar uma loja de conhecimento usando REST-Azure Search
description: Crie um repositório de conhecimento Azure Search para persistir aprimoramentos do pipeline de pesquisa cognitiva, usando a API REST e o postmaster.
author: lobrien
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: laobri
ms.openlocfilehash: 50648d22a62199d27374a1cacf617858ce9e7d6a
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329275"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Criar um repositório de conhecimento Azure Search usando REST

O repositório de conhecimento é um recurso do Azure Search que persiste a saída de um pipeline de enriquecimento de ia para análise posterior ou outro processamento downstream. Um pipeline aprimorado de ia aceita arquivos de imagem ou arquivos de texto não estruturados, indexa-os usando Azure Search, aplica aprimoramentos de ia de serviços cognitivas (como análise de imagem e processamento de linguagem natural) e, em seguida, salva os resultados em uma loja de conhecimento no Azure repositório. Você pode usar ferramentas como Power BI ou Gerenciador de Armazenamento para explorar a loja de conhecimento.

Neste artigo, você usará a interface da API REST para ingerir, indexar e aplicar os aprimoramentos de AI a um conjunto de revisões de Hotel. As revisões de Hotel são importadas para o armazenamento de BLOBs do Azure e os resultados são salvos como um repositório de conhecimento no armazenamento de tabelas do Azure.

Depois de criar a loja de conhecimento, você pode aprender sobre como acessar essa loja de conhecimento usando [Gerenciador de armazenamento](knowledge-store-view-storage-explorer.md) ou [Power bi](knowledge-store-connect-power-bi.md).

## <a name="1---create-services"></a>1-criar serviços

+ [Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial.

+ [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo e a loja de conhecimento. Sua conta de armazenamento deve usar o mesmo local (como US-oeste) para seu serviço de Azure Search. O *tipo de conta* deve ser *StorageV2 (uso geral v2)* (padrão) ou *armazenamento (uso geral v1)* .

+ Recomendação: [Aplicativo de área de trabalho do postmaster](https://www.getpostman.com/) para enviar solicitações para Azure Search. Você pode usar a API REST com qualquer ferramenta capaz de trabalhar com solicitações e respostas HTTP. O postmaster é uma boa opção para explorar as APIs REST e será usado neste artigo. Além disso, o [código-fonte](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) deste artigo inclui uma coleção de solicitações do postmaster. 

## <a name="2---store-the-data"></a>2-armazenar os dados

Carregue o arquivo CSV de revisões de Hotel no armazenamento de BLOBs do Azure para que ele possa ser acessado por um indexador Azure Search e alimentado por meio do pipeline de enriquecimento de ia.

### <a name="create-an-azure-blob-container-with-the-data"></a>Criar um contêiner de blob do Azure com os dados

1. [Baixe os dados de revisão do Hotel salvos em um arquivo CSV (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Esses dados são provenientes de Kaggle.com e contêm comentários de clientes sobre hotéis.
1. [Entre no portal do Azure](https://portal.azure.com)e navegue até sua conta de armazenamento do Azure.
1. [Crie um contêiner de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Para criar o contêiner, na barra de navegação à esquerda para sua conta de armazenamento, clique em **BLOBs**e, em seguida, clique em **+ contêiner** na barra de comandos.
1. Para o novo **nome**do contêiner, `hotel-reviews`digite.
1. Selecione qualquer **nível de acesso público**. Usamos o padrão.
1. Clique em **OK** para criar o contêiner de blob do Azure.
1. Abra o novo `hotels-review` contêiner, clique em **carregar**e selecione o arquivo **HotelReviews-Free. csv** que você baixou na primeira etapa.

    ![Carregar os dados](media/knowledge-store-create-portal/upload-command-bar.png "Carregar as revisões do Hotel")

1. Clique em **carregar** para importar o arquivo CSV para o armazenamento de BLOBs do Azure. O novo contêiner será exibido.

    ![Criar o contêiner de blob do Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o contêiner de blob do Azure")

## <a name="3---configure-postman"></a>3-configurar o postmaster

Baixe o [código-fonte da coleção de postmaster](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) e importe-o para o postmaster usando **arquivo, importar..** .. Alterne para a guia **coleções** e clique no botão **...** e selecione **Editar**. 

![Aplicativo de postagem mostrando navegação](media/knowledge-store-create-rest/postman-edit-menu.png "navegar até o menu Editar no postmaster")

Na caixa de diálogo Editar resultante, navegue até a guia **variáveis** . 

A guia **variáveis** permite que você adicione valores que o postmaster alternará sempre que encontrá-los dentro de chaves duplas. Por exemplo, o postmaster substituirá o símbolo `{{admin-key}}` pelo "valor atual" do `admin-key`. O postmaster fará essa substituição nas URLs, nos cabeçalhos, no corpo da solicitação e assim por diante. 

Você encontrará o valor de `admin-key` na guia **chaves** do serviço de pesquisa. Você precisará alterar `search-service-name` e `storage-account-name` para os valores escolhidos na [etapa 1](#1---create-services). Defina `storage-connection-string` do valor na guia **chaves de acesso** da conta de armazenamento. Os outros valores que você pode deixar inalterados.

![Guia variáveis do aplicativo do postmaster](media/knowledge-store-create-rest/postman-variables-window.png "janela variáveis do postmaster")


| Variável    | Onde obtê-lo |
|-------------|-----------------|
| `admin-key` | Serviço de Pesquisa, guia **chaves**              |
| `api-version` | Deixar como "2019-05-06-Preview" |
| `datasource-name` | Deixe como "Hotel-Reviews-DS" | 
| `indexer-name` | Deixe como "Hotel-Reviews-IXR" | 
| `index-name` | Deixe como "Hotel-Reviews-IX" | 
| `search-service-name` | Serviço de Pesquisa, nome principal. A URL é `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | Deixe como "Hotel-Reviews-SS" | 
| `storage-account-name` | Conta de armazenamento, nome principal | 
| `storage-connection-string` | Conta de armazenamento, guia **chaves de acesso** , **cadeia de conexão** **key1** | 
| `storage-container-name` | Deixar como "Hotel-Reviews" | 

### <a name="review-the-request-collection-in-postman"></a>Examinar a coleção de solicitações no postmaster

Criar uma loja de conhecimento exige que você emita quatro solicitações HTTP: 

1. Uma solicitação PUT para criar o índice. Esse índice contém os dados usados e retornados por Azure Search.
1. Uma solicitação POST para criar a fonte de fontes. Essa fonte de dados conecta seu comportamento de Azure Search à conta de armazenamento de data e da loja de conhecimento. 
1. Uma solicitação PUT para criar o confirmador de habilidades. O skillset especifica os aprimoramentos aplicados aos seus dados e à estrutura da loja de conhecimento.
1. Uma solicitação PUT para criar o indexador. A execução do indexador lê os dados, aplica o qualificable e armazena os resultados. Você deve executar esta solicitação por último.

O [código-fonte](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) contém uma coleção de postmaster com essas quatro solicitações. Para emitir as solicitações, alterne para a guia da solicitação no postmaster e adicione os cabeçalhos de solicitação `api-key` e `Content-Type`. Defina o valor de `api-key` como `{{admin-key}}`. Defina o valor `Content-type` como `application/json`. 

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a interface do postmaster para os cabeçalhos @ no__t-1

> [!Note]
> Você precisará definir os cabeçalhos `api-key` e `Content-type` em todas as suas solicitações. Se uma variável for reconhecida pelo postmaster, ela será renderizada em texto laranja, assim como com `{{admin-key}}` na captura de tela. Se a variável for digitada incorretamente, ela será renderizada em texto vermelho.
>

## <a name="4---create-an-azure-search-index"></a>4-criar um índice de Azure Search

Você precisa criar um índice de Azure Search para representar os dados nos quais você está interessado em Pesquisar, filtrar e fazer aprimoramentos. Você cria o índice emitindo uma solicitação PUT para `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. O postmaster substituirá os símbolos entre as chaves duplas, como `{{search-service-name}}`, `{{index-name}}` e `{{api-version}}` pelos valores especificados na [etapa 3](#3---configure-postman). Se você estiver usando outra ferramenta para emitir seus comandos REST, precisará substituir essas variáveis por conta própria.

Especifique a estrutura do seu índice de Azure Search no corpo da solicitação. No postmaster, depois de definir os cabeçalhos `api-key` e `Content-type`, alterne para o painel **corpo** da solicitação. Você deve ver o JSON a seguir, mas se não estiver, escolha **RAW** e **JSON (Application/JSON)** e cole o seguinte código como o corpo:

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

Você verá que essa definição de índice é uma combinação de dados que você gostaria de apresentar ao usuário (nome do Hotel, examinar conteúdo, data e assim por diante), metadados de pesquisa e dados de aprimoramento de ia (sentimentos, keyfrases e Language).

Pressione o botão **Enviar** para emitir a solicitação Put. Você deve receber a mensagem de status de `201 - Created`. Se você receber um status diferente, o painel **corpo** mostrará uma resposta JSON com uma mensagem de erro. 

## <a name="5---create-the-datasource"></a>5-criar a fonte de fontes

Agora, você precisa se conectar Azure Search aos dados do Hotel armazenados na [etapa 2](#2---store-the-data). A criação da fonte de fontes é feita com uma POSTAgem para `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Novamente, você precisará definir os cabeçalhos `api-key` e `Content-Type`, conforme especificado anteriormente. 

No postmaster, abra a solicitação "criar fonte de origem". Alterne para o painel **corpo** , que deve ter o seguinte código:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Pressione o botão **Enviar** para emitir a solicitação post. 

## <a name="6---create-the-skillset"></a>6-criar o conconhecimento 

A próxima etapa é especificar o skillset, que especifica os aprimoramentos a serem aplicados e a loja de conhecimento onde os resultados serão armazenados. No postmaster, abra a guia "criar o conconhecimento". Essa solicitação envia um PUT para `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Defina os cabeçalhos `api-key` e `Content-type` como você fez anteriormente. 

Há dois grandes objetos de alto nível: `"skills"` e `"knowledgeStore"`. Cada objeto dentro do objeto `"skills"` é um serviço de enriquecimento. Cada serviço de enriquecimento tem `"inputs"` e `"outputs"`. Observe como o `LanguageDetectionSkill` tem uma saída `targetName` de `"Language"`. O valor desse nó é usado pela maioria das outras habilidades como uma entrada, com a origem como `document/Language`. Esse recurso de usar a saída de um nó como a entrada para outro é ainda mais evidente no `ShaperSkill`, que especifica como os dados fluirão para as tabelas da loja de conhecimento.

O objeto `"knowledge_store"` se conecta à conta de armazenamento por meio da variável de postmaster `{{storage-connection-string}}`. Em seguida, ele contém um conjunto de mapeamentos entre o documento aprimorado e as tabelas e colunas que estarão disponíveis na própria loja de conhecimento. 

Para gerar o configurador de habilidades, coloque a solicitação pressionando o botão **Enviar** no postmaster.

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

## <a name="7---create-the-indexer"></a>7-criar o indexador

A etapa final é criar o indexador, que, na verdade, lê os dados e ativa o skillset. No postmaster, alterne para a solicitação "criar indexador" e revise o corpo. Como você pode ver, a definição do indexador refere-se a vários outros recursos que você já criou – a DataSource, o índice e o skillset. 

O objeto `"parameters/configuration"` controla como o indexador ingeri os dados. Nesse caso, os dados de entrada estão em um único documento com uma linha de cabeçalho e valores separados por vírgulas. A chave do documento é um identificador exclusivo para o documento, que antes da codificação é a URL do documento de origem. Por fim, os valores de saída de tipo de habilidade, como código de idioma, sentimentos e frases-chave, são mapeados para seus locais apropriados no documento. Observe que, embora haja um único valor para `Language`, `Sentiment` é aplicado a cada elemento na matriz de `pages`. `Keyphrases` é, em si, uma matriz e também é aplicada a cada elemento na matriz `pages`.

Depois de definir os cabeçalhos `api-key` e `Content-type` e confirmar que o corpo da solicitação é semelhante ao código-fonte a seguir, pressione **Enviar** no postmaster. O postmaster COLOCARá a solicitação em `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Azure Search criará e executará o indexador. 

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

## <a name="8---run-the-indexer"></a>8-executar o indexador 

Na portal do Azure, navegue até a **visão geral** do serviço de pesquisa e selecione a guia **indexadores** . Clique em **Hotéis-Reviews-IXR** que você criou na etapa anterior. Se o indexador ainda não tiver sido executado, pressione o botão **executar** . A tarefa de indexação pode gerar alguns avisos relacionados ao reconhecimento de idioma, pois os dados incluem algumas revisões escritas em linguagens que ainda não têm suporte nas habilidades cognitivas. 

## <a name="next-steps"></a>Passos seguintes

Agora que você enriqueceu seus dados usando serviços cognitivas e projetou os resultados em uma loja de conhecimento, você pode usar Gerenciador de Armazenamento ou Power BI para explorar seu conjunto de dados aprimorado.

Para saber como explorar essa loja de conhecimento usando Gerenciador de Armazenamento, consulte o passo a passos a seguir.

> [!div class="nextstepaction"]
> [Exibir com Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)

Para saber como conectar essa loja de conhecimento ao Power BI, consulte o passo a passos a seguir.

> [!div class="nextstepaction"]
> [Ligar com o Power BI](knowledge-store-connect-power-bi.md)

Se você quiser repetir este exercício ou tentar um passo a passos de enriquecimento de ia diferente, exclua o indexador *Hotel-Reviews-idxr* . A exclusão do indexador redefine o contador de transação diária gratuito de volta para zero.
