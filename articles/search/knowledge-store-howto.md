---
title: Como obter a utilizar dados de conhecimento de extração (pré-visualização) - Azure Search
description: Conheça as etapas para enviar documentos plena criados de IA indexação pipelines do Azure Search para um arquivo de dados de conhecimento na sua conta de armazenamento do Azure. A partir daí, pode ver, reformatar e consumir documentos plena no Azure Search e em outros aplicativos.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: e50dfcdc5ac2fbe2435066546a340874e1b8f682
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551062"
---
# <a name="how-to-get-started-with-knowledge-mining-in-azure-search"></a>Como começar com a mineração de dados de conhecimento no Azure Search

> [!Note]
> Arquivo de dados de conhecimento está em pré-visualização e não se utilização em produção. O [2019 no versão REST API-05-06-Preview](search-api-preview.md) fornece esta funcionalidade. Não existe nenhum suporte de .NET SDK neste momento.
>
[Arquivo de dados de conhecimento](knowledge-store-concept-intro.md) salva sofisticados de IA documentos criados durante a indexação à sua conta de armazenamento do Azure para extração de dados de conhecimento downstream noutras aplicações. Também pode utilizar o possível guardado para compreender e refinar um pipeline de indexação de pesquisa do Azure. 

Um arquivo de dados de conhecimento é definido por um *conjunto de capacidades* e criado por um *indexador*. A expressão física de um arquivo de dados de conhecimento é especificada por meio *projeções* que determinam as estruturas de dados no armazenamento. Quando que concluir este passo a passo, irá criar todos esses objetos e saberá como elas se encaixam. 

Neste exercício, começa com dados de exemplo, serviços e ferramentas para aprender o fluxo de trabalho básico para criar e utilizar o seu primeiro armazenamento de dados de conhecimento, com ênfase na definição do conjunto de capacidades.

## <a name="prerequisites"></a>Pré-requisitos

Arquivo de dados de conhecimento é no Centro de vários serviços, com o armazenamento de Blobs do Azure e o armazenamento de tabelas do Azure fornece armazenamento físico e o Azure Search e os serviços cognitivos para criação de objetos e atualizações. Familiaridade com o [arquitetura básica](knowledge-store-concept-intro.md) é um pré-requisito para este passo a passo.

Os seguintes serviços e ferramentas são utilizadas neste início rápido. 

+ [Aplicativo de desktop do Postman](https://www.getpostman.com/), utilizado para enviar pedidos HTTP para o Azure Search.

+ [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar dados de exemplo e o conhecimento armazenar. Arquivo de dados de conhecimento existirá no armazenamento do Azure.

+ [Criar um recurso de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) no escalão S0 pay as you go para o amplo espectro acesso a uma gama completa de habilidades utilizadas no possível de IA. Os serviços cognitivos e o serviço Azure Search têm de estar na mesma região.

+ [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial. 

Documentos JSON de exemplo e um ficheiro de coleção do Postman também são necessários. Instruções para a localização e a carregar ficheiros suplementares é fornecido na [preparar dados de exemplo](#prepare-sample-data) secção.

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

    ![Obter uma chave de acesso e de ponto final HTTP](media/search-get-started-postman/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Irá fornecer o nome do serviço e a chave de API em cada solicitação HTTP nas seções a seguir.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

Um arquivo de dados de conhecimento contém a saída de um pipeline de melhoria. Consistem em entradas de dados "inutilizáveis" que, por fim, se torna "utilizáveis" à medida que progride através do pipeline. Exemplos de dados não utilizáveis podem incluir arquivos de imagem que têm de ser analisado relativamente a texto ou características de imagem ou arquivos de texto densa que podem ser analisados para sentimentos, expressões-chave ou entidades. 

Este exercício utiliza ficheiros de texto densa (lei de caso de informações) que são originados pela [Caselaw acesso projeto](https://case.law/bulk/download/) página de transferência de dados do público em massa. Um exemplo de documento de 10 nós carregamos para o GitHub para este exercício. 

Nesta tarefa, irá criar um contentor de Blobs do Azure para esses documentos utilizar como entrada para o pipeline. 

1. Transferir e extrair os [dados de exemplo de pesquisa do Azure](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) repositório para obter o [conjunto de dados de Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw). 

1. [Inicie sessão no portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento do Azure, clique em **Blobs**e, em seguida, clique em **+ contentor**.

1. [Criar um contentor de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo: 

   1. Nome do contentor `caselaw-test`. 
   
   1. Defina o nível de acesso público a qualquer um dos respetivos valores válidos.

1. Depois do contentor é criado, abra-o e selecione **carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "carregar na barra de comando")

1. Navegue para a pasta que contém o **caselaw sample.json** ficheiro de exemplo. Selecione o ficheiro e, em seguida, clique em **carregar**.

1. Enquanto estiver no armazenamento do Azure, obtenha o nome de contentor e a cadeia de ligação.  Terá de ambas essas cadeias de caracteres [criar origem de dados](#create-data-source):

   1. Na página Descrição geral, clique em **chaves de acesso** e copie um *cadeia de ligação*. Ele começa com `DefaultEndpointsProtocol=https;` e termina com `EndpointSuffix=core.windows.net`. O nome da conta e a chave são entre. 

   1. Deve ser o nome do contentor `caselaw-test` ou o nome que atribuiu.



## <a name="set-up-postman"></a>Configurar o Postman

Postman é a aplicação de cliente que irá utilizar para enviar pedidos e de documentos JSON para o Azure Search. Vários dos pedidos podem ser formulados com apenas as informações neste artigo. No entanto, duas das maiores solicitações (criar um índice, a criação de um conjunto de capacidades) incluem JSON verboso que é demasiado grande para incorporar um artigo. 

Para disponibilizar todos os documentos JSON e pedidos totalmente, criamos um ficheiro de coleção do Postman. Transferir e, em seguida, importar este ficheiro são a sua primeira tarefa na configuração de cliente.

1. Transfira e deszipe o [amostras de Azure Search Postman](https://github.com/Azure-Samples/azure-search-postman-samples) repositório.

1. Inicie o Postman e importar a coleção do Caselaw Postman:

   1. Clique em **importação** > **importar ficheiros** > **escolher ficheiros**. 

   1. Navegue para a pasta \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw.

   1. Selecione **Caselaw.postman_collection_v2.json**. Deverá ver quatro **POST** pedidos na coleção.

   ![Coleção do postman para demonstração Caselaw](media/knowledge-store-howto/postman-collection.png "coleção do Postman para demonstração Caselaw")
   

## <a name="create-an-index"></a>Criar um índice
    
O primeiro pedido utiliza a [criar o índice API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), criar um índice da Azure Search que armazena todos os dados pesquisáveis. Um índice Especifica todos os campos, parâmetros e atributos.

Não precisa necessariamente um índice para extração de dados de conhecimento, mas um indexador não será executado, a menos que um índice é fornecido. 

1. No URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`, substitua `YOUR-AZURE-SEARCH-SERVICE-NAME` com o nome do seu serviço de pesquisa. 

1. Na seção do cabeçalho, substitua `<YOUR AZURE SEARCH ADMIN API-KEY>` com uma chave de API de administração para o Azure Search.

1. Na seção de corpo, o documento JSON é um esquema de índice. Recolhido para visibilidade, o shell externo de um índice consiste dos seguintes elementos. A coleção de campos corresponde a campos no conjunto de dados de caselaw.

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

1. Expanda o `fields` coleção. Ele contém grande parte da definição de índice, compostas por campos simples, [campos complexos](search-howto-complex-data-types.md) com substructures aninhadas e coleções.

   Dedique uns momentos para rever a definição de campo para o `casebody` campo complexo em linhas 302-384. Tenha em atenção que um campo complexo pode conter outros campos complexos representações hierárquicas são necessários. Estruturas hierárquicas podem ser modeladas num índice, conforme mostrado aqui e também como uma projeção num conjunto de capacidades, criando assim uma estrutura de dados aninhada no arquivo de dados de conhecimento.

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

1. Clique em **enviar** para executar o pedido.  Obterá um **Estado: 201 criado** mensagem como uma resposta.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Criar uma origem de dados

A segunda solicitação utiliza a [API de origem de dados criar](https://docs.microsoft.com/rest/api/searchservice/create-data-source) para ligar ao armazenamento de Blobs do Azure. 

1. No URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`, substitua `YOUR-AZURE-SEARCH-SERVICE-NAME` com o nome do seu serviço de pesquisa. 

1. Na seção do cabeçalho, substitua `<YOUR AZURE SEARCH ADMIN API-KEY>` com uma chave de API de administração para o Azure Search.

1. Na seção de corpo, o documento JSON inclui ligação cadeia de caracteres e BLOBs contentor nome da sua conta. A cadeia de ligação pode ser encontrada no portal do Azure dentro da sua conta de armazenamento **chaves de acesso**. 

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

1. Clique em **enviar** para executar o pedido.  Obterá um **Estado: 201 criado** mensagem como uma resposta.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Criar um arquivo de conjunto de capacidades e dados de conhecimento

O terceiro pedido utiliza a [API do conjunto de capacidades de criar](https://docs.microsoft.com/rest/api/searchservice/create-skillset), criar um objeto de Azure Search que especifica quais capacidades cognitivas para chamar, como encadear habilidades em conjunto e o mais importante para estas instruções - como especificar um arquivo de dados de conhecimento.

1. No URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`, substitua `YOUR-AZURE-SEARCH-SERVICE-NAME` com o nome do seu serviço de pesquisa. 

1. Na seção do cabeçalho, substitua `<YOUR AZURE SEARCH ADMIN API-KEY>` com uma chave de API de administração para o Azure Search.

1. Na seção de corpo, o documento JSON é uma definição de conjunto de capacidades. Recolhido para visibilidade, o shell externo de um conjunto de capacidades consiste dos seguintes elementos. O `skills` coleção define possível na memória, mas o `knowledgeStore` definição especifica como a saída é armazenada. O `cognitiveServices` definição é a ligação para os mecanismos de enriquecimento de IA.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Expanda `cognitiveServices` e `knowledgeStore` para que pode fornecer informações de ligação. No exemplo, essas cadeias de caracteres são localizadas após a definição do conjunto de capacidades, até o final do corpo do pedido. 

   Para `cognitiveServices`, aprovisionar um recurso no escalão S0, localizado na mesma região que o Azure Search. Pode obter o nome de cognitiveServices e a chave a partir da mesma página no portal do Azure. 
   
   Para `knowledgeStore`, pode usar a mesma cadeia de ligação utilizada para o contentor de BLOBs caselaw.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Expanda a coleção de habilidades, em particular as habilidades de Modelador em linhas 85 e 179, respectivamente. A habilidade de Modelador é importante porque ele reúne as estruturas de dados que pretende para extração de dados de conhecimento. Durante a execução do conjunto de capacidades, essas estruturas só estão na memória, mas como mover para a próxima etapa, verá como esta saída pode ser salvas num arquivo de dados de conhecimento para obter uma exploração.

   O fragmento seguinte é a partir da linha 217. 

    ```json
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
   . . .
   ```

1. Expanda `projections` elemento no `knowledgeStore`, a partir na linha 262. Projeções de especificar a composição de arquivo de dados de conhecimento. Projeções são especificadas em pares de objetos de tabelas, mas atualmente apenas um de cada vez. Como pode ver na primeira projeção, `tables` for especificado, mas `objects` não é. Na segunda, é o oposto.

   No armazenamento do Azure, serão possível criar tabelas no armazenamento de tabelas para cada tabela que cria e cada objeto obtém um contentor no armazenamento de Blobs.

   Normalmente, os objetos de blob contêm a expressão completa de uma melhoria. Tabelas normalmente contêm possível parcial, em combinações dispor para fins específicos. Este exemplo mostra uma tabela de casos e uma tabela de opiniões, mas não apresentados são outras tabelas, como entidades, advogados, juízes e partes.

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

1. Clique em **enviar** para executar o pedido. A resposta deve ser **201** e ter um aspeto semelhante ao exemplo seguinte, que mostra a primeira parte da resposta.

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

O quarto pedido utiliza a [API do indexador de criar](https://docs.microsoft.com/rest/api/searchservice/create-indexer), criar um indexador de Azure Search. Um indexador é o motor de execução do pipeline de indexação. Todas as definições que criou até agora são colocadas em movimento com este passo.

1. No URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`, substitua `YOUR-AZURE-SEARCH-SERVICE-NAME` com o nome do seu serviço de pesquisa. 

1. Na seção do cabeçalho, substitua `<YOUR AZURE SEARCH ADMIN API-KEY>` com uma chave de API de administração para o Azure Search.

1. Na seção de corpo, o documento JSON Especifica o nome de indexador. Uma origem de dados e índice são necessários o indexador. Um conjunto de capacidades é opcional para um indexador, mas necessário para a melhoria de IA.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    ```

1. Expanda outputFieldMappings. Em contraste com fieldMappings, que são utilizados para personalizado mapeamento entre os campos numa origem de dados e campos num índice, os outputFieldMappings são utilizados para mapeamento de campos plena, criados e preenchidos pelo pipeline, aos campos de saída num índice ou numa projeção.

    ```json
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
    ```

1. Clique em **enviar** para executar o pedido. A resposta deve ser **201** e o corpo da resposta deve ser quase idêntico ao payload de pedido fornecido (cortado para fins de brevidade).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
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
