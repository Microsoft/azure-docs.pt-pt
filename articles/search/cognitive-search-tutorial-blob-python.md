---
title: 'Tutorial: Create a skillset in Python using REST APIs'
titleSuffix: Azure Cognitive Search
description: Step through an example of data extraction, natural language, and image AI processing in Azure Cognitive Search using a Jupyter Python notebook. Extracted data is indexed and easily accessed by query.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: d7b4755bb2e69c4a852901b71d917c6baa5d63ae
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406422"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-rest-and-python"></a>Tutorial: Create an AI enrichment pipeline using REST and Python

In this tutorial, you learn the mechanics of programming data enrichment in Azure Cognitive Search using *cognitive skills*. Skills are backed by natural language processing (NLP) and image analysis capabilities in Cognitive Services. Through skillset composition and configuration, you can extract text and text representations of an image or scanned document file. You can also detect language, entities, key phrases, and more. The result is rich additional content in a search index, created with AI enrichments in an indexing pipeline. 

In this tutorial, you'll use Python to do the following tasks:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplicar competências incorporadas: reconhecimento de entidades, deteção de idioma, manipulação de texto e extração de expressões-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

Output is a full text searchable index on Azure Cognitive Search. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md). 

This tutorial runs on the Free service, but the number of free transactions is limited to 20 documents per day. If you want to run this tutorial more than once in the same day, use a smaller file set so that you can fit in more runs.

> [!NOTE]
> As you expand scope by increasing the frequency of processing, adding more documents, or adding more AI algorithms, you will need to [attach a billable Cognitive Services resource](cognitive-search-attach-cognitive-services.md). Charges accrue when calling APIs in Cognitive Services, and for image extraction as part of the document-cracking stage in Azure Cognitive Search. There are no charges for text extraction from documents.
>
> Execution of built-in skills is charged at the existing [Cognitive Services pay-as-you go price](https://azure.microsoft.com/pricing/details/cognitive-services/). Image extraction pricing is described on the [Azure Cognitive Search pricing page](https://go.microsoft.com/fwlink/?linkid=2042400).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

The following services, tools, and data are used in this tutorial. 

+ [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) for storing the sample data. Make sure the storage account is in the same region as Azure Cognitive Search.

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), providing Python 3.x and Jupyter Notebooks.

+ [Sample data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consists of a small file set of different types. 

+ [Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this tutorial.

## <a name="get-a-key-and-url"></a>Get a key and URL

To interact with your Azure Cognitive Search service, you will need the service URL and an access key. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. A valid key establishes trust, on a per request basis, between the application sending the request and the service that handles it.

## <a name="prepare-sample-data"></a>Prepare sample data

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Source data must originate from a supported data source type of an [Azure Cognitive Search indexer](search-indexer-overview.md). Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. [Sign in to the Azure portal](https://portal.azure.com), navigate to your Azure storage account, click **Blobs**, and then click **+ Container**.

1. [Create a Blob container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) to contain sample data. You can set the Public Access Level to any of its valid values.

1. After the container is created, open it and select **Upload** on the command bar to upload the sample files you downloaded in a previous step.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois de os ficheiros de exemplo serem carregados, obtenha o nome do contentor e uma cadeia de ligação para o seu armazenamento de Blobs. Pode fazê-lo ao navegar até à sua conta de armazenamento no portal do Azure. Click **Access keys**, and then copy the **Connection String**  field.

The connection string will have this format: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Keep the connection string handy. You will need it in a future step.

Existem outras formas de especificar a cadeia de ligação, por exemplo, fornecer uma assinatura de acesso partilhada. Para saber mais sobre as credenciais da origem de dados, veja [Indexar o Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Criar um bloco de notas do Jupyter

> [!Note]
> This article shows you how to build a data source, index, indexer and skillset using a series of Python scripts. To download the complete notebook example, go to the [Azure-Search-python-samples repo](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Use Anaconda Navigator to launch Jupyter Notebook and create a new Python 3 notebook.

## <a name="connect-to-azure-cognitive-search"></a>Connect to Azure Cognitive Search

In your notebook, run this script to load the libraries used for working with JSON and formulating HTTP requests.

```python
import json
import requests
from pprint import pprint
```

Next, define the names for the data source, index, indexer, and skillset. Run this script to set up the names for this tutorial.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> On a free service, you are limited to three indexes, indexers, and data sources. Este tutorial cria um de cada. Make sure you have room to create new objects before going any further.

In the following script, replace the placeholders for your search service (YOUR-SEARCH-SERVICE-NAME) and admin API key (YOUR-ADMIN-API-KEY), and then run it to set up the search service endpoint.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Criar uma origem de dados

Agora que os seus serviços e ficheiros de origem estão preparados, comece a reunir os componentes do pipeline de indexação. Begin with a data source object that tells Azure Cognitive Search how to retrieve external source data.

In the following script, replace the placeholder YOUR-BLOB-RESOURCE-CONNECTION-STRING with the connection string for the blob you created in the previous step. Then, run the script to create a data source named `cogsrch-py-datasource`.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

The request should return a status code of 201 confirming success.

In the Azure portal, on the search service dashboard page, verify that the cogsrch-py-datasource appears in the **Data sources** list. Click **Refresh** to update the page.

![Data sources tile in the portal](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Data sources tile in the portal")

## <a name="create-a-skillset"></a>Criar um conjunto de competências

In this step, you will define a set of enrichment steps to apply to your data. Um passo de melhoramento é denominado *competência* e o conjunto de passos de melhoramento é denominado *conjunto de competências*. This tutorial uses [built-in cognitive skills](cognitive-search-predefined-skills.md) for the skillset:

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de Texto](cognitive-search-skill-textsplit.md) para dividir conteúdo grande em segmentos mais pequenos antes de chamar a competência de extração de expressões-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Entity Recognition](cognitive-search-skill-entity-recognition.md) for extracting the names of organizations from content in the blob container.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves. 

### <a name="python-script"></a>Python script
Run the following script to create a skillset called `cogsrch-py-skillset`.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

The request should return a status code of 201 confirming success.

The key phrase extraction skill is applied for each page. By setting the context to `"document/pages/*"`, you run this enricher for each member of the document/pages array (for each page in the document).

Cada competência é executada no conteúdo do documento. During processing, Azure Cognitive Search cracks each document to read content from different file formats. Text found in the source file is placed into a `content` field, one for each document. Therefore, set the input as `"/document/content"`.

Veja a seguir uma representação gráfica do conjunto de competências.

![Understand a skillset](media/cognitive-search-tutorial-blob/skillset.png "Understand a skillset")

Outputs can be mapped to an index, used as input to a downstream skill, or both, as is the case with language code. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Criar um índice

In this section, you define the index schema by specifying the fields to include in the searchable index, and setting the search attributes for each field. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

Neste exercício, utiliza os seguintes campos e tipos de campo:

| nomes de campo: | ID         | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| tipos de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Run this script to create the index named `cogsrch-py-index`.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

The request should return a status code of 201 confirming success.

To learn more about defining an index, see [Create Index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Criar um indexador, mapear campos e executar transformações

So far, you have created a data source, a skillset, and an index. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. To tie these objects together in an indexer, you must define field mappings.

+ The fieldMappings are processed before the skillset, mapping source fields from the data source to target fields in an index. If field names and types are the same at both ends, no mapping is required.

+ The outputFieldMappings are processed after the skillset, referencing sourceFieldNames that don't exist until document cracking or enrichment creates them. The targetFieldName is a field in an index.

Besides hooking up inputs to outputs, you can also use field mappings to flatten data structures. For more information, see [How to map enriched fields to a searchable index](cognitive-search-output-field-mapping.md).

Run this script to create an indexer named `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

The request should quickly return a status code of 201, however, the processing can take several minutes to complete. Although the data set is small, analytical skills, such as image analysis, are computationally intensive and take time.

Use the [Check indexer status](#check-indexer-status) script in the next section to determine when the indexer process is complete.

> [!TIP]
> A criação de um indexador invoca o pipeline. If there is a problem accessing the data, mapping inputs and outputs, or with the order of operations, it will appear at this stage. To re-run the pipeline with code or script changes, you may need to delete objects first. Para obter mais informações, veja [Repor e executar novamente](#reset).

#### <a name="explore-the-request-body"></a>Explorar o corpo do pedido

O script define `"maxFailedItems"` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também a instrução `"dataToExtract":"contentAndMetadata"` nos parâmetros de configuração. This statement tells the indexer to  extract the content from different file formats and the metadata related to each file.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. The `"imageAction":"generateNormalizedImages"` configuration, combined with the OCR Skill and Text Merge Skill, tells the indexer to extract text from the images (for example, the word "stop" from a traffic Stop sign), and embed it as part of the content field. This behavior applies to both the images embedded in the documents (think of an image inside a PDF)and images found in the data source, for instance a JPG file.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Verificar o estado do indexador

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. To find out whether the indexer  processing is complete, run the following script.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

In the response, monitor the "lastResult" for its "status" and "endTime" values. Periodically run the script to check the status. When the indexer has completed, the status will be set to "success", an "endTime" will be specified, and the response will include any errors and warnings that occurred during enrichment.

![Indexer is created](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexer is created")

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. In this tutorial, the warnings are benign. For example, one of the JPEG files that does not have text will show the warning in this screenshot.

![Example indexer warning](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Example indexer warning")

## <a name="query-your-index"></a>Consultar o índice

Depois de concluída a indexação, execute consultas que devolvam os conteúdos dos campos individuais. By default, Azure Cognitive Search returns the top 50 results. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. For instructions, see [How to page results in Azure Cognitive Search](search-pagination-page-layout.md).

Como passo de verificação, consulte o índice de todos os campos.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

The results should look similar to the following example. The screenshot only shows a part of the response.

![Query index for all fields](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Query the index for all fields")

O resultado é o esquema de índice, com o nome, o tipo e os atributos de cada campo.

Submeta uma segunda consulta para `"*"` devolver todos os conteúdos de um único campo, tal como `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

The results should look similar to the following example. The screenshot only shows a part of the response.

![Query index for the contents of organizations](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Query the index to return the contents of organizations")

Repeat for additional fields: content, languageCode, keyPhrases, and organizations in this exercise. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

Pode utilizar GET ou POST, dependendo da complexidade da cadeia de consulta e do comprimento. Para obter mais informações, veja [Consultar através da API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

In the early experimental stages of pipeline development, the most practical approach for design iterations is to delete the objects from Azure Cognitive Search and allow your code to rebuild them. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Para voltar a indexar os documentos com as novas definições:

1. Elimine o índice para remover os dados persistentes. Elimine o indexador para recriá-lo no seu serviço.
2. Modify the skillset and index definitions.
3. Volte a criar um índice e o indexador no serviço para executar o pipeline.

You can use the portal to delete indexes, indexers, and skillsets. When you delete the indexer, you can optionally, selectively delete the index, skillset, and data source at the same time.

![Delete search objects](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Delete search objects in the portal")

You can also delete them using a script. The following script will delete the skillset we created. You can easily modify the request to delete the index, indexer, and data source.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

[Built-in skills](cognitive-search-predefined-skills.md) were introduced, along with skillset definitions and a way to chain skills together through inputs and outputs. You also learned that `outputFieldMappings` in the indexer definition is required for routing enriched values from the pipeline into a searchable index on an Azure Cognitive Search service.

Finally, you learned how to test the results and reset the system for further iterations. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Nesta versão, não há um mecanismo para visualizar as construções internas (documentos melhorados criados pelo sistema). You also learned how to check the indexer status and what  objects must be deleted before rerunning a pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

The fastest way to clean up after a tutorial is by deleting the resource group containing the Azure Cognitive Search service and Azure Blob service. Assuming you put both services in the same group, delete the resource group to permanently delete everything in it, including the services and any stored content that you created for this tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu.

> [!div class="nextstepaction"]
> [Example: Creating a custom skill for AI enrichment](cognitive-search-create-custom-skill-example.md)
