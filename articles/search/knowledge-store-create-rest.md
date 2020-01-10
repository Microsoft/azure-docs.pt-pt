---
title: Criar uma loja de conhecimento (versão prévia) usando REST
titleSuffix: Azure Cognitive Search
description: Use a API REST e o postmaster para criar um repositório de conhecimento de Pesquisa Cognitiva do Azure para persistir aprimoramentos de um pipeline de enriquecimento de ia. Este recurso está atualmente em visualização pública.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 7dd1f07d44bd3b71bb83becee5405cf5c100460c
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754085"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Criar uma loja de conhecimento usando REST e postmaster

> [!IMPORTANT] 
> a loja de conhecimento está atualmente em visualização pública. A funcionalidade de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de visualização. Atualmente, há suporte ao portal limitado e não há suporte para o SDK do .NET.

Uma loja de conhecimento contém a saída de um pipeline de enriquecimento de Pesquisa Cognitiva do Azure para análise posterior ou outro processamento downstream. Um pipeline aprimorado de ia aceita arquivos de imagem ou arquivos de texto não estruturados, indexa-os usando o Azure Pesquisa Cognitiva, aplica os aprimoramentos de ia dos serviços cognitivas (como análise de imagem e processamento de linguagem natural) e salva os resultados em um repositório de conhecimento no armazenamento do Azure. Você pode usar ferramentas como Power BI ou Gerenciador de Armazenamento no portal do Azure para explorar a loja de conhecimento.

Neste artigo, você usa a interface da API REST para ingerir, indexar e aplicar aprimoramentos de ia a um conjunto de revisões de Hotel. As revisões do Hotel são importadas para o armazenamento de BLOBs do Azure. Os resultados são salvos como uma loja de conhecimento no armazenamento de tabelas do Azure.

Depois de criar a loja de conhecimento, você pode saber mais sobre como acessar a loja de conhecimento usando [Gerenciador de armazenamento](knowledge-store-view-storage-explorer.md) ou [Power bi](knowledge-store-connect-power-bi.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!TIP]
> Recomendamos o [aplicativo de área de trabalho do postmaster](https://www.getpostman.com/) para este artigo. O [código-fonte](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) deste artigo inclui uma coleção de postmaster que contém todas as solicitações. 

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este guia de início rápido usa o Azure Pesquisa Cognitiva, o armazenamento de BLOBs do Azure e os [Serviços cognitivas do Azure](https://azure.microsoft.com/services/cognitive-services/) para o ia. 

Como a carga de trabalho é tão pequena, os serviços cognitivas são tocados nos bastidores para fornecer processamento gratuito para até 20 transações diariamente quando invocado do Azure Pesquisa Cognitiva. Desde que você use os dados de exemplo que fornecemos, você pode ignorar a criação ou anexação de um recurso de serviços cognitivas.

1. [Baixe HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Esses dados são dados de revisão do Hotel salvos em um arquivo CSV (origina-se de Kaggle.com) e contêm 19 partes de comentários do cliente sobre um único hotel. 

1. [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) ou [localize uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) em sua assinatura atual. Você usará o armazenamento do Azure para o conteúdo bruto a ser importado e a loja de conhecimento que é o resultado final.

   Escolha o tipo de conta **StorageV2 (uso geral v2)** .

1. Abra as páginas de serviços de BLOB e crie um contêiner chamado *Hotel-Reviews*.

1. Clique em **Carregar**.

    ![Carregar os dados](media/knowledge-store-create-portal/upload-command-bar.png "Carregar as revisões do Hotel")

1. Selecione o arquivo **HotelReviews-Free. csv** que você baixou na primeira etapa.

    ![Criar o contêiner de blob do Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o contêiner de blob do Azure")

1. Você está quase pronto com esse recurso, mas antes de sair dessas páginas, use um link no painel de navegação esquerdo para abrir a página **chaves de acesso** . Obtenha uma cadeia de conexão para recuperar dados do armazenamento de BLOBs. Uma cadeia de conexão é semelhante ao exemplo a seguir: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Ainda no portal, alterne para o Azure Pesquisa Cognitiva. [Crie um novo serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Você pode usar um serviço gratuito para este exercício.

## <a name="configure-postman"></a>Configurar o Postman

Instalar e configurar o postmaster.

### <a name="download-and-install-postman"></a>Baixar e instalar o postmaster

1. Baixe o [código-fonte da coleção de postmaster](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Selecione **arquivo** > **importar** para importar o código-fonte para o postmaster.
1. Selecione a guia **coleções** e, em seguida, selecione o botão **...** (reticências).
1. Selecione **Editar**. 
   
   ![Aplicativo de postmaster mostrando navegação](media/knowledge-store-create-rest/postman-edit-menu.png "Ir para o menu Editar no postmaster")
1. Na caixa de diálogo **Editar** , selecione a guia **variáveis** . 

Na guia **variáveis** , você pode adicionar valores que o postmaster troca sempre que encontrar uma variável específica dentro de chaves duplas. Por exemplo, o postmaster substitui o símbolo `{{admin-key}}` pelo valor atual que você definiu para `admin-key`. O postmaster faz a substituição nas URLs, nos cabeçalhos, no corpo da solicitação e assim por diante. 

Para obter o valor para `admin-key`, vá para o serviço de Pesquisa Cognitiva do Azure e selecione a guia **chaves** . Altere `search-service-name` e `storage-account-name` para os valores escolhidos em [criar serviços](#create-services-and-load-data). Defina `storage-connection-string` usando o valor na guia **chaves de acesso** da conta de armazenamento. Você pode deixar os padrões para os outros valores.

![Guia variáveis de aplicativo do postmaster](media/knowledge-store-create-rest/postman-variables-window.png "Janela variáveis do postmaster")


| Variável    | Onde obtê-lo |
|-------------|-----------------|
| `admin-key` | Na página **chaves** do serviço de pesquisa cognitiva do Azure.  |
| `api-version` | Deixe como **2019-05-06-Preview**. |
| `datasource-name` | Deixe como **Hotel-Reviews-DS**. | 
| `indexer-name` | Deixe como **Hotel-Reviews-IXR**. | 
| `index-name` | Deixe como **Hotel-Reviews-IX**. | 
| `search-service-name` | O nome do serviço de Pesquisa Cognitiva do Azure. A URL é `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Deixe como **Hotel-Reviews-SS**. | 
| `storage-account-name` | O nome da conta de armazenamento. | 
| `storage-connection-string` | Na conta de armazenamento, na guia **chaves de acesso** , selecione **key1** > **cadeia de conexão**. | 
| `storage-container-name` | Deixe as **revisões do Hotel**. | 

### <a name="review-the-request-collection-in-postman"></a>Examinar a coleção de solicitações no postmaster

Ao criar uma loja de conhecimento, você deve emitir quatro solicitações HTTP: 

- **PUT Solicitar para criar o índice**: esse índice contém os dados que o Azure pesquisa cognitiva usa e retorna.
- **Pós-solicitação para criar a fonte de**dados: essa fonte se conecta ao seu comportamento de pesquisa cognitiva do Azure para a conta de armazenamento do Data Store e da loja de conhecimento. 
- **PUT Solicitar para criar o**configurador de habilidades: o configurador especifica os aprimoramentos que são aplicados aos seus dados e a estrutura da loja de conhecimento.
- **PUT Solicitar para criar o indexador**: a execução do indexador lê os dados, aplica o confirmador e armazena os resultados. Você deve executar esta solicitação por último.

O [código-fonte](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) contém uma coleção de postmaster que tem quatro solicitações. Para emitir as solicitações, no postmaster, selecione a guia para a solicitação. Em seguida, adicione `api-key` e `Content-Type` cabeçalhos de solicitação. Defina o valor de `api-key` como `{{admin-key}}`. Defina o valor `Content-type` como `application/json`. 

![Captura de tela mostrando a interface do postmaster para cabeçalhos](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Você deve definir `api-key` e `Content-type` cabeçalhos em todas as suas solicitações. Se o postmaster reconhecer uma variável, a variável aparecerá em texto laranja, assim como ocorre com `{{admin-key}}` na captura de tela anterior. Se a variável for digitada incorretamente, ela aparecerá em texto vermelho.
>

## <a name="create-an-azure-cognitive-search-index"></a>Criar um índice do Azure Cognitive Search

Crie um índice de Pesquisa Cognitiva do Azure para representar os dados que você está interessado em Pesquisar, filtrar e aplicar aprimoramentos ao. Crie o índice emitindo uma solicitação PUT para `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. O postmaster substitui símbolos que são colocados em chaves duplas (como `{{search-service-name}}`, `{{index-name}}`e `{{api-version}}`) com os valores que você define em configurar o [postmaster](#configure-postman). Se você usar uma ferramenta diferente para emitir seus comandos REST, deverá substituir essas variáveis por conta própria.

Defina a estrutura do seu índice de Pesquisa Cognitiva do Azure no corpo da solicitação. No postmaster, depois de definir os cabeçalhos de `api-key` e de `Content-type`, vá para o painel **corpo** da solicitação. Você deverá ver o JSON a seguir. Se você não fizer isso, selecione **Raw** > **JSON (Application/JSON)** e cole o código a seguir como o corpo:

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

Essa definição de índice é uma combinação de dados que você gostaria de apresentar ao usuário (o nome do Hotel, examinar o conteúdo, a data), os metadados de pesquisa e os dados de aprimoramento de ia (sentimentos, keyfrases e Language).

Selecione **Enviar** para emitir a solicitação Put. Você deve ver o status `201 - Created`. Se você vir um status diferente, no painel **corpo** , procure uma resposta JSON que contenha uma mensagem de erro. 

## <a name="create-the-datasource"></a>Criar a fonte de fontes

Em seguida, conecte o Azure Pesquisa Cognitiva aos dados do Hotel armazenados no armazenamento de BLOBs. Para criar a fonte de mensagens, envie uma solicitação POST para `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Você deve definir os cabeçalhos `api-key` e `Content-Type` conforme discutido anteriormente. 

No postmaster, vá para a solicitação **criar fonte** de mensagens e, em seguida, para o painel **corpo** . Deverá ver o seguinte código:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Selecione **Enviar** para emitir a solicitação post. 

## <a name="create-the-skillset"></a>Criar o conconhecimento 

A próxima etapa é especificar o skillset, que especifica os aprimoramentos a serem aplicados e a loja de conhecimento onde os resultados serão armazenados. No postmaster, selecione a guia **criar a habilidade** . Essa solicitação envia um PUT para `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Defina os cabeçalhos `api-key` e `Content-type` como fazia anteriormente. 

Há dois grandes objetos de alto nível: `skills` e `knowledgeStore`. Cada objeto dentro do `skills` objeto é um serviço de enriquecimento. Cada serviço de enriquecimento tem `inputs` e `outputs`. O `LanguageDetectionSkill` tem uma `targetName` de saída de `Language`. O valor desse nó é usado pela maioria das outras habilidades como uma entrada. A origem é `document/Language`. A capacidade de usar a saída de um nó como a entrada para outro é ainda mais evidente em `ShaperSkill`, que especifica como os dados fluem para as tabelas da loja de conhecimento.

O objeto `knowledge_store` se conecta à conta de armazenamento por meio da variável de postmaster `{{storage-connection-string}}`. `knowledge_store` contém um conjunto de mapeamentos entre o documento avançado e as tabelas e colunas na loja de conhecimento. 

Para gerar o qualificable, selecione o botão **Enviar** no postmaster para colocar a solicitação:

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

A etapa final é criar o indexador. O indexador lê os dados e ativa o qualificable. No postmaster, selecione a solicitação **criar indexador** e examine o corpo. A definição do indexador refere-se a vários outros recursos que você já criou: a fonte de recursos, o índice e o skillset. 

O objeto `parameters/configuration` controla como o indexador ingeri os dados. Nesse caso, os dados de entrada estão em um único documento que tem uma linha de cabeçalho e valores separados por vírgulas. A chave do documento é um identificador exclusivo para o documento. Antes da codificação, a chave do documento é a URL do documento de origem. Por fim, os valores de saída de tipo de habilidade, como código de idioma, sentimentos e frases-chave, são mapeados para seus locais no documento. Embora haja um único valor para `Language`, `Sentiment` é aplicado a cada elemento na matriz de `pages`. `Keyphrases` é uma matriz que também é aplicada a cada elemento na matriz de `pages`.

Depois de definir os cabeçalhos `api-key` e `Content-type` e confirmar que o corpo da solicitação é semelhante ao código-fonte a seguir, selecione **Enviar** no postmaster. O postmaster envia uma solicitação PUT para `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. O Azure Pesquisa Cognitiva cria e executa o indexador. 

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

Na portal do Azure, vá para a página de **visão geral** do serviço de pesquisa cognitiva do Azure. Selecione a guia **indexadores** e, em seguida, selecione **Hotéis-Reviews-IXR**. Se o indexador ainda não tiver sido executado, selecione **executar**. A tarefa de indexação pode gerar alguns avisos relacionados ao reconhecimento de idioma. Os dados incluem algumas revisões que são escritas em linguagens que ainda não são compatíveis com as habilidades cognitivas. 

## <a name="next-steps"></a>Passos seguintes

Agora que você enriqueceu seus dados usando serviços cognitivas e projetou os resultados em uma loja de conhecimento, você pode usar Gerenciador de Armazenamento ou Power BI para explorar seu conjunto de dados aprimorado.

Para saber como explorar essa loja de conhecimento usando Gerenciador de Armazenamento, consulte este passo a passos:

> [!div class="nextstepaction"]
> [Exibir com Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)

Para saber como conectar essa loja de conhecimento a Power BI, consulte este passo a passos:

> [!div class="nextstepaction"]
> [Ligar com o Power BI](knowledge-store-connect-power-bi.md)

Se você quiser repetir este exercício ou tentar um passo a passos de enriquecimento de ia diferente, exclua o indexador **Hotel-Reviews-idxr** . A exclusão do indexador redefine o contador de transações diárias livres para zero.
