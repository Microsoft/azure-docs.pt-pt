---
title: Como começar com dados de conhecimento Store (pré-visualização) - Azure Search
description: Conheça as etapas para enviar documentos plena criados de IA indexação pipelines do Azure Search para um arquivo de dados de conhecimento na sua conta de armazenamento do Azure. A partir daí, pode ver, reformatar e consumir documentos plena no Azure Search e em outros aplicativos.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 2a904cfb049af413887798c8aab449561bc2b73f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026971"
---
# <a name="how-to-get-started-with-knowledge-store"></a>Como começar com dados de conhecimento Store

[Dados de conhecimento Store](knowledge-store-concept-intro.md) é uma nova funcionalidade de pré-visualização do Azure Search, que guarda possível de IA criado num pipeline de indexação para extração de dados de conhecimento em outras aplicações. Também pode utilizar o possível guardado para compreender e refinar um pipeline de indexação de pesquisa do Azure.

Um arquivo de dados de conhecimento é definido por um conjunto de capacidades. Para cenários de pesquisa em texto completo do Azure Search regulares, a finalidade de um conjunto de capacidades está fornecendo possível de ia para tornar o conteúdo mais pesquisável. Para cenários de arquivo de dados de conhecimento, a função de um conjunto de capacidades está a criar e preencher várias estruturas de dados para extração de dados de conhecimento.

Neste exercício, começa com dados de exemplo, serviços e ferramentas para aprender o fluxo de trabalho básico para criar e utilizar o seu primeiro armazenamento de dados de conhecimento, com ênfase na definição do conjunto de capacidades.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste início rápido. 

+ [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial. 

+ [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. Arquivo de dados de conhecimento existirá no armazenamento do Azure.

+ [Criar um recurso de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) no escalão S0 pay as you go para o amplo espectro acesso a uma gama completa de habilidades utilizadas no possível de IA.

+ [Aplicação de ambiente de trabalho postman](https://www.getpostman.com/) para enviar pedidos para o Azure Search.

+ [Coleção do postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/caselaw) com pedidos preparados para a criação de uma origem de dados, o índice, o conjunto de capacidades e o indexador. Várias definições de objetos são demasiado longas para incluir neste artigo. Tem de obter esta coleção para ver as definições de índice e o conjunto de capacidades integralmente.

+ [Dados de exemplo Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) provenientes do [Caselaw acesso projeto](https://case.law/bulk/download/) página de transferência de dados do público em massa. Especificamente, o exercício utiliza os primeiros 10 documentos do primeiro download (Arkansas). Um exemplo de documento de 10 nós carregamos para o GitHub para este exercício.

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

    ![Obter uma chave de acesso e de ponto final HTTP](media/search-fiddler/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço.

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

1. [Inicie sessão no portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento do Azure, clique em **Blobs**e, em seguida, clique em **+ contentor**.

1. [Criar um contentor de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Pode definir o nível de acesso público a qualquer um dos respetivos valores válidos.

1. Depois do contentor é criado, abra-o e selecione **carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "carregar na barra de comando")

1. Navegue para a pasta que contém o **caselaw sample.json** ficheiro de exemplo. Selecione o ficheiro e, em seguida, clique em **carregar**.


## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure um pedido de HTTP. Se não estiver familiarizado com essa ferramenta, consulte [Explore pesquisa APIs REST do Azure com o Postman](search-fiddler.md).

+ Método de pedido para todas as chamadas nestas instruções é **POST**.
+ Cabeçalhos de pedido (2) incluem o seguinte: "Content-type" definido como "application/json", "api-key" definido como sua "admin key" (a chave de administrador é um marcador de posição para a chave primária de pesquisa), respectivamente. 
+ O corpo do pedido é onde coloca o conteúdo real da sua chamada. 

  ![Pesquisa semiestruturada](media/search-semi-structured-data/postmanoverview.png)

Podemos utilizar o Postman para fazer quatro chamadas à API para o serviço de pesquisa, criação de uma origem de dados, um índice, um conjunto de capacidades e um indexador. A origem de dados inclui um ponteiro para a conta de armazenamento e dados JSON. O serviço de pesquisa faz a ligação ao importar os dados.

[Criar um conjunto de capacidades](#create-skillset) é o foco destas instruções: Especifica os passos de enriquecimento e como os dados são mantidos num arquivo de dados de conhecimento.

Ponto final do URL tem de especificar uma versão de api e cada chamada devem retornar um **201 criado**. A api-version do pré-visualização para a criação de um conjunto de capacidades com suporte de arquivo de dados de conhecimento é `2019-05-06-Preview`.

Execute as seguintes chamadas à API a partir do seu cliente REST.

## <a name="create-a-data-source"></a>Criar uma origem de dados

O [API de origem de dados criar](https://docs.microsoft.com/rest/api/searchservice/create-data-source) cria um objeto de Azure Search que especifica quais os dados para o índice.

O ponto final desta chamada é `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` 

1. Substitua `[service name]` pelo nome do seu serviço de pesquisa. 

2. Para esta chamada, o corpo do pedido tem de incluir ligação cadeia de caracteres e BLOBs contentor nome da sua conta. A ligação pode ser encontrada no portal do Azure dentro da sua conta de armazenamento **chaves de acesso**. 

   Certifique-se substituir o nome de contentor de ligação cadeia de caracteres e BLOBs, no corpo do pedido antes de executar a chamada.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. Envie o pedido. A resposta deve ser **201** e o corpo da resposta deve ser quase idêntico ao payload de pedido que forneceu.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>Criar um índice
    
A segunda está [criar o índice API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), criar um índice da Azure Search que armazena todos os dados pesquisáveis. Um índice Especifica todos os campos, parâmetros e atributos.

Não precisa necessariamente um índice para extração de dados de conhecimento, mas um indexador não será executado, a menos que um índice é fornecido. 

O URL para esta chamada é `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`

1. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

2. Copie a definição de índice no pedido de criar o índice na coleção do Postman para o corpo do pedido. A definição de índice é várias centenas de linhas, demasiado longos para imprimir aqui. 

   O shell externo de um índice consiste dos seguintes elementos. 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. O `fields` coleção contém a maior parte da definição do índice. Ele inclui campos simples, [campos complexos](search-howto-complex-data-types.md) com substructures aninhadas e coleções.

   Reveja a definição de campo para `casebody` em linhas 302-384. Tenha em atenção que um campo complexo pode conter outros campos complexos representações hierárquicas são necessários.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. Envie o pedido. 

   A resposta deve ser **201** e ter um aspeto semelhante ao exemplo seguinte, que mostra os campos de vários primeiro:

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Criar um arquivo de conjunto de capacidades e dados de conhecimento

O [API do conjunto de capacidades de criar](https://docs.microsoft.com/rest/api/searchservice/create-skillset) cria um objeto de Azure Search que especifica quais capacidades cognitivas para chamar, como encadear habilidades em conjunto e o mais importante para este passo a passo - a especificar um arquivo de dados de conhecimento.

O ponto final desta chamada é `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`

1. Substitua `[service name]` pelo nome do seu serviço de pesquisa.

2. Copie a definição do conjunto de capacidades no pedido de criar o conjunto de capacidades na coleção do Postman para o corpo do pedido. A definição do conjunto de capacidades é várias centenas de linhas, demasiado longos para imprimir aqui, mas é o foco destas instruções.

   O shell externo de um conjunto de capacidades consiste dos seguintes elementos. O `skills` coleção define possível na memória, mas o `knowledgeStore` definição especifica como a saída é armazenada. O `cognitiveServices` definição é a ligação para os mecanismos de enriquecimento de IA.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. Em primeiro lugar, defina `cognitiveServices` e `knowledgeStore` cadeia de ligação e a chave. No exemplo, essas cadeias de caracteres são localizadas após a definição do conjunto de capacidades, até o final do corpo do pedido.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "<your cognitive services resource name>",
        "key": "<your cognitive services key>"
    },
    "knowledgeStore": {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your storage account key>;EndpointSuffix=core.windows.net",
    ```

3. Reveja a coleção de habilidades, em particular as habilidades de Modelador em linhas 85 e 170, respectivamente. A habilidade de Modelador é importante porque ele reúne as estruturas de dados que pretende para extração de dados de conhecimento. Durante a execução do conjunto de capacidades, essas estruturas só estão na memória, mas como mover para a próxima etapa, verá como esta saída pode ser salvas num arquivo de dados de conhecimento para obter uma exploração.

   O fragmento seguinte é a partir da linha de 207. 

    ```json
    {
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
             ]
          }
     ]
   }
   . . .
   ```

3. Reveja os `projections` elemento no `knowledgeStore`, a partir na linha 253. Projeções de especificar a composição de arquivo de dados de conhecimento. Projeções são especificadas em pares de objetos de tabelas, mas atualmente apenas um de cada vez. Como pode ver na primeira projeção, `tables` for especificado, mas `objects` não é. Na segunda, é o oposto.

   No armazenamento do Azure, serão possível criar tabelas no armazenamento de tabelas para cada tabela que cria e cada objeto obtém um contentor no armazenamento de Blobs.

   Normalmente, os objetos contêm a expressão completa de uma melhoria do utilizador. Tabelas normalmente contêm possível parcial, em combinações dispor para fins específicos. Este exemplo mostra uma tabela de casos, mas não apresentados são outras tabelas, como entidades, juízes e opiniões.

    ```json
    "projections": [
    {
        "tables": [
            {
              "tableName": "Opinions",
              "generatedKeyName": "OpinionId",
              "source": "/document/Case/OpinionsSnippets/*"
            },
          . . . 
        ],
        "objects": []
    },
    {
        "tables": [],
        "objects": [
            {
                "storageContainer": "enrichedcases",
                "key": "/document/CaseFull/Id",
                "source": "/document/CaseFull"
            }
          ]
        }
      ]
    }
    ```

5. Envie o pedido. A resposta deve ser **201** e ter um aspeto semelhante ao exemplo seguinte, que mostra a primeira parte da resposta.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Criar e executar um indexador

O [API do indexador de criar](https://docs.microsoft.com/rest/api/searchservice/create-indexer) cria e executa imediatamente um indexador. Todas as definições que criou até agora são colocadas em movimento com este passo. O indexador é executado imediatamente porque não existe no serviço. Depois de existir, uma chamada POST para um indexador existente é uma operação de atualização.

O ponto final desta chamada é `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`

1. Substitua `[service name]` pelo nome do seu serviço de pesquisa. 

2. Para esta chamada, o corpo do pedido especifica o nome do indexador. Uma origem de dados e índice são necessários o indexador. Um conjunto de capacidades é opcional para um indexador, mas necessário para a melhoria de IA.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. Envie o pedido. A resposta deve ser **201** e o corpo da resposta deve ser quase idêntico ao payload de pedido fornecido (cortado para fins de brevidade).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>Explorar o arquivo de dados de conhecimento

Pode começar a explorar, assim como o primeiro documento é importado. Para esta tarefa, utilize [ **Explorador de armazenamento** ](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) no portal.

É importante perceber que um arquivo de dados de conhecimento é totalmente anular a exposição da Azure Search. O índice da Azure Search e o conhecimento armazenamento ambos contêm a representação de dados e conteúdo, mas parte formas a partir daí. Utilize o índice para pesquisa em texto completo e todos os cenários suportados no Azure Search pesquisa filtrada. Em alternativa, seguir em frente com apenas seu conhecimento store, anexar a outras ferramentas para analisar o conteúdo.

## <a name="takeaways"></a>Conclusões

Acabou de criar seu primeiro armazenamento de dados de conhecimento no armazenamento do Azure e utilizou o Explorador de armazenamento para ver o possível. Esta é a experiência fundamental para trabalhar com possível armazenado. 

## <a name="next-steps"></a>Passos Seguintes

A habilidade de Modelador faz o trabalho pesado na criação de formulários de dados detalhados que podem ser combinados em novas formas. Como passo seguinte, reveja a página de referência para essa habilidade para obter detalhes sobre como são utilizadas.

> [!div class="nextstepaction"]
> [Referência de habilidades Modelador](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->