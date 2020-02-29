---
title: 'Tutorial: Python e IA sobre bolhas Azure'
titleSuffix: Azure Cognitive Search
description: Percorra um exemplo de extração de texto e processamento de linguagem natural sobre conteúdos no armazenamento blob usando um caderno Jupyter Python e as APIs de PESQUISA Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: e7708b0043b7f5baf2c12e813306595cc358a01d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194059"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: Use Python e IA para gerar conteúdo pesquisável a partir de blobs Azure

Se tiver texto ou imagens não estruturadas no armazenamento do Azure Blob, um pipeline de enriquecimento de [IA](cognitive-search-concept-intro.md) pode extrair informações e criar novos conteúdos que são úteis para cenários de pesquisa de texto completo ou de mineração de conhecimento. Embora um pipeline possa processar imagens, este tutorial python foca-se no texto, aplicando a deteção de linguagem e processamento de linguagem natural para criar novos campos que você pode alavancar em consultas, facetas e filtros.

Este tutorial utiliza a Python e as [APIs](https://docs.microsoft.com/rest/api/searchservice/) de Repouso de Pesquisa para executar as seguintes tarefas:

> [!div class="checklist"]
> * Comece com documentos inteiros (texto não estruturado) tais como PDF, HTML, DOCX e PPTX no armazenamento azure Blob.
> * Defina um oleoduto que extrai texto, detete linguagem, reconheça entidades e detete frases-chave.
> * Defina um índice para armazenar a saída (conteúdo bruto, mais pares de valor de nome gerados pelo gasoduto).
> * Execute o gasoduto para iniciar transformações e análises, e para criar e carregar o índice.
> * Explore os resultados usando a pesquisa completa de texto e uma sintaxe de consulta rica.

Se não tiver uma subscrição Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de pesquisa existente 

> [!Note]
> Pode utilizar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de que tem espaço ao seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

1. Abra esta [pasta OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e no canto superior esquerdo, clique em **Baixar** para copiar os ficheiros para o seu computador. 

1. Clique no ficheiro zip e **selecione Extrato Tudo**. Existem 14 ficheiros de vários tipos. Vaiusar 7 para este exercício.

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial utiliza a Pesquisa Cognitiva Azure para indexação e consultas, Serviços Cognitivos no backend para enriquecimento de IA, e armazenamento Azure Blob para fornecer os dados. Este tutorial permanece sob a atribuição gratuita de 20 transações por indexante por dia em Serviços Cognitivos, pelo que os únicos serviços que precisa para criar são a pesquisa e armazenamento.

Se possível, crie tanto na mesma região como grupo de recursos para proximidade e gestão. Na prática, a sua conta de Armazenamento Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Comece com o Armazenamento Azure

1. [Inscreva-se no portal Azure](https://portal.azure.com/) e clique **em + Criar Recurso**.

1. Procure a conta de *armazenamento* e selecione a oferta da Conta de Armazenamento da Microsoft.

   ![Criar conta de Armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar conta de Armazenamento")

1. No separador Basics, são necessários os seguintes itens. Aceite os incumprimentos para todo o resto.

   + **Grupo de recursos**. Selecione um existente ou crie um novo, mas use o mesmo grupo para todos os serviços para que possa geri-los coletivamente.

   + **Nome da conta de armazenamento.** Se pensa que pode ter vários recursos do mesmo tipo, use o nome para desambiguar por tipo e região, por exemplo *blobstoragewestus*. 

   + **Localização**. Se possível, escolha o mesmo local utilizado para a Pesquisa Cognitiva Azure e Serviços Cognitivos. Um único local anula as cargas de largura de banda.

   + **Tipo de conta.** Escolha o predefinido, *StorageV2 (finalidade geral v2)* .

1. Clique em **Rever + Criar** para criar o serviço.

1. Uma vez criado, clique em **Ir ao recurso** para abrir a página 'Overview'.

1. Clique no serviço **Blobs.**

1. Clique em **+ Recipiente** para criar um recipiente e *nomeá-lo cog-search-demo*.

1. Selecione *cog-search-demo* e, em seguida, clique em **Carregar** para abrir a pasta onde guardou os ficheiros de descarregamento. Selecione todos os ficheiros não-imagem. Devia ter 7 ficheiros. Clique **em OK** para carregar.

   ![Upload de ficheiros de amostras](media/cognitive-search-tutorial-blob/sample-files.png "Upload de ficheiros de amostras")

1. Antes de sair do Azure Storage, obtenha uma cadeia de ligação para que possa formular uma ligação em Azure Cognitive Search. 

   1. Volte para a página de visão geral da sua conta de armazenamento (usamos *blobstragewestus* como exemplo). 
   
   1. No painel de navegação esquerdo, selecione **teclas de acesso** e copie uma das cordas de ligação. 

   A cadeia de ligação é um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Guarde a corda de ligação ao Bloco de Notas. Vai precisar mais tarde quando configurar a ligação de fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado por Serviços Cognitivos, incluindo Text Analytics e Computer Vision para linguagem natural e processamento de imagem. Se o seu objetivo fosse completar um protótipo ou projeto real, nesta altura forneceria serviços cognitivos (na mesma região que a Pesquisa Cognitiva Azure) para que o pudesse anexar a operações de indexação.

Para este exercício, no entanto, você pode saltar o fornecimento de recursos porque a Pesquisa Cognitiva Azure pode ligar-se aos Serviços Cognitivos nos bastidores e dar-lhe 20 transações gratuitas por corrida de indexador. Uma vez que este tutorial utiliza 7 transações, a atribuição gratuita é suficiente. Para projetos maiores, planeie o fornecimento de Serviços Cognitivos no nível S0 pay-as-you-go. Para mais informações, consulte [Anexar Serviços Cognitivos](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O terceiro componente é a Pesquisa Cognitiva Azure, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o free tier para completar esta passagem. 

Tal como acontece com o armazenamento da Azure Blob, tire um momento para recolher a chave de acesso. Mais à frente, quando começar a estruturar pedidos, terá de fornecer o ponto final e a chave api-key usado para autenticar cada pedido.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma chave de api-key e URL para pesquisa cognitiva azure

1. [Faça sessão no portal Azure](https://portal.azure.com/), e na página **'Visão Geral** do serviço de pesquisa', obtenha o nome do seu serviço de pesquisa. Pode confirmar o seu nome de serviço revendo o URL do ponto final. Se o seu URL final fosse `https://mydemo.search.windows.net`, o seu nome de serviço seria `mydemo`.

2. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso só para leitura.

   ![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-nodejs/service-name-and-keys.png)

Todos os pedidos requerem uma chave api no cabeçalho de cada pedido enviado ao seu serviço. Uma chave válida estabelece confiança, por pedido, entre o pedido que envia o pedido e o serviço que o trata.

## <a name="2---start-a-notebook"></a>2 - Iniciar um caderno

Crie o caderno utilizando as seguintes instruções ou descarregue um caderno acabado a partir de Amostras de [Pitão Azure-Search-python repo](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Use a Anaconda Navigator para lançar o Jupyter Notebook e criar um novo caderno Python 3.

No seu caderno, execute este script para carregar as bibliotecas utilizadas para trabalhar com a JSON e formular pedidos HTTP.

```python
import json
import requests
from pprint import pprint
```

No mesmo caderno, defina os nomes para a fonte de dados, índice, indexante e skillset. Executa este guião para definir os nomes para este tutorial.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

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

## <a name="3---create-the-pipeline"></a>3 - Criar o gasoduto

Na Pesquisa Cognitiva Azure, o processamento de IA ocorre durante a indexação (ou ingestão de dados). Esta parte do walkthrough cria quatro objetos: fonte de dados, definição de índice, skillset, indexante. 

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

Um [objeto de origem](https://docs.microsoft.com/rest/api/searchservice/create-data-source) de dados fornece a cadeia de ligação ao recipiente Blob que contém os ficheiros.

No seguinte script, substitua o espaço reservado YOUR-BLOB-RESOURCE-CONNECTION-STRING com a cadeia de ligação para a bolha que criou no passo anterior. Substitua o texto do espaço reservado para o recipiente. Em seguida, executar o script para criar uma fonte de dados chamada `cogsrch-py-datasource`.

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
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

O pedido deve devolver um código de estado de 201 confirmando o sucesso.

No portal Azure, na página do painel de instrumentos de pesquisa, verifique se a fonte de dados cogsrch-py aparece na lista de fontes de **dados.** Clique em **Refresh** para atualizar a página.

![Fontes de dados azulejos no portal](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Fontes de dados azulejos no portal")

### <a name="step-2-create-a-skillset"></a>Passo 2: Criar uma habilidade

Neste passo, definirá um conjunto de passos de enriquecimento para aplicar aos seus dados. Um passo de melhoramento é denominado *competência* e o conjunto de passos de melhoramento é denominado *conjunto de competências*. Este tutorial usa [habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) para o skillset:

+ [Reconhecimento de entidades](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdos no recipiente blob.

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de Texto](cognitive-search-skill-textsplit.md) para dividir conteúdo grande em segmentos mais pequenos antes de chamar a competência de extração de expressões-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves. 

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

### <a name="step-3-create-an-index"></a>Passo 3: Criar um índice

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

### <a name="step-4-create-and-run-an-indexer"></a>Passo 4: Criar e executar um indexante

Um [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) conduz o oleoduto. Os três componentes que criou até agora (fonte de dados, skillset, índice) são inputs para um indexante. Criar o indexador em Azure Cognitive Search é o evento que põe todo o oleoduto em movimento. 

Para ligar estes objetos num indexante, deve definir mapeamentos de campo.

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

O pedido deverá devolver um código de estado de 201 em breve, no entanto, o processamento pode demorar vários minutos a ser concluído. Embora o conjunto de dados seja pequeno, as habilidades analíticas, como a análise de imagem, são computacionalmente intensivas e levam tempo.

Pode monitorizar o [estado do indexador](#check-indexer-status) para determinar quando o indexante está a funcionar ou a terminar.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver algum problema de acesso aos dados, de mapear inputs e saídas, ou com a ordem de operações, aparecerá nesta fase. Para reexecutar o pipeline com alterações de código ou script, poderá ser necessário eliminar objetos primeiro. Para obter mais informações, veja [Repor e executar novamente](#reset).

#### <a name="about-the-request-body"></a>Sobre o corpo de pedido

O script define `"maxFailedItems"` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também a instrução `"dataToExtract":"contentAndMetadata"` nos parâmetros de configuração. Esta declaração diz ao indexante para extrair o conteúdo de diferentes formatos de ficheiros e dos metadados relacionados com cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. A configuração `"imageAction":"generateNormalizedImages"`, combinada com a Habilidade de Habilidade oCR e a Habilidade de Fusão de Texto, diz ao indexante para extrair texto das imagens (por exemplo, a palavra "parar" de um sinal de paragem de tráfego), e incorpora-o como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (pense numa imagem dentro de um PDF) como às imagens encontradas na fonte de dados, por exemplo, a um ficheiro JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorização da indexação

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se o processamento do indexador está completo, execute o seguinte script.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Na resposta, monitorize o "último Resultado" pelos seus valores de "status" e "endTime". Verifique periodicamente o script para verificar o estado. Quando o indexante estiver concluído, o estado será definido para "sucesso", será especificado um "tempo final", e a resposta incluirá quaisquer erros e avisos que ocorreram durante o enriquecimento.

![Indexer é criado](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexer é criado")

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Muitos avisos são benignos. Por exemplo, se indexar um ficheiro JPEG que não tem texto, verá o aviso nesta imagem.

![Alerta indicador de exemplo](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Alerta indicador de exemplo")

## <a name="5---search"></a>5 - Pesquisa

Depois de concluída a indexação, execute consultas que devolvam os conteúdos dos campos individuais. Por padrão, a Pesquisa Cognitiva Azure devolve os 50 melhores resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter instruções, consulte [como página resultados em Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

Como passo de verificação, obtenha a definição de índice mostrando todos os campos.

```python
# Query the service for the index definition
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

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração do design é apagar os objetos da Pesquisa Cognitiva Azure e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Pode utilizar o portal para eliminar índices, indexadores, fontes de dados e competências. Ao eliminar o indexante, pode eliminar opcionalmente, seletivamente, o índice, a skillset e a fonte de dados ao mesmo tempo.

![Eliminar objetos de pesquisa](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Eliminar objetos de pesquisa no portal")

Também pode eliminá-los usando um script. O seguinte guião mostra como eliminar uma habilidade. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

O código de estado 204 é devolvido após uma eliminação com êxito.

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

Foram introduzidas [competências incorporadas,](cognitive-search-predefined-skills.md) juntamente com definições de skillset e uma forma de cadeiar competências através de inputs e saídas. Também descobriu que `outputFieldMappings` na definição de indexante é necessário para encaminhar valores enriquecidos do oleoduto para um índice pesquisável num serviço de Pesquisa Cognitiva Azure.

Finalmente, aprendeu a testar os resultados e a redefinir o sistema para mais iterações. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Nesta versão, não há um mecanismo para visualizar as construções internas (documentos melhorados criados pelo sistema). Também aprendeu a verificar o estado do indexador e que objetos devem ser eliminados antes de reexecutar um gasoduto.

## <a name="clean-up-resources"></a>Limpar recursos

Quando se trabalha na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação De Todos os recursos ou grupos de Recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com todos os objetos num oleoduto de enriquecimento de IA, vamos ver mais de perto as definições de habilidades e habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar uma habilidade](cognitive-search-defining-skillset.md)
