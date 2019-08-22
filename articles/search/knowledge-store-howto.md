---
title: Como começar a usar a loja de conhecimento (versão prévia)-Azure Search
description: Conheça as etapas para enviar documentos aprimorados criados por pipelines de indexação de ia no Azure Search para uma loja de conhecimento em sua conta de armazenamento do Azure. A partir daí, você pode exibir, remodelar e consumir documentos aprimorados no Azure Search e em outros aplicativos.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: d6cecdce9bc4f7fee9ec936ac73b6accc77084c4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648824"
---
# <a name="how-to-get-started-with-knowledge-store-in-azure-search"></a>Como começar a usar a loja de conhecimento no Azure Search

> [!Note]
> A loja de conhecimento está em versão prévia e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>
A [loja de conhecimento](knowledge-store-concept-intro.md) salva os documentos aprimorados do ia criados durante a indexação para sua conta de armazenamento do Azure para downstream Data Mining em outros aplicativos. Você também pode usar aprimoramentos salvos para entender e refinar um pipeline de indexação de Azure Search. 

Uma loja de conhecimento é definida por um qualificable e criada porum indexador. A expressão física de um repositório de conhecimento é especificada por meio de projeções que determinam as estruturas de dados no armazenamento. No momento em que você concluir este passo a passos, você terá criado todos esses objetos e saberá como eles se encaixam juntos. 

Neste exercício, comece com os dados de exemplo, os serviços e as ferramentas para aprender o fluxo de trabalho básico para criar e usar sua primeira loja de conhecimento, com ênfase na definição de Skills.

## <a name="prerequisites"></a>Pré-requisitos

A loja de conhecimento está no centro de vários serviços, com o armazenamento de BLOBs do Azure e o armazenamento de tabelas do Azure que fornece armazenamento físico e Azure Search e serviços cognitivas para a criação e as atualizações de objetos. A familiaridade com a [arquitetura básica](knowledge-store-concept-intro.md) é um pré-requisito para este passo a passos.

Os seguintes serviços e ferramentas são usados neste guia de início rápido. 

+ [Obtenha o aplicativo de área de trabalho do postmaster](https://www.getpostman.com/), usado para enviar solicitações HTTP para Azure Search.

+ [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar dados de exemplo e o repositório de conhecimento. Sua loja de conhecimento existirá no armazenamento do Azure.

+ [Crie um recurso de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) na camada pago pelo uso S0 para acesso de amplo espectro a toda a gama de habilidades usadas em Aprimoramentos de ia. Serviços cognitivas e seu serviço de Azure Search precisam estar na mesma região.

+ [Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial. 

Também são necessários documentos JSON de exemplo e um arquivo de coleção de postmaster. As instruções para localizar e carregar arquivos suplementares são fornecidas na seção [preparar dados de exemplo](#prepare-sample-data) .

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

    ![Obter um ponto de extremidade http e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade http e uma chave de acesso")

Todas as solicitações exigem uma chave de API em cada solicitação enviada ao seu serviço. Você fornecerá o nome do serviço e a chave de API em cada solicitação HTTP nas seções a seguir.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

Um repositório de conhecimento contém a saída de um pipeline de enriquecimento. As entradas consistem em dados "inutilizáveis" que, por fim, se tornam "utilizáveis" durante o andamento do pipeline. Exemplos de dados inutilizáveis podem incluir arquivos de imagem que precisam ser analisados para características de texto ou imagem, ou arquivos de texto denso que podem ser analisados para entidades, frases-chave ou sentimentos. 

Este exercício usa arquivos de texto denso (informações de lei de caso) provenientes da página de download de dados públicos em massa do [projeto do Caselaw Access](https://case.law/bulk/download/) . Carregamos um exemplo de 10 documentos no GitHub para este exercício. 

Nesta tarefa, você criará um contêiner de BLOBs do Azure para esses documentos usar como uma entrada para o pipeline. 

1. Baixe e extraia o repositório de [dados de exemplo Azure Search](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) para obter o [conjunto de dados Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw). 

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento do Azure, cliqueem BLOBs e, em seguida, clique em **+ contêiner**.

1. [Crie um contêiner](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) de BLOBs para conter dados de exemplo: 

   1. Nomeie o contêiner `caselaw-test`. 
   
   1. Defina o nível de acesso público como qualquer um de seus valores válidos.

1. Depois que o contêiner for criado, abra-o e selecione **carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "Carregar na barra de comandos")

1. Navegue até a pasta que contém o arquivo de exemplo **caselaw-Sample. JSON** . Selecione o arquivo e clique em **carregar**.

1. Enquanto você estiver no armazenamento do Azure, obtenha a cadeia de conexão e o nome do contêiner.  Você precisará dessas duas cadeias de caracteres em [criar fonte de dados](#create-data-source):

   1. Na página Visão geral, clique em **chaves de acesso** e copie uma cadeia de *conexão*. Ele começa com `DefaultEndpointsProtocol=https;` e termina com. `EndpointSuffix=core.windows.net` O nome da conta e a chave estão entre. 

   1. O nome do contêiner deve `caselaw-test` ser ou qualquer nome que você atribuiu.



## <a name="set-up-postman"></a>Configurar o Postman

O postmaster é o aplicativo cliente que você usará para enviar solicitações e documentos JSON para Azure Search. Várias das solicitações podem ser formuladas usando apenas as informações neste artigo. No entanto, duas das maiores solicitações (criar um índice, criar um valor de habilidade) incluem JSON detalhado que é muito grande para ser inserido em um artigo. 

Para disponibilizar todos os documentos e solicitações JSON totalmente disponíveis, criamos um arquivo de coleção de postmaster. Baixar e importar esse arquivo é sua primeira tarefa na configuração do cliente.

1. Baixe e descompacte o repositório de [exemplos do Azure Search postmaster](https://github.com/Azure-Samples/azure-search-postman-samples) .

1. Inicie o postmaster e importe a coleção do postmaster Caselaw:

   1. Clique em **importar** > **arquivos** > de importação**escolher arquivos**. 

   1. Navegue até a pasta \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw

   1. Selecione **Caselaw. postman_collection_v2. JSON**. Você deve ver quatro solicitações **post** na coleção.

   ![Coleção de postmaster para demonstração de Caselaw](media/knowledge-store-howto/postman-collection.png "Coleção de postmaster para demonstração de Caselaw")
   

## <a name="create-an-index"></a>Criar um índice
    
A primeira solicitação usa a [API CREATE INDEX](https://docs.microsoft.com/rest/api/searchservice/create-data-source), criando um índice de Azure Search que armazena todos os dados pesquisáveis. Um índice especifica todos os campos, parâmetros e atributos.

Você não precisa necessariamente de um índice para a mineração de conhecimento, mas um indexador não será executado a menos que um índice seja fornecido. 

1. Na URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`, substitua `YOUR-AZURE-SEARCH-SERVICE-NAME` pelo nome do serviço de pesquisa. 

1. Na seção de cabeçalho, substitua `<YOUR AZURE SEARCH ADMIN API-KEY>` por uma chave de API de administrador para Azure Search.

1. Na seção corpo, o documento JSON é um esquema de índice. Recolhido para visibilidade, o Shell externo de um índice consiste nos seguintes elementos. A coleção Fields corresponde aos campos no conjunto de dados caselaw.

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

1. Expanda `fields` a coleção. Ele contém a massa da definição de índice, composta de campos simples, [campos complexos](search-howto-complex-data-types.md) com subestruturações aninhadas e coleções.

   Reserve um tempo para examinar a definição de campo para `casebody` o campo complexo nas linhas 302-384. Observe que um campo complexo pode conter outros campos complexos quando representações hierárquicas são necessárias. As estruturas hierárquicas podem ser modeladas em um índice, como mostrado aqui, e também como uma projeção em um qualificable, criando assim uma estrutura de dados aninhada na loja de conhecimento.

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

1. Clique em **Enviar** para executar a solicitação.  Você deve obter um **status: 201 mensagem** criada como uma resposta.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Criar uma origem de dados

A segunda solicitação usa a [API criar fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) para se conectar ao armazenamento de BLOBs do Azure. 

1. Na URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`, substitua `YOUR-AZURE-SEARCH-SERVICE-NAME` pelo nome do serviço de pesquisa. 

1. Na seção de cabeçalho, substitua `<YOUR AZURE SEARCH ADMIN API-KEY>` por uma chave de API de administrador para Azure Search.

1. Na seção corpo, o documento JSON inclui a cadeia de conexão da conta de armazenamento e o nome do contêiner de BLOB. A cadeia de conexão pode ser encontrada no portal do Azure dentro das **chaves de acesso**da sua conta de armazenamento. 

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

1. Clique em **Enviar** para executar a solicitação.  Você deve obter um **status: 201 mensagem** criada como uma resposta.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Criar um habilidades e uma loja de conhecimento

A terceira solicitação usa a [API criar](https://docs.microsoft.com/rest/api/searchservice/create-skillset)conjunto de qualificações, criando um objeto de Azure Search que especifica quais habilidades cognitivas deve ser chamada, como encadear habilidades e, mais importante, para este passo-a-como especificar uma loja de conhecimento.

1. Na URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`, substitua `YOUR-AZURE-SEARCH-SERVICE-NAME` pelo nome do serviço de pesquisa. 

1. Na seção de cabeçalho, substitua `<YOUR AZURE SEARCH ADMIN API-KEY>` por uma chave de API de administrador para Azure Search.

1. Na seção corpo, o documento JSON é uma definição de contratação de conhecimento. Recolhido para visibilidade, o Shell externo de um conconhecimento consiste nos seguintes elementos. A `skills` coleção define os aprimoramentos na memória, mas a `knowledgeStore` definição especifica como a saída é armazenada. A `cognitiveServices` definição é sua conexão com os mecanismos de enriquecimento de ia.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. `cognitiveServices` Expanda `knowledgeStore` e para que você possa fornecer informações de conexão. No exemplo, essas cadeias de caracteres estão localizadas após a definição do Configurador de habilidades, em direção ao final do corpo da solicitação. 

   Para `cognitiveServices`, provisione um recurso na camada S0, localizado na mesma região que Azure Search. Você pode obter o nome e a chave de cognitivaservices na mesma página do portal do Azure. 
   
   Para `knowledgeStore`o, você pode usar a mesma cadeia de conexão usada para o contêiner de blob caselaw.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Expanda a coleção de habilidades, particularmente as habilidades do shaper nas linhas 85 e 179, respectivamente. A habilidade de Modelador é importante porque monta as estruturas de dados que você deseja para a mineração de conhecimento. Durante a execução do confinamento, essas estruturas são apenas na memória, mas à medida que você passa para a próxima etapa, verá como essa saída pode ser salva em uma loja de conhecimento para explorar ainda mais.

   O trecho a seguir é da linha 217. 

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

1. Expanda `projections` o `knowledgeStore`elemento em, começando na linha 262. As projeções especificam a composição da loja de conhecimento. As projeções são especificadas em pares Tables-Objects, mas atualmente só uma vez. Como você pode ver na primeira projeção, `tables` é especificado, `objects` mas não é. Na segunda, é o oposto.

   No armazenamento do Azure, as tabelas serão criadas no armazenamento de tabelas para cada tabela que você criar e cada objeto obterá um contêiner no armazenamento de BLOBs.

   Os objetos de blob normalmente contêm a expressão completa de um enriquecimento. As tabelas normalmente contêm aprimoramentos parciais, em combinações que você organiza para fins específicos. Este exemplo mostra uma tabela de casos e uma tabela de opiniões, mas não mostradas são outras tabelas, como entidades, advogados, juízes e partes.

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

1. Clique em **Enviar** para executar a solicitação. A resposta deve ser **201** e ser semelhante ao exemplo a seguir, mostrando a primeira parte da resposta.

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

A quarta solicitação usa a [API criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer), criando um Azure Search indexador. Um indexador é o mecanismo de execução do pipeline de indexação. Todas as definições que você criou até agora são colocadas em movimento com esta etapa.

1. Na URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`, substitua `YOUR-AZURE-SEARCH-SERVICE-NAME` pelo nome do serviço de pesquisa. 

1. Na seção de cabeçalho, substitua `<YOUR AZURE SEARCH ADMIN API-KEY>` por uma chave de API de administrador para Azure Search.

1. Na seção corpo, o documento JSON especifica o nome do indexador. Uma fonte de dados e um índice são exigidos pelo indexador. Um contextset é opcional para um indexador, mas necessário para o enriquecimento do ia.

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

1. Expanda outputFieldMappings. Em contraste com fieldMappings, que são usadas para mapeamento personalizado entre campos em uma fonte de dados e campos em um índice, os outputFieldMappings são usados para mapear campos aprimorados, criados e preenchidos pelo pipeline, para os campos de saída em um índice ou projeção.

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

1. Clique em **Enviar** para executar a solicitação. A resposta deve ser **201** e o corpo da resposta deve parecer quase idêntico ao conteúdo da solicitação que você forneceu (arrumado para fins de brevidade).

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

## <a name="explore-knowledge-store"></a>Explorar o repositório de conhecimento

Você pode começar a explorar assim que o primeiro documento for importado. Para essa tarefa, use [**Gerenciador de armazenamento**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) no Portal.

É importante perceber que uma loja de conhecimento está totalmente desanexada da Azure Search. O índice de Azure Search e a loja de conhecimento contêm a representação de dados e o conteúdo, mas as maneiras de fazer parte. Use o índice para pesquisa de texto completo, pesquisa filtrada e todos os cenários com suporte no Azure Search. Ou então, avance com apenas sua loja de conhecimento, anexando outras ferramentas para analisar o conteúdo.

## <a name="takeaways"></a>Conclusões

Agora você criou sua primeira loja de conhecimento no armazenamento do Azure e usou Gerenciador de Armazenamento para exibir os aprimoramentos. Essa é a experiência fundamental para trabalhar com aprimoramentos armazenados. 

## <a name="next-steps"></a>Passos seguintes

A habilidade de Modelador faz o trabalho pesado de criar formulários de dados granulares que podem ser combinados em novas formas. Como uma próxima etapa, examine a página de referência para essa habilidade para obter detalhes sobre como ela é usada.

> [!div class="nextstepaction"]
> [Referência de habilidade do modelador](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->
