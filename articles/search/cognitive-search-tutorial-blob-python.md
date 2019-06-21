---
title: 'Tutorial do Python: Chamar os serviços cognitivos num pipeline de indexação - Azure Search'
description: Percorrer um exemplo de extração de dados, de linguagem natural e de imagem IA processamento na Azure Search utilizando um bloco de notas do Jupyter Python. Dados extraídos são indexados e facilmente acessados pela consulta.
manager: cgronlun
author: LisaLeib
services: search
ms.service: search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: 50a252ff93f7e2cc6e5c6100c6bce850e9e96baf
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295629"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Tutorial do Python: Chamar APIs serviços cognitivos num pipeline de indexação do Azure Search

Neste tutorial, vai aprender os mecanismos da programação do melhoramento de dados no Azure Search com as *competências cognitivas*. Habilidades são apoiadas por capacidades de análise de imagem nos serviços cognitivos e processamento de linguagem natural (NLP). Através do conjunto de capacidades composição e configuração, pode extrair representações de texto de uma imagem ou um ficheiro de documentos digitalizados e de texto. Também pode detetar idioma, entidades, expressões-chave e muito mais. O resultado é o conteúdo mais avançado no índice da Azure Search, criado com possível de IA num pipeline de indexação. 

Neste tutorial, vai utilizar Python para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplicar competências incorporadas: reconhecimento de entidades, deteção de idioma, manipulação de texto e extração de expressões-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

O resultado é um índice pesquisável de texto completo no Azure Search. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md). 

Este tutorial é executado no serviço gratuito, mas o número de transações gratuitos está limitado a 20 documentos por dia. Se pretender executar este tutorial mais de uma vez no mesmo dia, utilize um ficheiro mais pequeno, definir, para que caibam em mais execuções.

> [!NOTE]
> Como expandir âmbito ao aumento da frequência de processamento, adicionar mais documentos ou adicionar mais algoritmos de IA, precisará [anexar um recurso dos serviços cognitivos cobrar](cognitive-search-attach-cognitive-services.md). Os encargos acumulam ao chamar APIs serviços cognitivos e para extração de imagem como parte da fase de aberturas de documentos no Azure Search. Não existem custos para extração de texto de documentos.
>
> Execução de habilidades internas é cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/). Preços de extração de imagem está descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste tutorial. 

+ [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. Certifique-se a conta de armazenamento na mesma região que o Azure Search.

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), fornecendo o Python 3.x e blocos de notas do Jupyter.

+ [Dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consiste num conjunto de pequenos ficheiros de diferentes tipos. 

+ [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

Para interagir com o serviço Azure Search, terá a URL do serviço e uma chave de acesso. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

![Obter uma chave de acesso e de ponto final HTTP](media/search-fiddler/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Os dados de origem devem ter origem num tipo de origem de dados suportado de um [indexador do Azure Search](search-indexer-overview.md). Armazenamento de tabelas do Azure não é suportado para a pesquisa cognitiva. Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. [Inicie sessão no portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento do Azure, clique em **Blobs**e, em seguida, clique em **+ contentor**.

1. [Criar um contentor de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Pode definir o nível de acesso público a qualquer um dos respetivos valores válidos.

1. Depois do contentor é criado, abra-o e selecione **carregar** na barra de comandos para carregar os ficheiros de exemplo que transferiu no passo anterior.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois de os ficheiros de exemplo serem carregados, obtenha o nome do contentor e uma cadeia de ligação para o seu armazenamento de Blobs. Pode fazê-lo ao navegar até à sua conta de armazenamento no portal do Azure. Clique em **chaves de acesso**e, em seguida, copie a **cadeia de ligação** campo.

A cadeia de ligação tem de ter este formato: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Manter a cadeia de ligação à mão. Irá precisar num passo posterior.

Existem outras formas de especificar a cadeia de ligação, por exemplo, fornecer uma assinatura de acesso partilhada. Para saber mais sobre as credenciais da origem de dados, veja [Indexar o Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Criar um bloco de notas do Jupyter

> [!Note]
> Este artigo mostra-lhe como criar uma origem de dados, índice, indexador e um conjunto de capacidades usando uma série de scripts do Python. Para transferir o exemplo de bloco de notas completa, vá para o [repositório Azure-pesquisa-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Utilize o navegador Anaconda para iniciar o bloco de notas do Jupyter e criar um novo bloco de notas de Python 3.

## <a name="connect-to-azure-search"></a>Ligar ao Azure Search

No bloco de notas, execute este script para carregar as bibliotecas usadas para trabalhar com JSON e formular pedidos HTTP.

```python
import json
import requests
from pprint import pprint
```

Em seguida, defina os nomes para a origem de dados, índice, indexador e conjunto de capacidades. Execute este script para configurar os nomes para este tutorial.

```python
#Define the names for the data source, skillset, index and indexer
datasource_name="cogsrch-py-datasource"
skillset_name="cogsrch-py-skillset"
index_name="cogsrch-py-index"
indexer_name="cogsrch-py-indexer"
```

> [!Tip]
> Num serviço gratuito, está limitado a três índices, indexadores e origens de dados. Este tutorial cria um de cada. Certifique-se de que tem espaço para criar novos objetos antes de qualquer continuarmos.

O script seguinte, substitua os marcadores de posição para o seu serviço de pesquisa (seu-pesquisa-SERVICE-NAME) e API de administração da chave (chave-seu-ADMIN-API) e, em seguida, execute-o para configurar o ponto final de serviço de pesquisa.

```python
#Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
        'api-key': '<YOUR-ADMIN-API-KEY>' }
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Criar uma origem de dados

Agora que os seus serviços e ficheiros de origem estão preparados, comece a reunir os componentes do pipeline de indexação. Começar com um objeto de origem de dados que informa a Azure Search como recuperar dados de origem externa.

O script seguinte, substitua o marcador de posição seu-BLOBS-RESOURCE-CONNECTION-STRING a cadeia de ligação para o blob que criou no passo anterior. Em seguida, execute o script para criar uma origem de dados com o nome `cogsrch-py-datasource`.

```python
#Create a data source
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
r = requests.put( endpoint + "/datasources/" + datasource_name, data=json.dumps(datasource_payload), headers=headers, params=params )
print (r.status_code)
```

O pedido deverá devolver um código de estado de 201 confirmar o êxito.

No portal do Azure, na página de dashboard do serviço de pesquisa, certifique-se de que o cogsrch-py-datasource é apresentada no **origens de dados** lista. Clique em **atualizar** para atualizar a página.

![Mosaico Origens de dados no portal](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Mosaico Origens de dados no portal")

## <a name="create-a-skillset"></a>Criar um conjunto de competências

Neste passo, irá definir um conjunto de passos de melhoria para aplicar aos seus dados. Um passo de melhoramento é denominado *competência* e o conjunto de passos de melhoramento é denominado *conjunto de competências*. Este tutorial utiliza [incorporadas capacidades cognitivas](cognitive-search-predefined-skills.md) para o conjunto de capacidades:

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de Texto](cognitive-search-skill-textsplit.md) para dividir conteúdo grande em segmentos mais pequenos antes de chamar a competência de extração de expressões-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de entidades](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdo no contentor de Blobs.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves. 

### <a name="python-script"></a>Script de Python
Execute o seguinte script para criar um conjunto de capacidades chamado `cogsrch-py-skillset`.

```python
#Create a skillset
skillset_payload = {
  "name": skillset_name,
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
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
      "textSplitMode" : "pages",
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
          "name":"languageCode", "source": "/document/languageCode"
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

r = requests.put(endpoint + "/skillsets/" + skillset_name, data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

O pedido deverá devolver um código de estado de 201 confirmar o êxito.

A habilidade de extração de expressões-chave é aplicada para cada página. Ao definir o contexto `"document/pages/*"`, execute este enricher para cada membro da matriz documento/páginas (para cada página no documento).

Cada competência é executada no conteúdo do documento. Durante o processamento, o Azure Search abre cada documento para ler o conteúdo de diferentes formatos de ficheiros. Texto encontrado no arquivo de origem é colocado num `content` campo, uma para cada documento. Por conseguinte, definir a entrada como `"/document/content"`.

Veja a seguir uma representação gráfica do conjunto de competências.

![Compreender um conjunto de competências](media/cognitive-search-tutorial-blob/skillset.png "Compreender um conjunto de competências")

Saídas podem ser mapeadas para um índice, utilizado como entrada para uma habilidade downstream, ou ambos, tal como acontece com o código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Criar um índice

Nesta secção, pode definir o esquema de índice especificando os campos serão incluídos no índice pesquisável e definir os atributos de pesquisa para cada campo. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

Neste exercício, utiliza os seguintes campos e tipos de campo:

| nomes de campo: | id         | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| tipos de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Execute este script para criar o índice com o nome `cogsrch-py-index`.

```python
#Create an index
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

r = requests.put(endpoint + "/indexes/" + index_name, data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

O pedido deverá devolver um código de estado de 201 confirmar o êxito.

Para saber mais acerca da definição de um índice, veja [Create Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Criar Índice (API REST do Azure Search)).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Criar um indexador, mapear campos e executar transformações

Até agora, criou uma origem de dados, um conjunto de capacidades e um índice. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. Para vincular esses objetos em conjunto um indexador, tem de definir mapeamentos de campo.

+ Os fieldMappings são processadas antes das competências, mapeamento de campos de origem da origem de dados aos campos de destino num índice. Se os nomes de campos e tipos são os mesmos em ambas as extremidades, nenhum mapeamento é necessário.

+ Os outputFieldMappings processadas depois das competências, fazendo referência sourceFieldNames que não existem até aberturas de documentos ou enriquecimento cria-los. O targetFieldName é um campo num índice.

Além de conectar entradas para saídas, também pode utilizar os mapeamentos de campo aplaná estruturas de dados. Para obter mais informações, consulte [como mapear campos plena para um índice pesquisável](cognitive-search-output-field-mapping.md).

Execute este script para criar um indexador com o nome `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
    {
      "sourceFieldName" : "content",
      "targetFieldName" : "content"
    }
  ],
   "outputFieldMappings" :
  [
    {
      "sourceFieldName" : "/document/organizations",
      "targetFieldName" : "organizations"
    },
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
   "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}

r = requests.put(endpoint + "/indexers/" + indexer_name, data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

O pedido rapidamente deverá devolver um código de estado de 201, no entanto, o processamento pode demorar vários minutos a concluir. Embora o conjunto de dados for pequeno, habilidades analíticas, como análise de imagem, são computacionalmente intensivas e demorar algum tempo.

Utilize o [verificar o estado de indexador](#check-indexer-status) script na secção seguinte para determinar quando o processo de indexador está completo.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver um problema de acesso os dados, mapeamento de entradas e saídas, ou com a ordem de operações, ele será exibido nesse estágio. Voltar a executar o pipeline com as alterações de código ou script, terá de eliminar primeiro os objetos. Para obter mais informações, veja [Repor e executar novamente](#reset).

#### <a name="explore-the-request-body"></a>Explorar o corpo do pedido

O script define `"maxFailedItems"` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também a instrução `"dataToExtract":"contentAndMetadata"` nos parâmetros de configuração. Essa instrução informa o indexador para extrair o conteúdo de formatos de ficheiro diferentes e os metadados relacionados com a cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. O `"imageAction":"generateNormalizedImages"` configuração, combinada com a habilidade de OCR e a habilidade de unir de texto, informa o indexador para extrair texto de imagens (por exemplo, a palavra "stop" de um sinal de tráfego) e incorporá-lo como parte do campo conteúdo. Este comportamento aplica-se para as imagens embutidas nos documentos (Imagine uma imagem dentro de um PDF) e a imagens encontradas na origem de dados, por exemplo, um arquivo JPG.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Verificar o estado do indexador

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se o processamento de indexador está concluído, execute o seguinte script.

```python
#Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name + "/status", headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

Em resposta, monitor "lastResult" para os valores "status" e "endTime". Periodicamente, execute o script para verificar o estado. Quando o indexador for concluída, o estado será definido para "êxito", "endTime" será especificado e a resposta irá incluir quaisquer erros e avisos que ocorreram durante a melhoria.

![Indexador é criado](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "indexador é criado")

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Neste tutorial, os avisos são benignos. Por exemplo, um dos ficheiros JPEG que não tem texto irá mostrar o aviso nesta captura de ecrã.

![Aviso de indexador de exemplo](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "aviso do indexador de exemplo")

## <a name="query-your-index"></a>Consultar o índice

Depois de concluída a indexação, execute consultas que devolvam os conteúdos dos campos individuais. Por predefinição, o Azure Search devolve os 50 principais resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter as instruções, veja [Como paginar os resultados no Azure Search](search-pagination-page-layout.md).

Como passo de verificação, consulte o índice de todos os campos.

```python
#Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name, headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

Os resultados devem ter um aspeto semelhantes ao seguinte exemplo. A captura de ecrã mostra apenas uma parte da resposta.

![Índice de consulta para todos os campos](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "consultar o índice para todos os campos")

O resultado é o esquema de índice, com o nome, o tipo e os atributos de cada campo.

Submeta uma segunda consulta para `"*"` devolver todos os conteúdos de um único campo, tal como `organizations`.

```python
#Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name + "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Os resultados devem ter um aspeto semelhantes ao seguinte exemplo. A captura de ecrã mostra apenas uma parte da resposta.

![Índice de consulta para o conteúdo das organizações](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "consultar o índice para retornar o conteúdo das organizações")

Repita esta operação para campos adicionais: conteúdo, languageCode, keyPhrases e organizações neste exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

Pode utilizar GET ou POST, dependendo da complexidade da cadeia de consulta e do comprimento. Para obter mais informações, veja [Consultar através da API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).
it <a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento do pipeline, a abordagem mais prática para as iterações de design consiste em eliminar os objetos do Azure Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Para voltar a indexar os documentos com as novas definições:

1. Elimine o índice para remover os dados persistentes. Elimine o indexador para recriá-lo no seu serviço.
2. Modificar as definições de conjunto de capacidades e índice.
3. Volte a criar um índice e o indexador no serviço para executar o pipeline.

Pode utilizar o portal para eliminar índices, indexadores e conjuntos de competências. Ao eliminar o indexador, pode, opcionalmente, de forma seletiva eliminar a origem de dados, conjunto de capacidades e índice ao mesmo tempo.

![Eliminar objetos de pesquisa](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "pesquisa de eliminação de objetos no portal")

Também pode eliminá-los usando um script. O script seguinte irá eliminar o conjunto de capacidades que criámos. Pode modificar facilmente o pedido para eliminar o índice, indexador e origem de dados.

```python
#delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name, headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

[Predefinidas habilidades](cognitive-search-predefined-skills.md) foram introduzidas, juntamente com as definições do conjunto de capacidades e uma forma de habilidades de cadeia em conjunto através de entradas e saídas. Também aprendeu que precisa de `outputFieldMappings` na definição do indexador para encaminhar valores melhorados do pipeline para um índice pesquisável num serviço do Azure Search.

Por fim, aprendeu como testar os resultados e reiniciar o sistema para obter mais iterações. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Nesta versão, não há um mecanismo para visualizar as construções internas (documentos melhorados criados pelo sistema). Também aprendeu a verificar o estado de indexador e os objetos que têm de ser eliminadas antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar no final do tutorial passa por eliminar o grupo de recursos que contém o serviço do Azure Search e o serviço Blob do Azure. Partindo do princípio de que colocar ambos os serviços no mesmo grupo, elimine o grupo de recursos para eliminar permanentemente tudo no mesmo, incluindo os serviços e qualquer conteúdo armazenado que criou para este tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos Seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu.

> [!div class="nextstepaction"]
> [Exemplo: criar uma competência personalizada](cognitive-search-create-custom-skill-example.md)
