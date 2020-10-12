---
title: 'Tutorial: Python e IA sobre bolhas de Azure'
titleSuffix: Azure Cognitive Search
description: Passe por um exemplo de extração de texto e processamento de linguagem natural sobre o conteúdo no armazenamento blob usando um caderno Jupyter Python e as APIs de Repouso de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: 34265552122c1f8d1bcbbcfe95948683a5750a71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531008"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: Use Python e IA para gerar conteúdo pesmável a partir de bolhas Azure

Se tiver texto ou imagens não estruturados no armazenamento de Azure Blob, um [oleoduto de enriquecimento de IA](cognitive-search-concept-intro.md) pode extrair informações e criar novos conteúdos que são úteis para a pesquisa completa de texto ou cenários de mineração de conhecimento. Embora um pipeline possa processar imagens, este tutorial python foca-se no texto, aplicando a deteção de linguagem e processamento de linguagem natural para criar novos campos que você pode alavancar em consultas, facetas e filtros.

Este tutorial utiliza Python e as [APIs search REST](/rest/api/searchservice/) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Comece com documentos inteiros (texto não estruturado) como PDF, HTML, DOCX e PPTX no armazenamento Azure Blob.
> * Defina um pipeline que extraia texto, deteta linguagem, reconhece entidades e deteta frases-chave.
> * Defina um índice para armazenar a saída (conteúdo bruto, mais pares de valor-nome gerados pelo gasoduto).
> * Execute o pipeline para iniciar transformações e análises, e para criar e carregar o índice.
> * Explore resultados utilizando a pesquisa completa por texto e uma sintaxe de consulta rica.

Se não tiver uma subscrição do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Você pode usar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexantes e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de ter espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

1. Abra esta [pasta OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e no canto superior esquerdo, clique em **Baixar** para copiar os ficheiros para o seu computador. 

1. Clique com o botão direito no ficheiro zip e selecione **Extract All**. Existem 14 ficheiros de vários tipos. Vais usar 7 para este exercício.

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial usa a Azure Cognitive Search para indexar e consultas, Serviços Cognitivos no backend para enriquecimento de IA, e armazenamento Azure Blob para fornecer os dados. Este tutorial fica sob a alocação gratuita de 20 transações por indexante por dia em Serviços Cognitivos, pelo que os únicos serviços que precisa de criar são a pesquisa e armazenamento.

Se possível, crie tanto na mesma região como no grupo de recursos para proximidade e gestão. Na prática, a sua conta de Armazenamento Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Comece com o armazenamento Azure

1. [Inscreva-se no portal Azure](https://portal.azure.com/) e clique **+ Criar Recurso.**

1. Procure a *conta de armazenamento* e selecione a oferta de Conta de Armazenamento da Microsoft.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Criar conta de Armazenamento" border="false":::

1. No separador Básicos, são necessários os seguintes itens. Aceite os incumprimentos para tudo o resto.

   + **Grupo de recursos**. Selecione um existente ou crie um novo, mas use o mesmo grupo para todos os serviços para que possa geri-los coletivamente.

   + **Nome da conta de armazenamento**. Se acha que pode ter múltiplos recursos do mesmo tipo, use o nome para desambiguar por tipo e região, por *exemplo, blobstoragewestus*. 

   + **Localização**. Se possível, escolha o mesmo local utilizado para a Azure Cognitive Search and Cognitive Services. Um único local anula as cargas de largura de banda.

   + **Tipo de Conta**. Escolha o predefinido, *StorageV2 (finalidade geral v2)*.

1. Clique **em 'Rever + Criar'** para criar o serviço.

1. Uma vez criado, clique em **Ir ao recurso** para abrir a página 'Vista Geral'.

1. Clique no serviço **Blobs.**

1. Clique **em + Recipiente** para criar um recipiente e nomeie-o *cog-search-demo*.

1. Selecione *a demonstração de pesquisa de cog e,* em seguida, clique em **Upload** para abrir a pasta onde guardou os ficheiros de descarregamento. Selecione todos os ficheiros de não imagem. Devia ter 7 ficheiros. Clique **em OK** para carregar.

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="Criar conta de Armazenamento" border="false":::

1. Antes de sair do Azure Storage, obtenha uma cadeia de ligação para que possa formular uma ligação na Pesquisa Cognitiva Azure. 

   1. Procure de volta à página geral da sua conta de armazenamento *(usamos blobstragewestus* como exemplo). 
   
   1. No painel de navegação esquerdo, selecione **as teclas de acesso** e copie uma das cordas de ligação. 

   A cadeia de ligação é um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Guarde o fio de ligação para o Bloco de Notas. Vai precisar mais tarde ao configurar a ligação à fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado por Serviços Cognitivos, incluindo Análise de Texto e Visão Computacional para o processamento de linguagem natural e imagem. Se o seu objetivo fosse completar um protótipo ou projeto real, você iria neste momento providenciar Serviços Cognitivos (na mesma região que a Azure Cognitive Search) para que você possa anexá-lo a operações de indexação.

Uma vez que este tutorial utiliza apenas 7 transações, pode ignorar o fornecimento de recursos porque a Azure Cognitive Search pode ligar-se aos Serviços Cognitivos para 20 transações gratuitas por indexante executado. A dotação gratuita é suficiente. Para projetos maiores, planeie o fornecimento de Serviços Cognitivos no nível S0 pay-as-you-go. Para obter mais informações, consulte [Os Serviços Cognitivos anexados.](cognitive-search-attach-cognitive-services.md)

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O terceiro componente é Azure Cognitive Search, que pode [criar no portal.](search-create-service-portal.md) Você pode usar o nível Livre para completar esta caminhada através. 

Tal como acontece com o armazenamento da Azure Blob, aproveite um momento para recolher a chave de acesso. Mais à frente, quando iniciar os pedidos estruturantes, terá de fornecer o ponto final e a tecla api de administração utilizada para autenticar cada pedido.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma api-chave de administrador e URL para pesquisa cognitiva Azure

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **de Visão Geral** do seu serviço de pesquisa, obtenha o nome do seu serviço de pesquisa. Pode confirmar o seu nome de serviço revendo o URL do ponto final. Se o seu URL de ponto final `https://mydemo.search.windows.net` fosse, o seu nome de serviço seria `mydemo` .

2. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso apenas de leitura.

   :::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="Criar conta de Armazenamento" border="false":::

Todos os pedidos requerem uma chave API no cabeçalho de cada pedido enviado ao seu serviço. Uma chave válida estabelece confiança, por pedido, entre o pedido de envio do pedido e o serviço que o trata.

## <a name="2---start-a-notebook"></a>2 - Iniciar um caderno

Crie o caderno utilizando as seguintes instruções, ou descarregue um caderno acabado a partir de [Azure-Search-python-samples repo](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Use o Navegador Anaconda para lançar o Jupyter Notebook e criar um novo caderno Python 3.

No seu caderno, execute este script para carregar as bibliotecas utilizadas para trabalhar com json e formular pedidos HTTP.

```python
import json
import requests
from pprint import pprint
```

No mesmo caderno, defina os nomes para a fonte de dados, índice, indexante e skillset. Execute este guião para configurar os nomes para este tutorial.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

No seguinte script, substitua os espaços reservados para o seu serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) e a tecla API de administração (YOUR-ADMIN-API-KEY), e, em seguida, execute-o para configurar o ponto final do serviço de pesquisa.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3 - Criar o gasoduto

Na Pesquisa Cognitiva Azure, o processamento de IA ocorre durante a indexação (ou ingestão de dados). Esta parte da caminhada cria quatro objetos: fonte de dados, definição de índice, skillset, indexante. 

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

Um [objeto de origem de dados](/rest/api/searchservice/create-data-source) fornece o fio de ligação ao recipiente Blob que contém os ficheiros.

No seguinte script, substitua o espaço reservado YOUR-BLOB-RESOURCE-CONNECTION-STRING pelo fio de ligação para a bolha que criou no passo anterior. Substitua o texto do espaço reservado para o recipiente. Em seguida, executar o script para criar uma fonte de dados chamada `cogsrch-py-datasource` .

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

No portal Azure, na página do dashboard do serviço de pesquisa, verifique se a fonte de dados cogsrch-py aparece na lista de fontes de **dados.** Clique em **Atualizar** para atualizar a página.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="Criar conta de Armazenamento" border="false":::

### <a name="step-2-create-a-skillset"></a>Passo 2: Criar um skillset

Neste passo, definirá um conjunto de passos de enriquecimento para aplicar aos seus dados. Um passo de melhoramento é denominado *competência* e o conjunto de passos de melhoramento é denominado *conjunto de competências*. Este tutorial usa [habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) para o skillset:

+ [Reconhecimento de Entidades](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações do conteúdo do recipiente blob.

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de Texto](cognitive-search-skill-textsplit.md) para dividir conteúdo grande em segmentos mais pequenos antes de chamar a competência de extração de expressões-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves. 

Execute o seguinte script para criar um skillset chamado `cogsrch-py-skillset` .

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
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
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
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
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

A habilidade de extração de frase-chave é aplicada para cada página. Ao definir o contexto para `"document/pages/*"` , executar este enriquecimento para cada membro da matriz de documento/páginas (para cada página no documento).

Cada competência é executada no conteúdo do documento. Durante o processamento, a Azure Cognitive Search quebra cada documento para ler conteúdo de diferentes formatos de ficheiros. O texto encontrado no ficheiro de origem é colocado num `content` campo, um para cada documento. Portanto, desa estale a entrada como `"/document/content"` .

Veja a seguir uma representação gráfica do conjunto de competências.

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="Criar conta de Armazenamento" border="false":::

As saídas podem ser mapeadas para um índice, usado como entrada para uma habilidade a jusante, ou ambos, como é o caso do código linguístico. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Passo 3: Criar um índice

Nesta secção, define o esquema de índice especificando os campos a incluir no índice pes pes pescável e definindo os atributos de pesquisa para cada campo. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

Neste exercício, utiliza os seguintes campos e tipos de campo:

| nomes de campo: | ID         | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| tipos de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Executar este script para criar o índice chamado `cogsrch-py-index` .

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

Para saber mais sobre a definição de um índice, consulte [Create Index (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Passo 4: Criar e executar um indexador

Um [Indexer](/rest/api/searchservice/create-indexer) conduz o oleoduto. Os três componentes que criou até agora (fonte de dados, skillset, índice) são entradas para um indexante. Criar o indexante na Azure Cognitive Search é o evento que coloca todo o oleoduto em movimento. 

Para ligar estes objetos num indexante, é necessário definir mapeamentos de campo.

+ Os `"fieldMappings"` são processados antes do skillset, mapeando campos de origem da fonte de dados para campos alvo em um índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, não é necessário mapear.

+ Os `"outputFieldMappings"` são processados após o skillset, referindo `"sourceFieldNames"` que não existem até que o documento de cracking ou enriquecimento os crie. `"targetFieldName"`É um campo num índice.

Além de ligar entradas às saídas, também pode usar mapeamentos de campo para aplainar estruturas de dados. Para mais informações, consulte [como mapear campos enriquecidos para um índice pescável.](cognitive-search-output-field-mapping.md)

Executar este script para criar um indexante chamado `cogsrch-py-indexer` .

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

Pode monitorizar o [estado do indexante](#check-indexer-status) para determinar quando o indexante está em funcionamento ou terminado.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver um problema de acesso aos dados, mapeamento de entradas e saídas, ou com a ordem de operações, aparecerá nesta fase. Para re-executar o pipeline com alterações de código ou script, pode ter de eliminar primeiro os objetos. Para obter mais informações, veja [Repor e executar novamente](#reset).

#### <a name="about-the-request-body"></a>Sobre o corpo de pedido

O script define `"maxFailedItems"` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também a instrução `"dataToExtract":"contentAndMetadata"` nos parâmetros de configuração. Esta declaração diz ao indexante para extrair o conteúdo de diferentes formatos de ficheiros e dos metadados relacionados com cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. A `"imageAction":"generateNormalizedImages"` configuração, combinada com a Habilidade de Fusão de OCR e A Capacidade de Fusão de Texto, diz ao indexante para extrair texto das imagens (por exemplo, a palavra "parar" de um sinal de stop de tráfego), e incorporá-lo como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (pense numa imagem dentro de um PDF) como às imagens encontradas na fonte de dados, por exemplo, a um ficheiro JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorização da indexação

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se o processamento do indexante está completo, execute o seguinte script.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Na resposta, monitorize os `"lastResult"` seus `"status"` `"endTime"` valores e valores. Execute periodicamente o script para verificar o estado. Quando o indexante tiver terminado, o estado será definido como "sucesso", será especificado um "endTime" e a resposta incluirá quaisquer erros e avisos ocorridos durante o enriquecimento.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="Criar conta de Armazenamento" border="false":::

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Muitos avisos são benignos. Por exemplo, se indexar um ficheiro JPEG que não tenha texto, verá o aviso nesta imagem.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="Criar conta de Armazenamento" border="false":::

## <a name="5---search"></a>5 - Pesquisar

Depois de concluída a indexação, execute consultas que devolvam os conteúdos dos campos individuais. Por padrão, a Azure Cognitive Search devolve os 50 melhores resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter instruções, consulte [como página os resultados na Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

Como passo de verificação, obtenha a definição de índice mostrando todos os campos.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Os resultados devem ser semelhantes ao exemplo seguinte. A imagem mostra apenas uma parte da resposta.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="Criar conta de Armazenamento" border="false":::

O resultado é o esquema de índice, com o nome, o tipo e os atributos de cada campo.

Submeta uma segunda consulta para `"*"` devolver todos os conteúdos de um único campo, tal como `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Os resultados devem ser semelhantes ao exemplo seguinte. A imagem mostra apenas uma parte da resposta.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="Criar conta de Armazenamento" border="false":::

Repita para campos adicionais: `content` , , e neste `languageCode` `keyPhrases` `organizations` exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

Pode utilizar GET ou POST, dependendo da complexidade da cadeia de consulta e do comprimento. Para obter mais informações, veja [Consultar através da API REST](/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração de design é eliminar os objetos da Azure Cognitive Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Pode utilizar o portal para eliminar índices, indexadores, fontes de dados e skillsets. Quando elimina o indexador, pode, opcionalmente, eliminar seletivamente o índice, o skillset e a fonte de dados ao mesmo tempo.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="Criar conta de Armazenamento" border="false":::

Também pode eliminá-los usando um script. O seguinte script mostra como apagar um skillset. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

O código de estado 204 é devolvido após uma eliminação com êxito.

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

[Foram introduzidas competências incorporadas,](cognitive-search-predefined-skills.md) juntamente com definições de skillset e uma forma de acorrentar competências em conjunto através de entradas e saídas. Também aprendeu que `outputFieldMappings` na definição do indexante é necessário encaminhar valores enriquecidos do pipeline para um índice pesmável num serviço de Pesquisa Cognitiva Azure.

Finalmente, aprendeu a testar os resultados e a redefinir o sistema para mais iterações. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Nesta versão, não há um mecanismo para visualizar as construções internas (documentos melhorados criados pelo sistema). Também aprendeu a verificar o estado do indexante e quais os objetos que devem ser eliminados antes de voltar a ser reensado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando se está a trabalhar na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação de todos os recursos ou grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com todos os objetos num oleoduto de enriquecimento de IA, vamos olhar mais de perto as definições de skillset e habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar um skillset](cognitive-search-defining-skillset.md)