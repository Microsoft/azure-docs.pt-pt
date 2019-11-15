---
title: 'Tutorial: criar um conconhecimento em Python usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Percorra um exemplo de extração de dados, linguagem natural e processamento de ia de imagem no Azure Pesquisa Cognitiva usando um notebook Jupyter Python. Os dados extraídos são indexados e acessados facilmente por consulta.
manager: nitinme
author: LisaLeib
ms.author: v-lilei
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 1e404998c8f49852248a754e7134f439dcdf5b04
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113674"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-rest-and-python"></a>Tutorial: criar um pipeline de enriquecimento de ia usando REST e Python

Neste tutorial, você aprende a mecânica do enriquecimento de dados de programação no Azure Pesquisa Cognitiva usando *habilidades cognitivas*. As habilidades são apoiadas por recursos de análise de imagem e NLP (processamento de idioma natural) em serviços cognitivas. Por meio de composição e configuração do consentset, você pode extrair texto e representações de texto de uma imagem ou arquivo de documento digitalizado. Você também pode detectar idioma, entidades, frases-chave e muito mais. O resultado é um conteúdo adicional avançado em um índice de pesquisa, criado com os aprimoramentos de ia em um pipeline de indexação. 

Neste tutorial, você usará o Python para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplicar competências incorporadas: reconhecimento de entidades, deteção de idioma, manipulação de texto e extração de expressões-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

A saída é um índice pesquisável de texto completo no Azure Pesquisa Cognitiva. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md). 

Este tutorial é executado no serviço gratuito, mas o número de transações gratuitas é limitado a 20 documentos por dia. Se você quiser executar este tutorial mais de uma vez no mesmo dia, use um conjunto de arquivos menor para que você possa se ajustar a mais execuções.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Pesquisa Cognitiva. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços do Azure pesquisa cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste tutorial. 

+ [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. Verifique se a conta de armazenamento está na mesma região que o Azure Pesquisa Cognitiva.

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), fornecendo blocos de anotações do Python 3. x e do Jupyter.

+ [Os dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consistem em um pequeno conjunto de arquivos de tipos diferentes. 

+ [Crie um serviço de pesquisa cognitiva do Azure](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

Para interagir com o serviço de Pesquisa Cognitiva do Azure, você precisará da URL do serviço e de uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Pesquisa Cognitiva à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações exigem uma chave de API em cada solicitação enviada ao seu serviço. Uma chave válida estabelece confiança, por solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Os dados de origem devem originar-se de um tipo de fonte de dados com suporte de um [indexador pesquisa cognitiva do Azure](search-indexer-overview.md). Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento do Azure, clique em **BLOBs**e, em seguida, clique em **+ contêiner**.

1. [Crie um contêiner de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Você pode definir o nível de acesso público para qualquer um de seus valores válidos.

1. Depois que o contêiner for criado, abra-o e selecione **carregar** na barra de comandos para carregar os arquivos de exemplo que você baixou em uma etapa anterior.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois de os ficheiros de exemplo serem carregados, obtenha o nome do contentor e uma cadeia de ligação para o seu armazenamento de Blobs. Pode fazê-lo ao navegar até à sua conta de armazenamento no portal do Azure. Clique em **chaves de acesso**e copie o campo cadeia de **conexão** .

A cadeia de conexão terá este formato: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Mantenha a cadeia de conexão à mão. Você precisará dela em uma etapa futura.

Existem outras formas de especificar a cadeia de ligação, por exemplo, fornecer uma assinatura de acesso partilhada. Para saber mais sobre as credenciais da origem de dados, veja [Indexar o Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Criar um bloco de notas do Jupyter

> [!Note]
> Este artigo mostra como criar uma fonte de dados, um índice, um indexador e um conjunto de qualificações usando uma série de scripts do Python. Para baixar o exemplo de notebook completo, vá para o [repositório Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Use o Anaconda Navigator para iniciar o Jupyter Notebook e criar um novo notebook Python 3.

## <a name="connect-to-azure-cognitive-search"></a>Conectar-se ao Azure Pesquisa Cognitiva

No seu bloco de anotações, execute este script para carregar as bibliotecas usadas para trabalhar com solicitações HTTP JSON e formular.

```python
import json
import requests
from pprint import pprint
```

Em seguida, defina os nomes para a fonte de dados, o índice, o indexador e o skillset. Execute este script para configurar os nomes deste tutorial.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> Em um serviço gratuito, você está limitado a três índices, indexadores e fontes de dados. Este tutorial cria um de cada. Verifique se você tem espaço para criar novos objetos antes de continuar.

No script a seguir, substitua os espaços reservados para o serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) e a chave de API de administração (YOUR-ADMIN-API-KEY) e execute-o para configurar o ponto de extremidade do serviço de pesquisa.

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

Agora que os seus serviços e ficheiros de origem estão preparados, comece a reunir os componentes do pipeline de indexação. Comece com um objeto de fonte de dados que informa ao Azure Pesquisa Cognitiva como recuperar dados de origem externos.

No script a seguir, substitua o espaço reservado YOUR-BLOB-RESOURCE-CONNECTION-STRING pela cadeia de conexão para o blob que você criou na etapa anterior. Em seguida, execute o script para criar uma fonte de dados chamada `cogsrch-py-datasource`.

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

A solicitação deve retornar um código de status de 201 confirmando o sucesso.

Na portal do Azure, na página painel do serviço de pesquisa, verifique se cogsrch-py-DataSource aparece na lista **fontes de dados** . Clique em **Atualizar** para atualizar a página.

![Bloco de fontes de dados no portal](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Bloco de fontes de dados no portal")

## <a name="create-a-skillset"></a>Criar um conjunto de competências

Nesta etapa, você definirá um conjunto de etapas de enriquecimento para aplicar aos seus dados. Um passo de melhoramento é denominado *competência* e o conjunto de passos de melhoramento é denominado *conjunto de competências*. Este tutorial usa [habilidades cognitivas internas](cognitive-search-predefined-skills.md) para o contratador de habilidades:

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de Texto](cognitive-search-skill-textsplit.md) para dividir conteúdo grande em segmentos mais pequenos antes de chamar a competência de extração de expressões-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) para extrair os nomes de organizações do conteúdo no contêiner de BLOB.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves. 

### <a name="python-script"></a>Script Python
Execute o script a seguir para criar um conseqüência de qualificações chamado `cogsrch-py-skillset`.

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

A solicitação deve retornar um código de status de 201 confirmando o sucesso.

A habilidade de extração de frase-chave é aplicada para cada página. Ao definir o contexto como `"document/pages/*"`, você executa esse aprimoramento para cada membro da matriz de documentos/páginas (para cada página no documento).

Cada competência é executada no conteúdo do documento. Durante o processamento, o Azure Pesquisa Cognitiva rachadura cada documento para ler o conteúdo de diferentes formatos de arquivo. O texto encontrado no arquivo de origem é colocado em um campo de `content`, um para cada documento. Portanto, defina a entrada como `"/document/content"`.

Veja a seguir uma representação gráfica do conjunto de competências.

![Entender um qualificable](media/cognitive-search-tutorial-blob/skillset.png "Entender um qualificable")

As saídas podem ser mapeadas para um índice, usado como entrada para uma habilidade de downstream, ou ambos, como é o caso com o código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Criar um índice

Nesta seção, você define o esquema de índice especificando os campos a serem incluídos no índice pesquisável e definindo os atributos de pesquisa para cada campo. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

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

A solicitação deve retornar um código de status de 201 confirmando o sucesso.

Para saber mais sobre como definir um índice, confira [criar índice (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Criar um indexador, mapear campos e executar transformações

Até agora, você criou uma fonte de dados, um condador e um índice. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. Para vincular esses objetos em um indexador, você deve definir mapeamentos de campo.

+ Os fieldMappings são processados antes do Configurador de habilidades, mapeando os campos de origem da fonte de dados para os campos de destino em um índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

+ Os outputFieldMappings são processados após o contextset, fazendo referência a sourceFieldNames que não existe até que a quebra de documentos ou o enriquecimento os crie. O targetFieldName é um campo em um índice.

Além de conectar entradas a saídas, você também pode usar mapeamentos de campo para mesclar estruturas de dados. Para obter mais informações, consulte [como mapear campos aprimorados para um índice pesquisável](cognitive-search-output-field-mapping.md).

Execute este script para criar um indexador chamado `cogsrch-py-indexer`.

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

A solicitação deve retornar rapidamente um código de status de 201, no entanto, o processamento pode levar vários minutos para ser concluído. Embora o conjunto de dados seja pequeno, as habilidades analíticas, como análise de imagem, são computacionalmente intensivas e demoram tempo.

Use o script [verificar o status do indexador](#check-indexer-status) na próxima seção para determinar quando o processo do indexador foi concluído.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver um problema ao acessar os dados, mapear entradas e saídas ou com a ordem das operações, ele será exibido neste estágio. Para executar novamente o pipeline com alterações de código ou script, talvez seja necessário excluir objetos primeiro. Para obter mais informações, veja [Repor e executar novamente](#reset).

#### <a name="explore-the-request-body"></a>Explorar o corpo do pedido

O script define `"maxFailedItems"` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também a instrução `"dataToExtract":"contentAndMetadata"` nos parâmetros de configuração. Essa instrução informa o indexador para extrair o conteúdo de diferentes formatos de arquivo e os metadados relacionados a cada arquivo.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. A configuração de `"imageAction":"generateNormalizedImages"`, combinada com a habilidade de OCR e habilidades de mesclagem de texto, diz ao indexador para extrair texto das imagens (por exemplo, a palavra "Stop" de um sinal de parada de tráfego) e inseri-la como parte do campo de conteúdo. Esse comportamento se aplica a ambas as imagens inseridas nos documentos (imagine uma imagem dentro de um PDF) e imagens encontradas na fonte de dados, por exemplo, um arquivo JPG.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Verificar o estado do indexador

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para descobrir se o processamento do indexador está concluído, execute o script a seguir.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Na resposta, monitore o "lastResult" para seus valores de "status" e "endTime". Execute periodicamente o script para verificar o status. Quando o indexador for concluído, o status será definido como "success", um "endTime" será especificado e a resposta incluirá todos os erros e avisos ocorridos durante o enriquecimento.

![Indexador é criado](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexador é criado")

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Neste tutorial, os avisos são benignos. Por exemplo, um dos arquivos JPEG que não tem texto mostrará o aviso nesta captura de tela.

![Exemplo de aviso do indexador](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Exemplo de aviso do indexador")

## <a name="query-your-index"></a>Consultar o índice

Depois de concluída a indexação, execute consultas que devolvam os conteúdos dos campos individuais. Por padrão, o Azure Pesquisa Cognitiva retorna os principais resultados de 50. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter instruções, consulte [como paginar resultados no Azure pesquisa cognitiva](search-pagination-page-layout.md).

Como passo de verificação, consulte o índice de todos os campos.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Os resultados devem ser semelhantes ao exemplo a seguir. A captura de tela mostra apenas uma parte da resposta.

![Índice de consulta para todos os campos](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Consultar o índice para todos os campos")

O resultado é o esquema de índice, com o nome, o tipo e os atributos de cada campo.

Submeta uma segunda consulta para `"*"` devolver todos os conteúdos de um único campo, tal como `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Os resultados devem ser semelhantes ao exemplo a seguir. A captura de tela mostra apenas uma parte da resposta.

![Índice de consulta para o conteúdo das organizações](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Consultar o índice para retornar o conteúdo das organizações")

Repita para campos adicionais: conteúdo, languageCode, prefrases e organizações neste exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

Pode utilizar GET ou POST, dependendo da complexidade da cadeia de consulta e do comprimento. Para obter mais informações, veja [Consultar através da API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nos estágios experimentais antecipados do desenvolvimento de pipeline, a abordagem mais prática para iterações de design é excluir os objetos do Pesquisa Cognitiva do Azure e permitir que seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Para voltar a indexar os documentos com as novas definições:

1. Elimine o índice para remover os dados persistentes. Elimine o indexador para recriá-lo no seu serviço.
2. Modifique as definições de conjuntos de qualificações e de índice.
3. Volte a criar um índice e o indexador no serviço para executar o pipeline.

Você pode usar o portal para excluir índices, indexadores e habilidades. Ao excluir o indexador, você pode, opcionalmente, excluir seletivamente o índice, o qualificable e a fonte de dados ao mesmo tempo.

![Excluir objetos de pesquisa](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Excluir objetos de pesquisa no portal")

Você também pode excluí-los usando um script. O seguinte script excluirá o configurador que criamos. Você pode facilmente modificar a solicitação para excluir o índice, o indexador e a fonte de dados.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

As [habilidades internas](cognitive-search-predefined-skills.md) foram introduzidas, juntamente com as definições de conjunto de qualificações e uma maneira de encadear habilidades em entradas e saídas. Você também aprendeu que `outputFieldMappings` na definição do indexador é necessária para rotear valores aprimorados do pipeline para um índice pesquisável em um serviço de Pesquisa Cognitiva do Azure.

Por fim, você aprendeu como testar os resultados e redefinir o sistema para iterações adicionais. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Nesta versão, não há um mecanismo para visualizar as construções internas (documentos melhorados criados pelo sistema). Você também aprendeu como verificar o status do indexador e quais objetos devem ser excluídos antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida de limpar após um tutorial é excluindo o grupo de recursos que contém o serviço de Pesquisa Cognitiva do Azure e o serviço blob do Azure. Supondo que você coloque os dois serviços no mesmo grupo, exclua o grupo de recursos para excluir permanentemente tudo nele, incluindo os serviços e qualquer conteúdo armazenado que você criou para este tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu.

> [!div class="nextstepaction"]
> [Exemplo: criando uma habilidade personalizada para o enriquecimento de ia](cognitive-search-create-custom-skill-example.md)
