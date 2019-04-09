---
title: 'Tutorial: Chamar APIs serviços cognitivos num pipeline de indexação - Azure Search'
description: Percorrer um exemplo de extração de dados, de linguagem natural e de imagem IA processamento no Azure Search, indexação para extração de dados e transformação sobre os JSON blobs.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5fbcef1d8bc19df251a4d33cafa2fa7b5a7d9431
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261926"
---
# <a name="tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline-preview"></a>Tutorial: Chamar APIs serviços cognitivos num Azure Search, indexação do pipeline (pré-visualização)

Neste tutorial, vai aprender os mecanismos da programação do melhoramento de dados no Azure Search com as *competências cognitivas*. Habilidades são apoiadas por capacidades de análise de imagem nos serviços cognitivos e processamento de linguagem natural (NLP). Através do conjunto de capacidades composição e configuração, pode extrair representações de texto de uma imagem ou um ficheiro de documentos digitalizados e de texto. Também pode detetar idioma, entidades, expressões-chave e muito mais. O resultado final é o conteúdo mais avançado no índice da Azure Search, criado por um pipeline de indexação com tecnologia de IA. 

Neste tutorial, vai fazer chamadas à API REST para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplicar competências incorporadas: reconhecimento de entidades, deteção de idioma, manipulação de texto e extração de expressões-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

O resultado é um índice pesquisável de texto completo no Azure Search. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md).

Este tutorial é executado no serviço gratuito, mas o número de transações gratuitos está limitado a 20 documentos por dia. Se pretender executar este tutorial mais de uma vez no mesmo dia, utilize um ficheiro mais pequeno, definir, para que caibam em mais execuções.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência de processamento, adicionar mais documentos ou adicionar mais algoritmos de IA, terá de anexar um recurso dos serviços cognitivos faturável. Os encargos acumulam ao chamar APIs serviços cognitivos e para extração de imagem como parte da fase de aberturas de documentos no Azure Search. Não existem custos para extração de texto de documentos.
>
> Execução de habilidades internas é cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/) . Preços de extração de imagem é cobrado a preços de pré-visualização, conforme descrito no [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Saiba [mais](cognitive-search-attach-cognitive-services.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial.

[Aplicação de ambiente de trabalho postman](https://www.getpostman.com/) é utilizado para fazer chamadas REST para o Azure Search.

### <a name="get-an-azure-search-api-key-and-endpoint"></a>Obtenha uma chave de api do Azure Search e o ponto final

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. No portal do Azure, no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://my-service-name.search.windows.net`.

2. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

![Obter uma chave de acesso e de ponto final HTTP](media/search-fiddler/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Configurar o serviço Blob do Azure e carregar dados de exemplo

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Os dados de origem devem ter origem num tipo de origem de dados suportado de um [indexador do Azure Search](search-indexer-overview.md). Tenha em atenção que o armazenamento de tabelas do Azure não é suportado para a pesquisa cognitiva. Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. A [transferência de dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) é constituída por um pequeno conjunto de ficheiros de diferentes tipos. 

1. [Inscreva-se para o armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal), criar uma conta de armazenamento, abra as páginas de serviços de Blob e criar um contentor. Crie a conta de armazenamento na mesma região que o Azure Search.

1. O contentor que criou, clique em **carregar** para carregar os ficheiros de exemplo que transferiu no passo anterior.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

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

Repita esta operação para campos adicionais: conteúdo, languageCode, keyPhrases e organizações neste exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

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
> [Exemplo: criar uma habilidade personalizada](cognitive-search-create-custom-skill-example.md)
