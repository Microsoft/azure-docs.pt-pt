---
title: Tutorial para chamar a pesquisa cognitiva APIs - Azure Search
description: Neste tutorial, acompanhe um exemplo de extração de dados, linguagem natural e processamento de imagem por IA na indexação do Azure Search para extração e transformação de dados.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 410e5d618e1deb1887329bea41e2cd3c6d795e58
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58075855"
---
# <a name="tutorial-learn-how-to-call-cognitive-search-apis-preview"></a>Tutorial: Saiba como chamar cognitivos procurar APIs (pré-visualização)

Neste tutorial, vai aprender os mecanismos da programação do melhoramento de dados no Azure Search com as *competências cognitivas*. As competências cognitivas são o processamento de linguagem natural (NLP) e as operações de análise de imagem que extraem texto e representações de texto a partir de uma imagem, detetam o idioma, as entidades, as expressões-chave e muito mais. O resultado final é conteúdo avançado adicional num índice do Azure Search, criado por um pipeline de indexação da pesquisa cognitiva. 

Neste tutorial, vai fazer chamadas à API REST para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplicar competências incorporadas: reconhecimento de entidades, deteção de idioma, manipulação de texto e extração de expressões-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

O resultado é um índice pesquisável de texto completo no Azure Search. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> A partir de 21 de Dezembro de 2018, poderá associar um recurso dos serviços cognitivos com um conjunto de capacidades do Azure Search. Isso permitirá que comecem a cobrança de consumo para a execução do conjunto de capacidades. Esta data, podemos também começar a cobrar extração de imagem como parte da fase de aberturas de documentos. Continuaremos a oferecer a extração de texto de documentos sem custos adicionais.
>
> A execução das habilidades internas será cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/) . Preços de extração de imagem são cobrados ao preço de pré-visualização e está descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Saiba [mais](cognitive-search-attach-cognitive-services.md).

## <a name="prerequisites"></a>Pré-requisitos

Ainda não conhece a pesquisa cognitiva? Leia [“O que é a pesquisa cognitiva?”](cognitive-search-concept-intro.md) para se familiarizar ou experimente o [início rápido do portal](cognitive-search-quickstart-blob.md) para uma introdução prática dos conceitos importantes.

Para realizar chamadas REST para o Azure Search, utilize o PowerShell ou uma ferramenta de teste Web, como Telerik Fiddler ou Postman, para formular pedidos HTTP. Se ainda não conhece estas ferramentas, veja [Explorar as APIs REST do Azure Search com o Fiddler ou o Postman](search-fiddler.md).

Utilize o [portal do Azure](https://portal.azure.com/) para criar serviços utilizados num fluxo de trabalho ponto a ponto. 

### <a name="set-up-azure-search"></a>Configurar o Azure Search

Primeiro, inscreva-se no serviço do Azure Search. 

1. Entre no [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Clique em **Criar um recurso**, procure o Azure Search e clique em **Criar**. Veja [Criar um serviço do Azure Search no portal](search-create-service-portal.md) se estiver a configurar um serviço de pesquisa pela primeira vez.

   ![Portal do dashboard](./media/cognitive-search-tutorial-blob/create-search-service-full-portal.png "Criar o serviço do Azure Search no portal")

1. Para Grupo de recursos, crie um grupo de recursos para conter todos os recursos que criar neste tutorial. Tal torna mais fácil limpar os recursos depois de concluir o tutorial.

1. Para a localização, recomendamos que escolha uma região que está próximo de seus dados e outras aplicações na cloud.

1. Para Escalão de preço, pode criar um serviço **Gratuito** para concluir os tutoriais e os inícios rápidos. Para uma investigação mais aprofundada através dos seus próprios dados, crie um [serviço pago](https://azure.microsoft.com/pricing/details/search/), como **Básico** ou **Standard**. 

   O serviço Gratuito está limitado a 3 índices, um tamanho de blob máximo de 16 MB e 2 minutos de indexação, o que é insuficiente para executar todas as funcionalidades da pesquisa cognitiva. Para rever os limites dos diferentes escalões, veja [Limites do Serviço](search-limits-quotas-capacity.md).

   ![Página de definição de serviço no portal](./media/cognitive-search-tutorial-blob/create-search-service1.png "página de definição de serviço no portal")
   ![página de definição de serviço no portal do](./media/cognitive-search-tutorial-blob/create-search-service2.png "página de definição de serviço no o portal")

 
1. Afixe o serviço no dashboard para ter acesso rápido às informações do serviço.

   ![Página de definições do serviço no portal](./media/cognitive-search-tutorial-blob/create-search-service3.png "Página de definições do serviço no portal")

1. Depois do serviço é criado, recolha as seguintes informações: **URL** da página de descrição geral, e **a chave de api** (primário ou secundário) na página chaves.

   ![Informações do ponto final e da chave no portal](./media/cognitive-search-tutorial-blob/create-search-collect-info.png "Informações do ponto final e da chave no portal")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Configurar o serviço Blob do Azure e carregar dados de exemplo

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Os dados de origem devem ter origem num tipo de origem de dados suportado de um [indexador do Azure Search](search-indexer-overview.md). Tenha em atenção que o armazenamento de tabelas do Azure não é suportado para a pesquisa cognitiva. Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. [Transferir os dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4). Os dados de exemplo são constituídos por um pequeno conjunto de ficheiros de diferentes tipos. 

1. Inscreva-se no armazenamento de Blobs do Azure, inicie sessão no Explorador de Armazenamento do Azure e crie um contentor denominado `basicdemo`. Veja [Início Rápido do Explorador de Armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) para obter instruções sobre todos os passos.

1. Com o Explorador de Armazenamento do Azure, no contentor `basicdemo` que criou, clique em **Carregar** para carregar os ficheiros de exemplo.

1. Depois de os ficheiros de exemplo serem carregados, obtenha o nome do contentor e uma cadeia de ligação para o seu armazenamento de Blobs. Pode fazê-lo ao navegar até à sua conta de armazenamento no portal do Azure. Em **Chaves de acesso**, copie o campo **Cadeia de Ligação**.

   A cadeia de ligação deve ter um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existem outras formas de especificar a cadeia de ligação, por exemplo, fornecer uma assinatura de acesso partilhada. Para saber mais sobre as credenciais da origem de dados, veja [Indexar o Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-data-source"></a>Criar uma origem de dados

Agora que os seus serviços e ficheiros de origem estão preparados, comece a reunir os componentes do pipeline de indexação. Comece com um [objeto de origem de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) que indica ao Azure Search como pode obter dados de origem externa.

Para este tutorial, utilize a API REST e uma ferramenta que possa formular e enviar pedidos HTTP, como PowerShell, Postman ou Fiddler. No cabeçalho do pedido, forneça o nome do serviço utilizado ao criar o serviço do Azure Search e a chave de API gerada para o serviço de pesquisa. No corpo do pedido, especifique o nome do contentor de blobs e a cadeia de ligação.

### <a name="sample-request"></a>Pedido de Amostra
```http
POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>Sintaxe do Corpo do Pedido
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
Envie o pedido. A ferramenta de teste Web deve devolver um código de estado de 201 a confirmar o êxito. 

Uma vez que este é o seu primeiro pedido, verifique o portal do Azure para confirmar que a origem de dados foi criada no Azure Search. Na página do dashboard do serviço de pesquisa, verifique se o mosaico Origens de Dados tem um novo item. Poderá ter de aguardar alguns minutos para a página do portal atualizar. 

  ![Mosaico Origens de dados no portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Mosaico Origens de dados no portal")

Se obtiver um erro 403 ou 404, verifique a construção do pedido: `api-version=2017-11-11-Preview` deve estar no ponto final, `api-key` deve estar no Cabeçalho após `Content-Type` e o valor deve ser válido para um serviço de pesquisa. Pode reutilizar o cabeçalho para os restantes passos neste tutorial.

> [!TIP]
> Agora, antes de fazer muito trabalho, é um bom momento para verificar se o serviço de pesquisa está em execução em uma das localizações suportadas fornecendo a funcionalidade de pré-visualização: Centro-Sul dos E.U.A. ou Europa Ocidental.

## <a name="create-a-skillset"></a>Criar um conjunto de competências

Neste passo, vai definir um conjunto de passos de melhoramento para aplicar aos seus dados. Um passo de melhoramento é denominado *competência* e o conjunto de passos de melhoramento é denominado *conjunto de competências*. Este tutorial utiliza [competências cognitivas predefinidas](cognitive-search-predefined-skills.md) para o conjunto de competências:

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de Texto](cognitive-search-skill-textsplit.md) para dividir conteúdo grande em segmentos mais pequenos antes de chamar a competência de extração de expressões-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de Entidades Nomeadas](cognitive-search-skill-named-entity-recognition.md) para extrair os nomes das organizações do conteúdo no contentor de blobs.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves. 

### <a name="sample-request"></a>Pedido de Amostra
Antes de realizar esta chamada REST, lembre-se de substituir o nome do serviço e a chave de administrador no pedido abaixo, caso a ferramenta não preserve o cabeçalho do pedido entre chamadas. 

Este pedido cria um conjunto de competências. Referencie o nome do conjunto de competências ```demoskillset``` para o resto deste tutorial.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Sintaxe do Corpo do Pedido
```json
{
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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
```

Envie o pedido. A ferramenta de teste Web deve devolver um código de estado de 201 a confirmar o êxito. 

#### <a name="about-the-request"></a>Acerca do pedido

Observe como a competência de extração de expressões-chave é aplicada para cada página. Ao definir o contexto como ```"document/pages/*"```, executa este melhoramento para cada membro da matriz de documento/páginas (para cada página no documento).

Cada competência é executada no conteúdo do documento. Durante o processamento, o Azure Search abre cada documento para ler o conteúdo de diferentes formatos de ficheiros. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, defina a entrada como ```"/document/content"```.

Veja a seguir uma representação gráfica do conjunto de competências. 

![Compreender um conjunto de competências](media/cognitive-search-tutorial-blob/skillset.png "Compreender um conjunto de competências")

As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Criar um índice

Nesta secção, pode definir o esquema de índice ao especificar os campos a incluir no índice pesquisável e os atributos de pesquisa para cada campo. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

Neste exercício, utiliza os seguintes campos e tipos de campo:

| nomes de campo: | `id`       | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| tipos de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Pedido de Amostra
Antes de realizar esta chamada REST, lembre-se de substituir o nome do serviço e a chave de administrador no pedido abaixo, caso a ferramenta não preserve o cabeçalho do pedido entre chamadas. 

Este pedido cria um índice. Utilize o nome do índice ```demoindex``` para o resto deste tutorial.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Sintaxe do Corpo do Pedido

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
Envie o pedido. A ferramenta de teste Web deve devolver um código de estado de 201 a confirmar o êxito. 

Para saber mais acerca da definição de um índice, veja [Create Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Criar Índice (API REST do Azure Search)).


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Criar um indexador, mapear campos e executar transformações

Até ao momento, criou uma origem de dados, um conjunto de competências e um índice. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. Para as associar num indexador, tem de definir os mapeamentos de campo. Os mapeamentos de campo fazem parte da definição do indexador e executam as transformações quando submete o pedido.

Para uma indexação não melhorada, a definição do indexador fornece uma secção *fieldMappings* opcional, caso os nomes dos campos ou os tipos de dados não coincidam precisamente ou caso pretenda utilizar uma função.

Para que as cargas de trabalho de pesquisa cognitiva tenham um pipeline de melhoramento, um indexador precisa de *outputFieldMappings*. Estes mapeamentos são utilizados quando um processo interno (pipeline de melhoramento) é a origem dos valores de campo. Os comportamentos exclusivos de *outputFieldMappings* incluem a capacidade de processar tipos complexos criados como parte do melhoramento (através da competência formulador). Além disso, podem existir vários elementos por documento (por exemplo, várias organizações num documento). A construção *outputFieldMappings* pode direcionar o sistema para “aplanar” coleções de elementos num único registo.

### <a name="sample-request"></a>Pedido de Amostra

Antes de realizar esta chamada REST, lembre-se de substituir o nome do serviço e a chave de administrador no pedido abaixo, caso a ferramenta não preserve o cabeçalho do pedido entre chamadas. 

Forneça também o nome do seu indexador. Pode referenciá-lo como ```demoindexer``` para o resto deste tutorial.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Sintaxe do Corpo do Pedido

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
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
```

Envie o pedido. A ferramenta de teste Web deve devolver um código de estado de 201 a confirmar um processamento concluído com êxito. 

A conclusão deste passo demora vários minutos. Apesar de o conjunto de dados ser pequeno, as competências analíticas realizam um processo de computação intensa. Algumas competências, como a análise de imagem, são de execução longa.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver problemas em atingir os dados, com o mapeamento de entradas e saídas ou com a ordem das operações, estes vão surgir nesta fase. Para executar novamente o pipeline com as alterações de código ou script, pode ter de remover primeiro os objetos. Para obter mais informações, veja [Repor e executar novamente](#reset).

### <a name="explore-the-request-body"></a>Explorar o corpo do pedido

O script define ```"maxFailedItems"``` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também a instrução ```"dataToExtract":"contentAndMetadata"``` nos parâmetros de configuração. Esta instrução informa o indexador para extrair automaticamente o conteúdo a partir de formatos de ficheiros diferentes, bem como de metadados relativos a cada ficheiro. 

Quando o conteúdo é extraído, pode definir ```imageAction``` para extrair texto das imagens existentes na origem de dados. O ```"imageAction":"generateNormalizedImages"``` configuração, combinada com a habilidade de OCR e a habilidade de unir de texto, informa o indexador para extrair texto de imagens (por exemplo, a palavra "stop" de um sinal de tráfego) e incorporá-lo como parte do campo conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

## <a name="check-indexer-status"></a>Verificar o estado do indexador

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se o indexador ainda está em execução, envie o seguinte pedido para verificar o estado do indexador.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

A resposta indica se o indexador está em execução. Depois de concluída a indexação, utilize outro HTTP GET para o ponto final ESTADO (conforme apresentado acima) para ver relatórios de quaisquer erros e avisos que ocorreram durante o enriquecimento.  

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, os ficheiros JPEG não têm entradas de texto). Pode rever a resposta do estado no que concerne a informações verbosas acerca dos avisos emitidos durante a indexação.
 
## <a name="verify-content"></a>Verificar o conteúdo

Depois de concluída a indexação, execute consultas que devolvam os conteúdos dos campos individuais. Por predefinição, o Azure Search devolve os 50 principais resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter as instruções, veja [Como paginar os resultados no Azure Search](search-pagination-page-layout.md).

Como passo de verificação, consulte o índice de todos os campos.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

O resultado é o esquema de índice, com o nome, o tipo e os atributos de cada campo.

Submeta uma segunda consulta para `"*"` devolver todos os conteúdos de um único campo, tal como `organizations`.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Repita a operação para os campos adicionais: conteúdo, idioma, expressões-chave e organizações neste exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

Pode utilizar GET ou POST, dependendo da complexidade da cadeia de consulta e do comprimento. Para obter mais informações, veja [Consultar através da API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="access-enriched-document"></a>

## <a name="accessing-the-enriched-document"></a>Aceder ao documento melhorado

A pesquisa cognitiva permite ver a estrutura do documento melhorado. Os documentos melhorados são estruturas temporárias criadas durante o melhoramento e, em seguida, eliminados quando o processo é concluído.

Para capturar um instantâneo do documento melhorado criado durante a indexação, adicione um campo chamado ```enriched``` ao índice. O indexador captura automaticamente para o campo uma representação da cadeia de todos os melhoramentos desse documento.

O campo ```enriched``` vai conter uma cadeia que é uma representação lógica do documento melhorado na memória no JSON.  Contudo, o valor do campo é um documento JSON válido. As aspas são carateres de escape, por isso, terá de substituir `\"` por `"` para ver o documento como JSON formatado.  

O campo ```enriched``` destina-se a fins de depuração, apenas para ajudá-lo a compreender a forma lógica do conteúdo a partir do qual as expressões estão a ser avaliadas. Pode ser também uma ferramenta útil para compreender e depurar o conjunto de competências.

Repita o exercício anterior, incluindo um campo `enriched` para capturar os conteúdos de um documento melhorado:

### <a name="request-body-syntax"></a>Sintaxe do Corpo do Pedido
```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento do pipeline, a abordagem mais prática para as iterações de design consiste em eliminar os objetos do Azure Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Para voltar a indexar os documentos com as novas definições:

1. Elimine o índice para remover os dados persistentes. Elimine o indexador para recriá-lo no seu serviço.
2. Modifique um conjunto de competências e a definição do índice.
3. Volte a criar um índice e o indexador no serviço para executar o pipeline. 

Pode utilizar o portal para eliminar índices, indexadores e conjuntos de competências.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

O código de estado 204 é devolvido após uma eliminação com êxito.

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

As [competências predefinidas](cognitive-search-predefined-skills.md) foram introduzidas, juntamente com a definição do conjunto de competências e os mecanismos de encadeamento de competências, através de entradas e saídas. Também aprendeu que precisa de `outputFieldMappings` na definição do indexador para encaminhar valores melhorados do pipeline para um índice pesquisável num serviço do Azure Search.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Nesta versão, não há um mecanismo para visualizar as construções internas (documentos melhorados criados pelo sistema). Também aprendeu como verificar o estado do indexador e quais os objetos a eliminar antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar no final do tutorial passa por eliminar o grupo de recursos que contém o serviço do Azure Search e o serviço Blob do Azure. Assumindo que coloca ambos os serviços no mesmo grupo, elimine o grupo de recursos agora para eliminar definitivamente todo o seu conteúdo, incluindo os serviços e quaisquer conteúdos armazenados criados para este tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos Seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu. 

> [!div class="nextstepaction"]
> [Exemplo: criar uma competência personalizada](cognitive-search-create-custom-skill-example.md)
