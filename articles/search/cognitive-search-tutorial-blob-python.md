---
title: 'Tutorial: Criar um skillset em Python usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Passe por um exemplo de extração de dados, linguagem natural e processamento de IA de imagem em Pesquisa Cognitiva Azure usando um caderno Jupyter Python. Os dados extraídos são indexados e facilmente acedidos por consulta.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: d9ae7f4b7dd8b0f45ae02bd2a90aca78127fd3d3
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472405"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-rest-and-python"></a>Tutorial: Criar um oleoduto de enriquecimento de IA usando REST e Python

Neste tutorial, aprende-se a mecânica do enriquecimento de dados de programação na Pesquisa Cognitiva Azure utilizando *habilidades cognitivas.* As competências são apoiadas pelo processamento de linguagem natural (NLP) e pelas capacidades de análise de imagem nos Serviços Cognitivos. Através da composição e configuração do skillset, pode extrair representações de texto e texto de uma imagem ou ficheiro de documento digitalizado. Também pode detetar linguagem, entidades, frases-chave e muito mais. O resultado é rico em conteúdo adicional num índice de pesquisa, criado com enriquecimentos de IA num pipeline indexante. 

Neste tutorial, você usará Python para fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplicar competências incorporadas: reconhecimento de entidades, deteção de idioma, manipulação de texto e extração de expressões-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

A saída é um índice pesquisável de texto completo na Pesquisa Cognitiva Azure. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md). 

Este tutorial funciona no serviço Gratuito, mas o número de transações gratuitas é limitado a 20 documentos por dia. Se quiser executar este tutorial mais de uma vez no mesmo dia, elimine o indexante para repor o contador.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste tutorial. 

+ [Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) de armazenamento Azure para armazenar os dados da amostra. Certifique-se de que a conta de armazenamento está na mesma região que a Pesquisa Cognitiva Azure.

+ [Anaconda 3.x,](https://www.anaconda.com/distribution/#download-section)fornecendo Cadernos Python 3.x e Jupyter.

+ [Os dados](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) da amostra consistem num pequeno conjunto de ficheiros de diferentes tipos. 

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Você pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

Para interagir com o seu serviço de Pesquisa Cognitiva Azure, você precisará do URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Uma chave válida estabelece confiança, por pedido, entre o pedido que envia o pedido e o serviço que o trata.

## <a name="prepare-sample-data"></a>Preparar dados da amostra

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Os dados de origem devem ter origem num tipo de fonte de dados suportado de um [indexador](search-indexer-overview.md)de pesquisa cognitiva Azure . Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. [Inscreva-se no portal Azure,](https://portal.azure.com)navegue na sua conta de armazenamento Azure, clique em **Blobs,** e depois clique em **+ Recipiente**.

1. [Crie um recipiente Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados da amostra. Pode definir o Nível de Acesso Público a qualquer um dos seus valores válidos.

1. Depois de criado o recipiente, abra-o e selecione **Upload** na barra de comando para carregar os ficheiros de amostra que descarregou num passo anterior.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois de os ficheiros de exemplo serem carregados, obtenha o nome do contentor e uma cadeia de ligação para o seu armazenamento de Blobs. Pode fazê-lo ao navegar até à sua conta de armazenamento no portal do Azure. Clique em **teclas de acesso**e, em seguida, copie o campo **de cordas de ligação.**

A cadeia de ligação terá este formato: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Mantenha a corda de ligação à mão. Vai precisar num passo futuro.

Existem outras formas de especificar a cadeia de ligação, por exemplo, fornecer uma assinatura de acesso partilhada. Para saber mais sobre as credenciais da origem de dados, veja [Indexar o Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Criar um bloco de notas do Jupyter

> [!Note]
> Este artigo mostra-lhe como construir uma fonte de dados, índice, indexante e skillset usando uma série de scripts Python. Para descarregar o exemplo completo do portátil, vá ao repo de amostras de [azure-search-python](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Use a Anaconda Navigator para lançar o Jupyter Notebook e criar um novo caderno Python 3.

## <a name="connect-to-azure-cognitive-search"></a>Ligue-se à Pesquisa Cognitiva Azure

No seu caderno, execute este script para carregar as bibliotecas utilizadas para trabalhar com a JSON e formular pedidos HTTP.

```python
import json
import requests
from pprint import pprint
```

Em seguida, defina os nomes para a fonte de dados, índice, indexante e skillset. Executa este guião para definir os nomes para este tutorial.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> Num serviço gratuito, está limitado a três índices, indexadores e fontes de dados. Este tutorial cria um de cada. Certifique-se de que tem espaço para criar novos objetos antes de ir mais longe.

No seguinte script, substitua os espaços reservados para o seu serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) e a chave API de administrador (YOUR-ADMIN-API-KEY) e, em seguida, execute-os para configurar o ponto final do serviço de pesquisa.

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

Agora que os seus serviços e ficheiros de origem estão preparados, comece a reunir os componentes do pipeline de indexação. Comece com um objeto de fonte de dados que diga ao Azure Cognitive Search como recuperar dados de origem externa.

No seguinte script, substitua o espaço reservado YOUR-BLOB-RESOURCE-CONNECTION-STRING com a cadeia de ligação para a bolha que criou no passo anterior. Em seguida, executar o script para criar uma fonte de dados chamada `cogsrch-py-datasource`.

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

O pedido deve devolver um código de estado de 201 confirmando o sucesso.

No portal Azure, na página do painel de instrumentos de pesquisa, verifique se a fonte de dados cogsrch-py aparece na lista de fontes de **dados.** Clique em **Refresh** para atualizar a página.

![Fontes de dados azulejos no portal](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Fontes de dados azulejos no portal")

## <a name="create-a-skillset"></a>Criar um conjunto de competências

Neste passo, definirá um conjunto de passos de enriquecimento para aplicar aos seus dados. Um passo de melhoramento é denominado *competência* e o conjunto de passos de melhoramento é denominado *conjunto de competências*. Este tutorial usa [habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) para o skillset:

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de Texto](cognitive-search-skill-textsplit.md) para dividir conteúdo grande em segmentos mais pequenos antes de chamar a competência de extração de expressões-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de entidades](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdos no recipiente blob.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves. 

### <a name="python-script"></a>Roteiro python
Execute o seguinte script para criar uma habilidade chamada `cogsrch-py-skillset`.

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

O pedido deve devolver um código de estado de 201 confirmando o sucesso.

A habilidade de extração da frase-chave é aplicada para cada página. Definindo o contexto para `"document/pages/*"`, executa este enriquecedor para cada membro da matriz de documento/páginas (para cada página do documento).

Cada competência é executada no conteúdo do documento. Durante o processamento, a Pesquisa Cognitiva Azure quebra cada documento para ler conteúdo a partir de diferentes formatos de ficheiros. O texto encontrado no ficheiro fonte é colocado num campo `content`, um para cada documento. Por isso, coloque a entrada como `"/document/content"`.

Veja a seguir uma representação gráfica do conjunto de competências.

![Compreender uma habilidade](media/cognitive-search-tutorial-blob/skillset.png "Compreender uma habilidade")

As saídas podem ser mapeadas para um índice, usado como entrada para uma habilidade a jusante, ou ambos, como é o caso do código linguístico. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Criar um índice

Nesta secção, define o esquema do índice especificando os campos a incluir no índice pesquisável e definindo os atributos de pesquisa para cada campo. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

Neste exercício, utiliza os seguintes campos e tipos de campo:

| nomes de campo: | ID         | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| tipos de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Execute este script para criar o índice chamado `cogsrch-py-index`.

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

O pedido deve devolver um código de estado de 201 confirmando o sucesso.

Para saber mais sobre a definição de um índice, consulte [Create Index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Criar um indexador, mapear campos e executar transformações

Até agora, criou uma fonte de dados, um conjunto de habilidades e um índice. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. Para ligar estes objetos num indexante, deve definir mapeamentos de campo.

+ Os mapeamentos de campo são processados antes do skillset, mapeando os campos de origem da fonte de dados para os campos-alvo em um índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, não é necessário mapeamento.

+ Os outputFieldMappings são processados após o skillset, referenciando sourceFieldNames que não existem até que a rachadura de documentos ou o enriquecimento os crie. O targetFieldName é um campo num índice.

Além de ligar as inputs às saídas, também pode usar mapeamentos de campo para aplainar estruturas de dados. Para mais informações, consulte [Como mapear campos enriquecidos para um índice pesquisável](cognitive-search-output-field-mapping.md).

Execute este script para criar um indexante chamado `cogsrch-py-indexer`.

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

O pedido deve devolver rapidamente um código de estado de 201, no entanto, o processamento pode demorar vários minutos a ser concluído. Embora o conjunto de dados seja pequeno, as habilidades analíticas, como a análise de imagem, são computacionalmente intensivas e levam tempo.

Utilize o script de estado do [indexante de verificação](#check-indexer-status) na secção seguinte para determinar quando o processo do indexante está concluído.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver algum problema de acesso aos dados, de mapear inputs e saídas, ou com a ordem de operações, aparecerá nesta fase. Para reexecutar o pipeline com alterações de código ou script, poderá ser necessário eliminar objetos primeiro. Para obter mais informações, veja [Repor e executar novamente](#reset).

#### <a name="explore-the-request-body"></a>Explorar o corpo do pedido

O script define `"maxFailedItems"` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também a instrução `"dataToExtract":"contentAndMetadata"` nos parâmetros de configuração. Esta declaração diz ao indexante para extrair o conteúdo de diferentes formatos de ficheiros e dos metadados relacionados com cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. A configuração `"imageAction":"generateNormalizedImages"`, combinada com a Habilidade de Habilidade oCR e a Habilidade de Fusão de Texto, diz ao indexante para extrair texto das imagens (por exemplo, a palavra "parar" de um sinal de paragem de tráfego), e incorpora-o como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (pense numa imagem dentro de um PDF)e imagens encontradas na fonte de dados, por exemplo, um ficheiro JPG.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Verificar o estado do indexador

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se o processamento do indexador está completo, execute o seguinte script.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Na resposta, monitorize o "último Resultado" pelos seus valores de "status" e "endTime". Verifique periodicamente o script para verificar o estado. Quando o indexante estiver concluído, o estado será definido para "sucesso", será especificado um "tempo final", e a resposta incluirá quaisquer erros e avisos que ocorreram durante o enriquecimento.

![Indexer é criado](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexer é criado")

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Neste tutorial, os avisos são benignos. Por exemplo, um dos ficheiros JPEG que não tem texto mostrará o aviso nesta imagem.

![Alerta indicador de exemplo](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Alerta indicador de exemplo")

## <a name="query-your-index"></a>Consultar o índice

Depois de concluída a indexação, execute consultas que devolvam os conteúdos dos campos individuais. Por padrão, a Pesquisa Cognitiva Azure devolve os 50 melhores resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter instruções, consulte [como página resultados em Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

Como passo de verificação, consulte o índice de todos os campos.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Os resultados devem ser semelhantes aos seguintes exemplos. A imagem só mostra uma parte da resposta.

![Índice de consulta para todos os campos](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Consulta do índice para todos os campos")

O resultado é o esquema de índice, com o nome, o tipo e os atributos de cada campo.

Submeta uma segunda consulta para `"*"` devolver todos os conteúdos de um único campo, tal como `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Os resultados devem ser semelhantes aos seguintes exemplos. A imagem só mostra uma parte da resposta.

![Índice de consulta para o conteúdo das organizações](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Consulta do índice para devolver o conteúdo das organizações")

Repita para campos adicionais: conteúdo, idiomaCódigo, tecladoS e organizações neste exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

Pode utilizar GET ou POST, dependendo da complexidade da cadeia de consulta e do comprimento. Para obter mais informações, veja [Consultar através da API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais do desenvolvimento do gasoduto, a abordagem mais prática para iterações de design é apagar os objetos da Pesquisa Cognitiva Azure e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Para voltar a indexar os documentos com as novas definições:

1. Elimine o índice para remover os dados persistentes. Elimine o indexador para recriá-lo no seu serviço.
2. Modificar as definições de skillset e index.
3. Volte a criar um índice e o indexador no serviço para executar o pipeline.

Pode utilizar o portal para eliminar índices, indexadores e habilidades. Ao eliminar o indexante, pode eliminar opcionalmente, seletivamente, o índice, a skillset e a fonte de dados ao mesmo tempo.

![Eliminar objetos de pesquisa](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Eliminar objetos de pesquisa no portal")

Também pode eliminá-los usando um script. O seguinte script eliminará a habilidade que criamos. Pode modificar facilmente o pedido de eliminação do índice, indexador e fonte de dados.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

Foram introduzidas [competências incorporadas,](cognitive-search-predefined-skills.md) juntamente com definições de skillset e uma forma de cadeiar competências através de inputs e saídas. Também descobriu que `outputFieldMappings` na definição de indexante é necessário para encaminhar valores enriquecidos do oleoduto para um índice pesquisável num serviço de Pesquisa Cognitiva Azure.

Finalmente, aprendeu a testar os resultados e a redefinir o sistema para mais iterações. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Nesta versão, não há um mecanismo para visualizar as construções internas (documentos melhorados criados pelo sistema). Também aprendeu a verificar o estado do indexador e que objetos devem ser eliminados antes de reexecutar um gasoduto.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de limpar depois de um tutorial é apagando o grupo de recursos que contém o serviço de Pesquisa Cognitiva Azure e o serviço Azure Blob. Assumindo que coloca ambos os serviços no mesmo grupo, apague o grupo de recursos para apagar permanentemente tudo o que nele está, incluindo os serviços e qualquer conteúdo armazenado que tenha criado para este tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu.

> [!div class="nextstepaction"]
> [Exemplo: Criar uma habilidade personalizada para enriquecimento de IA](cognitive-search-create-custom-skill-example.md)
